title: "开发富文本编辑器的一些经验教训"
date: 2017-07-17 10:59:32
categories: 前端
tags: [Javscript, Vue, Rich text editor]
---

最近我们的产品有一个需求是要在PC端做一个面向用户的书评编辑器，让用户和编辑在蜗牛读书上能方便快捷的编辑和产出一些优质的文章，它的主要难点就是富文本编辑器部分。

<!--more-->

这虽然是个业务需求，但是做业务的同时也要兼顾技术，所以在跟需求商量好不支持IE8之后，决定采用`Vue`来作为前端部分的技术架构。

## 前端架构

### webpack配置
`Vue`是一个非常优秀的前端MVVM框架，轻量、快速、文档友好又详细，代码组织也非常优雅，是我比较偏爱的MVVM架构。`Vue`官方提供了非常方便快速上手的脚手架`Vue-cli`，但是由于跟我们这边使用的Java Web架构有一些不太适合的地方，所以我并没有使用它，不过我也是对`Vue-cli`做了一番详细的学习后来搭建自己的`webpack`配置。

下面是我的生产环境的部分webpack配置，其实并不复杂，因为我的业务场景也并不复杂，现在的各种插件功能也足够强大。

> webpack.prod.config.js

```javascript
devtool: 'source-map',
plugins: [
    new CleanWebpackPlugin(['dist']),
    new ExtractTextPlugin('[name].css'),
    new webpack.DefinePlugin({
        'process.env': {
            NODE_ENV: '"production"'
        }
    }),
    new webpack.optimize.CommonsChunkPlugin({
        name: 'vendor',
        minChunks: function(module, count) {
            return (
                module.resource &&
                /\.js$/.test(module.resource) &&
                module.resource.indexOf('node_modules') >= 0
            )
        }
    }),
    new webpack.optimize.CommonsChunkPlugin({
        name: 'manifest',
        filename: 'manifest.js',
        chunks: ['vendor']
    }),
    new webpack.optimize.UglifyJsPlugin({
        sourceMap: true,
        compress: {
            warnings: false
        }
    }),
]
```
主要就是借鉴了`Vue-cli`中的code split思路，开发环境的webpack配置区别不大，只是sourcmap设置改为了`devtool: '#cheap-module-eval-source-map'`,去掉了代码压缩等。

需要注意的一点是，我在生成环境下的webpack配置中使用了`vue-loader`附带的`postcss`预处理器中的`cssnano`插件进行css部分的代码压缩，但是这个插件打包时会将`z-index:10`压缩成`z-index:1`，需要添加设置`zindex: false`才能避免这个问题,而且`cssnano`插件默认还有一个特性就是会删除没有使用到的css部分，比如我们为CSS3动画所需构建的`keyframes`，居然也会被`cssnano`认为是没有被使用的css，压缩过程中也删掉了，这个就有点费解了，所以为了避免这种情况，我们需要增加设置`discardUnused: false`：
```javascript
rules: [{
    test: /\.vue$/,
    loader: 'vue-loader',
    options: {
        loaders: {
            css: ExtractTextPlugin.extract({
                use: 'css-loader',
                fallback: 'vue-style-loader'
            }),
            scss: ExtractTextPlugin.extract({
                use: ['css-loader','sass-loader'],
                fallback: 'vue-style-loader'
            })
        },
        postcss: [
            require('autoprefixer')({
                browsers: ['> 1%']
            }),
            require('cssnano')({
                zindex: false,
                discardUnused: false
            })
        ], 
    }
}]
```

### 与Java Web的结合
为了将css文件抽离出来，我在开发环境也没有使用**Hot Module Reload**机制(使用了ExtractTextPlugin抽离css文件后，修改css样式不能通过**HMR**自动更新，需手动刷新)。

我们部门这边的Java Web除了一些简单的静态活动页，主要页面的承载页都会配置在另外的一个存放`freeMarker`的ftl文件的文件夹中，有别于静态文件的存放位置，这是部门中的Java Web一直沿用的文件结构，不好也没太大必要去改变它。

这就使得`Vue-cli`或者一些常见的webpack配置中的根据文件hash生成打包文件再使用`html-webpack-plugin`自动注入承载页的功能不太好实现，所以就需要结合部门自己的情况定制比较符合自己项目的打包流程。

我们有个网站应用自动部署平台，它的功能除了解析和编译后端工程代码，还会自动分析页面引用的静态资源，然后将资源的URL替换为对应的CDN域名的下的资源链接并添加资源MD5值相关的查询值后缀，比如`/static/js/app.js`会在自动部署后变成`//yuedust.yuedu.126.net/snail_st/static/js/app.js?a63ed8a8`。

所以既然目前项目中已经有了CDN域名替换和文件hash计算的功能，我在webpack打包中就没必要再多此一举了，而且，我还可以利用这一特性，固定的设置承载页引用的静态资源的URL，部分代码如下：
```html
<!doctype html>
<html lang="zh-cmn-Hans">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
    <meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no">
    <link rel="shortcut icon" href="/static/images/favicon.ico" />
    <title>蜗牛阅读-书评编辑</title>   
    <link rel="stylesheet" href="/static/bookreview/dist/app.css">
</head>
<body>
    <input type="hidden" id="csrfToken" name="csrfToken" value="${csrfToken!?html}" />
    <div id="app"></div>    
    <script src="/static/bookreview/dist/manifest.js"></script>
    <script src="/static/bookreview/dist/vendor.js"></script>
    <script src="/static/bookreview/dist/app.js"></script>
</body>
</html>
```
这样设置好后无论本地开发还是部署线上都不需要再修改ftl文件的内容了，既有效的利用到了**Code Split**加快打包速度和缓存利用率高的优点，也使得开发和部署变得简单，页面引用的静态资源一旦添加，就不需要再去更改路径了。

**当然，这只是结合自己项目的Java Web工程结构和特点设置的一套webpack使用方式，仅供参考**

## 开发富文本编辑器的教训
由于项目的时间较紧张，我在页面上应用了`Vue`框架的背景下，想当然的想要把`Vue`也应用于富文本编辑器的开发，事实证明这是不太可行的。

### 富文本中的数据渲染
`Vue`是数据和展现双向绑定的，这使得特定格式的数据渲染成对应的html非常的方便。

但是网页上的富文本编辑器普遍都是利用的是元素的`contenteditable`属性，这个属性是无法实现双向绑定的，要想实时保存富文本数据，只能监控元素的输入事件，然后读取元素的`innerText`后再去修改数据，但是一旦修改了数据，就会触发`Vue`的视图更新，导致你编辑元素的`innerText`被重新渲染，元素一旦被重新渲染，用户输入时的获取的光标焦点就消失了，而且在**windows**和**mac os**下的输入法实现有些不一样,mac下的输入法输入中文会先将用户输入的拼写填充到输入元素中，导致获取的innerText不准确，所以想要利用`Vue`的数据双向绑定机制来开发富文本部分，又想要实现数据的实时保存，存在很多问题。

### 富文本中的不可编辑区域
我们的书评内容的数据结构是一个各种**item**类型组成数组，**item**的类型有：文字、图片、书籍和笔记，富文本编辑器需要将这些数据展现出来并且可编辑，其中书籍和笔记的数据结构只能添加或者删除，而不能修改，这就与传统的富文本编辑器存在一定的区别，即富文本编辑器区域需要插入或者删除不能修改的元素。这个需求使得一个普通的富文本编辑器变得特殊起来，一开始我的思路是在`contenteditable="true"`的编辑器主体内插入`contenteditable="false"`的dom结构，这导致插入部分的文本无法与编辑器很好的交互，包括删除、撤销、选中等，最后找到了另外一种比较理想的解决办法。

## 开发富文本编辑器的一些经验
以下是我在开发一个本业务场景下的富文本编辑器的一些经验：
### 在开源富文本编辑器的基础上开发
知乎上有个问题，叫做[为什么都说富文本编辑器是天坑？](https://www.zhihu.com/question/38699645)，里面提到的很多开发富文本编辑器会遇到的一些难点，而我的第一版也是想着自己从头开始开发，但是的确碰到了很多没想到的问题，修修补补最终结果还是不满意。

所以如果是需要一个常规功能的富文本编辑器，尽量选择成熟稳定的开源项目，保证稳定可靠，如果需要像我一样开发一个符合特定业务场景的富文本编辑器，也尽量在开源项目的基础上进行二次开发，这样虽然会有一些代码冗余，但是能帮助你避开许多前人已经踩过的坑，而且也能从阅读这些项目的源码中学习到不少忽视的知识和特性。

我选择的是国内的一个个人开发者维护的叫做[wangEditor](http://www.wangeditor.com/)的项目，它比较轻量，源码也比较清晰便于二次开发。
### 基于DOM的数据渲染
要想在WEB端实现富文本编辑，经过我踩的一些坑，我觉得最终还是要回归于**DOM**的，`Vue`或者其他MVVM框架确实给开发和维护带来很大的遍历，但是在富文本编辑这块，还是没有DOM API来的可控。我的方案是根据服务端提供的一篇书评的`items`，组织出相应的HTML，然后再交给富文本编辑器进行初始化。

### 基于浏览器的`document.execCommand` API进行开发
当一个HTML文档处于设计模式(`designMode`)或者一个HTML元素设置了`contentEditable="true"`时，我们可以使用`execCommand`方法，运行一些命令来操纵可编辑区域的内容，这个API可以快速可靠的对富文本区域的选区内容进行一系列的操作，最关键是，支持**撤销**和**重做**功能，并且在撤销和重做的过程中能够完美的保持选区的状态，这一点非常重要，我们可以通过保存html来实现内容的撤销和重做，但是选区或者说光标的撤销和重做，用Javascript很难完美的控制,如果只是保存之前选区的`range`对象，是不能复原选区或者光标的。

具体支持的API可以参考[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/execCommand)的文档。

即使对于一些文档中不支持的API，也建议通过以上API来组合实现，比如一段HTML内容的替换，应该先通过Javascript建立相应的选区，然后运行`delete`命令删除该段内容，再通过`insertHTML`来插入所需的HTML，这样才能充分的利用浏览器的撤销和重做功能，并且与其他的操作串联起来。

### 富文本中的换行
富文本编辑器中的换行是一个值得注意的问题，我在开发书评编辑器的时候，遇到了一些问题：

富文本中展示换行看起来很容易，有几个方案，比如设置`CSS`的`white-space`再配合换行符，或者在`DOM`中添加`<br>`元素，看起来都能达到目的。但是书评编辑器特殊的地方在于，这是一个**已经制定好了数据结构**并且在客户端上也有编辑器，这就涉及到**Web**、**iOS**、**Andorid**三个端的一致性问题。
- 因为在客户端上是没有`<br>`概念的,客户端编辑器上需要换行位置插入的都是回车符，也就是`\n`，而这些换行符在WEB上如果需要显示成换行，就需要设置`white-space`为`pre`或者`pre-line`
- 如果设置为`white-space: pre;`，确实可以原样显示文本换行，但是如果是这样一条数据：
![](http://ww1.sinaimg.cn/large/7d25d640gy1fimrkn494kj20ka00ua9w.jpg)
这是书评中的一条文本数据，其中有两个换行符，代表要展示成三行，其中有一个空行，实际需要展示的效果是下图这样的：
![](http://ww1.sinaimg.cn/large/7d25d640gy1fimrtbjiupj20hu03f0sl.jpg)
这样的数据如果要展示在一个DOM节点中，设置为`white-space: pre;`,换行虽然保留了，但是由于第一行数据是连续的，`white-space: pre;`原样保持了数据的换行，导致了第一行超出了DOM的最大宽度，这样的方式显然就行不通了。
![](http://ww1.sinaimg.cn/large/7d25d640gy1fimrpfkex4j20j903f3yc.jpg)
- 如果设置成`white-space: pre-line`，`pre-line`可以在正确显示换行符的同时让超出一行的文字自动换到下一行，看起来很完美。但是，一旦在换行符之后（比如中间空的那行）输入文字，问题又出现了，在`white-space: pre-line`的元素中，如果在换行符之后输入文字，换行符会被删除，文字将会跳动到上一行继续显示，这样显然是不行的。
- 最终的方案只有剩插入`<br>`元素来实现换行了，通过`<br>`实现的换行，不会出现输入文字换行失效的问题，也不需要父元素设置`white-space: pre;`，所以我们需要将客户端在文本中插入的`\n`转换成`<br>`，最后把HTML结构重新解析成书评数据的时候，又需要将它们转换回来以便保证客户端编辑和展示的一致性，当然这中间还有一系列的转换逻辑,包括针对客户端老版本的编辑器的一些BUG做的兼容，最后为了实现一致还是废了一番功夫的。

### 富文本中的不可编辑区域
![](http://ww1.sinaimg.cn/large/7d25d640gy1fhmxzlovulj20ge03et8x.jpg)
![](http://ww1.sinaimg.cn/large/7d25d640gy1fhmy0aqo5rj20ge03cjrg.jpg)
如上面两图，我们的书评中有一部分内容是用户引用的某一本书籍、或是用户在阅读时记录的书籍原文，这些数据结构都是不能被修改的，只能插入或者删除，一开始我的思路是把该部分DOM结构设置为`contenteditable="false"`,但是这样的设置代码上不管怎么去弥补体验上都不够好。

后来我转变了思路，既然这就是一段不可编辑只能观看的DOM，而富文本编辑器里插入的图片是能够很好的与文字一起被很好的操作和维护的，那么为什么不把不可编辑的展示区域直接转换为图片插入到富文本区域呢，事实证明这个思路最后的体验非常好，除了一个小的技术问题，下面一点会说明。

### 将DOM转换为图片
要将一个DOM转化为图片，社区里已经有不少很成熟的开源库可以使用，比如我使用的是[dom-to-image](https://github.com/tsayen/dom-to-image)，需要注意的就是一个问题：DOM转化为图片，基本都利用到了`canvas`的`toDataUrl()`功能将图片转化转化为base64编码的URL，这里面有一个安全策略，就是如果`canvas`中绘制的DOM结构中有图片，而该图片与当前页面的域名不一样（这在我们的开发场景中很常见），出于安全策略的限制，此时浏览器是不允许调用`canvas`的`toDataUrl()`方法的，而我们的书籍卡片中必定会有书籍的封面，该封面的域名是我们的CDN域名，所以转换成图片被限制了。
![](http://ww1.sinaimg.cn/large/7d25d640gy1fhmzhxp1y4j20n403rq3h.jpg)
要想解决这个办法，就涉及到一个前端的`IMG`标签的属性：`crossOrigin`,如果将这个属性设置为`anonymous`,浏览器就会为这张图片的请求的`Request Headers` 中附带`Origin`为当前域名的这一行信息，告诉图片所在的静态资源服务器，这张图片我需要跨域访问以及我的域名，请在图片的`Response Headers`中附加`Access-Control-Allow-Methods`和`Access-Control-Allow-Origin`这两行信息,如下图：
![](http://ww1.sinaimg.cn/large/7d25d640gy1fhmywepnduj20gx0d7q46.jpg)
这样请求得到的图片渲染到`canvas`中，浏览器才不会限制该`canvas`转化为base64的URL。

这一特性需要服务端的支持，有的服务端就算附加了这个`Request Headers`字段依然不会返回想要的`Response`。

但是在支持这一特性的服务端，有时候设置了`crossOrigin="anonymous"`依然显示这个错误，不是这个属性没生效，而是我们的图片一般是存放在CDN上的，而CDN为了更快的返回用户的请求，会把图片的响应缓存下来，而这些缓存下来的响应显然是没有`Access-Control-Allow-Methods`和`Access-Control-Allow-Origin`这两行信息的，所以这时候即使我们认为自己的请求包含了`crossOrigin="anonymous"`，CDN服务器不认为这是一个不同的请求，所以返回给我们的响应是之前就缓存好的，导致了这个问题的发生。

这种情况就需要我们为我们请求的图片URL后添加一个时间戳来避免CDN服务器的缓存。

### 避免使用CDN来提高渲染速度
前端开发中说到提高页面的加载速度，一般都会提到最大限度的利用CDN缓存静态资源，以提高静态资源的访问速度，从而更快的将网页内容呈现给用户。

但是，我上面提到的将含有**跨域CDN图片**的`DOM`节点渲染成图片的情况下，向**CDN代理节点**请求图片资源反而会比我们直接向**静态资源源站点**请求要来的慢，其实这也很好理解：

- 为了将含有跨域CDN图片的`DOM`利用`HTML5``canvas`API渲染成图片，我们就需要为该图片的添加`crossOrigin="anonymous"`属性，并且为图片的请求URL添加一个时间戳
- 如果我们访问的是CDN域名下的图片，同时又为URL添加了一个全新的时间戳，那么这个图片资源的请求对于CDN代理节点来说肯定是全新的，也就是会认为本节点上没有这个资源的缓存
- CDN代理节点遇到一个自己没有缓存的资源，它就会向静态资源的源站点去请求，得到结果后再转发给用户，这等于说我们这个带有时间戳的图片URL的请求，不但没能利用的CDN的缓存提速，反而由CDN代理节点充当了一次中介，这显然会增加资源的返回耗时
![](http://ww1.sinaimg.cn/large/7d25d640gy1fi35ks1pm0j20kk0263ys.jpg)
![](http://ww1.sinaimg.cn/large/7d25d640gy1fi35kw8wc0j20kl02eq38.jpg)
上面两图分别就是请求CDN域名图片的耗时和请求源站点图片的耗时，经过多次测试，可以发现请求CDN域名图片的耗时基本在200ms以上，而向源站点的请求基本都在100ms以下，所以，有的时候，比如这种特殊情况下，请求CDN域名下的资源可能反而会增加请求的耗时。

### Promise大法好
根据上面提到的流程，需要我把从服务端拿到的一个包含各种类型**item**的数组解析成一个HTML字符串，其中包含了书籍和笔记类型的**item**需要转化成的base64格式的图片，这就出现了时序上的问题：

文本和图片类型的**item**，可以直接得到对应的HTML字符串，而书籍和笔记类型的**item**，则需要通过网络请求和canvas转换，但是最终我又需要得到整个的初始HTML内容来初始化富文本编辑器，然后再让用户可以去在这些HTML DOM节点上进行编辑，这就需要用到`Promise.all`这个API了，代码示例如下：
```javascript
/**
 * 将服务端返回的书评items转换为html string传输给富文本编辑器
 * @param  {json array} items 书评items
 * @return {promise}       所有items处理好后返回resolve(htmlStr), 否则reject(error)
 */
convertItemsToHtml(items){
    return new Promise ( (resolve, reject) => {
        let htmlStr = '';
        let itemStr = '';
        let itemPromises = items.map( item => {
            return new Promise( (resolve, reject) => {
                switch(item.resourceType){
                    case 'Text':
                        itemStr =  `<p data-type="Text">${item.text}</p>`;
                        resolve(itemStr);
                        break;
                    ...
                    case 'BookNote':
                        let $BookNoteEle = $(`<div class="editor__note image__render">${item.bookNote.markText}</div>`).appendTo($('body'));
                        domtoimage.toPng($BookNoteEle[0], {style: {opacity: 1, zIndex: 1}})
                            .then(function (dataUrl) {
                                itemStr =  `<p data-type="BookNote"><img data-type="BookNote" data-item='${escape(JSON.stringify(item))}' class="editor__img" src="${dataUrl}"></p>`;
                                $BookNoteEle.remove();
                                resolve(itemStr);
                            })
                            .catch(function (error) {
                                console.error('图片生成失败', error);
                                reject(error);
                            });
                        break;
                }
            })
        })
        Promise.all(itemPromises).then( ([...itemStrs]) => {
            htmlStr = itemStrs.reduce( (acc, val) => {
                return acc + val
            }, '');
            resolve(htmlStr);
        }).catch( (error) => {
            reject(error);
        })
    })
},
```
利用`Promise.all`和其他一些ES6的特性，可以使我们的代码变得更加强大而简洁。


以上就是我在开发特定业务需求的富文本编辑器中遇到的一些问题和总结的一些经验，可能会有一些错误，希望帮忙指正。
其他一些常见的富文本编辑中会遇到的问题，可以通过学习一些开源的成熟富文本编辑器项目来得到解答。