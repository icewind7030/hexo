title: webpack常用配置详解
date: 2017-02-14 16:44:25
tags: [webpack, config]
categories: 前端
---

使用webpack也有一段时间了，经常是照搬别人的设置，并不知道为什么要这么做，所以决定结合自己在使用webpack中学习到的经验和遇到的问题进行一下总结。本文并没有涵盖所有的webpack配置。

<!--more-->

## Webpack CLI
`CLI`就是`command line interface`的缩写,就是在命令行模式下使用webpack，最简单的用法就是
```shell
webpack <entry> <output>
```
虽然还可以加入一些基础的设置，但是这种用法远远达不到我们的要求，远不如通过一个config文件来控制显得直观和可控，但是CLI模式中有许多参数我们还是配合config文件来使用的，比如
- `-d`或者`-p`可以快速应用webpack debug模式或者production模式所需的一些常用设置，比如sourcemap,代码压缩等
- `--watch`可以设置webpack监控entry中的文件改动，自动重新编译
- `--progress`可以设置webpack在编译时显示编译的百分比进度
- `--colors` 输出结果带彩色，比如：会用红色显示耗时较长的步骤
- `--profile` 输出性能数据，可以看到每一步的耗时
- `--display-modules` 默认情况下 node_modules 下的模块会被隐藏，加上这个参数可以显示这些被隐藏的模块

## Webpack Configuration
webpack主要的使用方式还是通过文件来配置，默认的配置文件名为**webpack.config.js**,可以通过`webpack --config example.config.js`来设置成其他文件名。

### entry和output
entry就是webpack编译打包功能的入口配置，它可以是字符串、数组或者一个对象,output就是webpack的最终打包的输出文件
- `entry`如果为字符串就代表一个入口文件，数组代表多个入口文件，这两种情况如果没有特殊配置，最终都会打包成一个文件，即output指定的文件名；
- `entry`如果为对象一般都会指定2个以上的多个入口文件，比如：

    ```javascript
    entry: {
        page1: './page1',
        page2: ['./entry1', './entry2']
    },
    ```
    这种情况下，output必须是按照entry指定的输出文件名，比如:
    ```javascript
    output: {
        filename: '[name].bundle.js',
        chunkFilename: '[id].bundle.js'
    }
    ```
    其中`[name]`代表entry设置中对应的键名`page1`、`page2`,`[id]`代表webpack打包中自动给各个模块所设置的模块id；
    
- `output`中`filename`就是与entry对应的输出文件，而`chunkFilename`指的是一些并未在entry中指定输出文件名的输出文件，比如通过CommonJS的require.ensure方法异步加载的文件模块（下文会介绍）；

- `output`的`publicPath`就是css中引用的图片、字体或其他文件路径经过webpack打包后得到的新路径，或者是图片上传后的cdn地址，
    比如在CSS中有以下图片引用：
    ```css
    div{
        background-image: url(../images/picA.jpg);
    }
    ```
    而output中`publicPath`设置为：
    ```javascript
    output: {
        path: './dist',
        publicPath: './dist/images'
    },
    ```
    再配合webpack中处理图片的loade配置：
    ```javascript
    {
        test: /\.(png|jpg|GIF)$/,
        loader: 'file-loader?name=/dist/images/[name].[ext]'
    }
    ```
    最终打包得到的css中图片的地址就是我们想要的位置，即：
    ```css
    div{
        background-image: url(./dist/images/picA.jpg);
    }
    ```
    
### resolve
`resolve`是用来配置webpack如何解析模块路径的，这里只介绍一些常用的配置
#### alias
`resolve.alias`就是使用别名来处理模块，就是把用户对某个模块的请求重定向到alias指定的路径，比如：
```javascript
entry: 'entry.js',
output: {
    filename: 'bundle.js'
},
resolve: {
    alias: {
        moment: "moment/min/moment-with-locales.min.js"
    }
}
```
这样待打包的`entry.js`中的`require('moment');` 其实就等价于 `require('moment/min/moment-with-locales.min.js');`

这样做的好处是如果待打包的JS中引用了`moment`这个模块，webpack就不需要去解析`node_modules`中引入的moment包的整个内容，因为这个包中含有大量的涉及时间本地化的一些逻辑文件，这些文件其实都是不需要webpack去关心的，解析这些文件会拖慢我们的打包速度；而使用了别名后，打包`moment`这个模块的时间将会缩短一半以上。

#### root
`resolve.root`指定的是并未存放在`node_modules`文件夹下的自定义模块的路径
#### fallback
`resolve.fallback`是模块在root及默认路径下都未找到时的最终查找路径

### modules
`modules`就是配置webpack如何使用各种loaders来解析各种类型的模块的，这部分还是比较简单直观的，主要介绍一些其他经验
#### preLoaders和postLoaders
webpack使用loaders解析模块时，分别有个3个状态，分别是`preloaders,loaders,postloaders`,分别代表前中后三个处理状态，这个不是很常用，官方的建议是：
1. 对于预编译的javascript（如coffee，es6等）和一些非js文件，在preloaders里面处理，比如图片的压缩，或者使用JSHint, 对JS代码进行检查；
2. 对于javascript的loader建议在loader或preloader里面进行处理；
3. 建议preloader 和 postloader 的过程中，使用相同的语言。

#### noParse
`noParse`是webpack中一个比较有用的配置项，如果你确定一个模块中没有其它新的依赖 就可以配置这项，webpack 将不再扫描这个文件中的依赖：
```javascript
module: {
    noParse: ['/moment-with-locales/']
}
```
这样设置`noParse`后，再结合上面的alias配置，webapck的解析`moment`流程为：
1. webpack 检查到 `entry.js` 文件对 `moment` 的请求；
2. 请求被 `alias` 重定向，转而请求 `moment/min/moment-with-locales.min.js`；
3. `noParse` 规则中的 `/moment-with-locales/` 一条生效，所以 webpack 就直接把依赖打包进了 `bundle.js` 。

这样又进一步减少了webpack的打包时间，因为webpack甚至不需要知道moment中的内容。

### externals
`externals`指明了那些模块不需要被webpack打包进最后的bundle，而是通过单独的请求来获取，这样就可以配合一些已有的CDN服务来提高访问的速度，还可以提高打包的速度，减小打包文件的体积。比如还是上文的moment模块，就可以使用`externals`将它声明为一个外部依赖：
```javascript
externals: {
    moment: true
}
```
然后在html页面中手动引入或者借助一些插件引入:
```html
<script src="//apps.bdimg.com/libs/moment/2.8.3/moment-with-locales.min.js"></script>
```
这样又可以进一步减少webpack打包时间。

### devtool
webpack的`devtool`用于在对打包后的文件进行调试时方便进行错误定位，它有7种模式，而且模式之间还能任意组合。
- `eval`  每个模块被转化为字符串，在尾部添加`//# souceURL`（指明eval前文件）后，被eval包裹起来
- `source-map`  最原始的source-map实现方式，打包代码的同时生成一个sourcemap文件，并在打包文件的末尾添加`//# sourceMappingURL`，该注释会告诉JS引擎sourcemap文件的位置
- `hidden-source-map`  打包结果与`source-map`一致，但是打包文件结尾不显示`//# sourceMappingURL`,JS引擎依靠打包的文件名去查找相应sourcemap文件
- `inline-source-map`  直接打包文件的结尾追加包含打包前的每个文件的完整sourcemap信息的`//# sourceMappingURL`， 该信息为Base64编码的字符串
- `eval-source-map`  将每个模块转化为字符串，使用eval包裹，并将打包前每个模块的sourcemap信息转换为Base64编码，拼接在每个打包后模块的结尾
- `cheap-source-map` 与`source-map`模式原理相同，但是不包含源文件的列信息，也不包含 loader 的 sourcemap，（譬如 babel 的 sourcemap）
- `cheap-module-source-map` 不包含列信息，同时 loader 的 sourcemap 也被简化为只包含对应行的，在打包文件的末尾通过`//# sourceMappingURL`标识对应的sourcemap文件位置

这么多的模式，不可能都使用，所以，开发环境下，推荐使用`cheap-module-eval-source-map`,这是`cheap-module-source-map`和`eval`模式的组合，也是webpack使用`-d`命令下的默认模式；而生产模式下，推荐使用`cheap-module-source-map`模式。
- 大部分情况我们调试并不关心列信息，而且就算 sourcemap 没有列，有些浏览器引擎（例如 v8） 也会给出列信息，所以我们使用 cheap 模式可以大幅提高 sourcemap 生成的效率。
- 使用 module 可支持 babel 这种预编译工具（在 webpack 里做为 loader 使用）。
- 使用 eval 方式可大幅提高持续构建效率，参考webapck devtool速度对比列表，这对经常需要边改边调的前端开发而言非常重要
![](http://ww1.sinaimg.cn/large/7d25d640gy1fd08zfturej212509d0tc)

### plugins
最后介绍一些比较实用的webpack插件，它们的功能非常全面。
#### extract-text-webpack-plugin
这是个很常用的插件，用途就是把打包在js中的css提取出来，成为一个单独的css文件，用法为:
```javascript
var ExtractTextPlugin = require("extract-text-webpack-plugin");
module.exports = {
    module: {
        loaders: [
            { test: /\.css$/, loader: ExtractTextPlugin.extract("style-loader", "css-loader") }
        ]
    },
    plugins: [
        new ExtractTextPlugin("styles.css")
    ]
}
```
针对某些或全部css文件的loader使用，loader对应值的写法为：`ExtractTextPlugin.extract([notExtractLoader], loader, [options])`,需要注意的是`options`中`publicPath`这个配置，可以覆盖webpack的`output`中设置的`publicPath`的，但是只针对这一个loader对应的文件。

#### HotModuleReplacementPlugin
这是webpack内置的plugin，用作页面模块的热替换。就是在webpack运行了一次打包后不会直接结束，而是继续监视着入口资源文件，一旦他们发生改动，可以通过页面与服务端建立的`WebSocket`连接立即通知页面进行模块的热替换，而不需要自己手动或自动刷新整个页面。
要使用这个插件，`webpack.config.js`需要进行如下设置：
```javascript
plugins:[
    new webpack.HotModuleReplacementPlugin(),
]
```
然后需要借助一个Node脚本`server.js`对`webpack-dev-server`进行如下设置：
```javascript
var webpack = require('webpack');
var webpackDevServer = require('webpack-dev-server');
var config = require("./webpack.config.js");
config.entry.app.unshift("webpack-dev-server/client?http://localhost:8090/", "webpack/hot/dev-server");
var compiler = webpack(config);
var server = new webpackDevServer(compiler, {
    hot: true,
    historyApiFallback: true,
    noInfo: true,
    stats: { colors: true }
});
server.listen(8090);

```
它的原理就是给webpack的entry注入热替换的代码模块，然后通过`webpack-dev-server`这个小型的`Node Express`服务器对生成的文件提供访问。
最后，通过运行`node server.js`,页面就可以通过`localhost:8090`进行访问并且具有热替换功能了。

附上一张代码热替换的`console`信息图:
![](http://ww1.sinaimg.cn/large/7d25d640gy1fdfhf1mjaej20in058glw)

其实以上这些配置，其实完全可以通过一行`Webpack-dev-server CLI`命令来实现，就是在安装了`webpack-dev-server`的前提下，运行`webpack-dev-server --inline --hot`,它会自动进行所需要的代码注入。
我之所以更愿意选择用Node.js API的方式来运行，除了这样的配置更加直观可控外，还可以配合`nodemon`插件，实现前后端全部自动刷新：直需要安装`nodemon`插件，然后把`node server.js`改为`nodemon server.js`就可以了。

#### CommonsChunkPlugin
`CommonsChunkPlugin`也是webpack内置的plugin，它的作用是可以将webpack入口文件中的共用模块提取出来单独生成一个文件，这样的话，再配合浏览器的缓存，这些共用的文件就只需要第一次访问服务器进行获取，之后的访问就可以从缓存来获取来访问的速度，而不是每次都重新从一个较大的js文件来获取。
这里用`Vue`官方提供的脚手架中的`webpack CommonsChunkPlugin`配置来进行分析，这应该是比较规范的公共包的提取方案。
```javascript
entry: {
    app: ['./src/main.js'],
},
output: {
    path: path.resolve(__dirname, 'dist'),
    publicPath: './',
    filename: '[name].[chunkhash:7].js', //使用模块名加hash的方式，既便于理解和管理，也便于CDN缓存的利用和更新
    chunkFilename: '[id].[chunkhash:7].js'//对应于未指定模块名的模块生成的文件，稍后会介绍
},
plugins: [
    // 提取node_modules中的代码成vendor模块，这些模块一般很少变动
    // 提取出来以后可以充分利用webpack的缓存，加快二次打包的速度，还可以结合浏览器的缓存加快页面的加载速度
    new webpack.optimize.CommonsChunkPlugin({
        name: 'vendor',
        minChunks: function(module, count) {
            // any required modules inside node_modules are extracted to vendor
            return (
                module.resource &&
                /\.js$/.test(module.resource) &&
                module.resource.indexOf(
                    path.join(__dirname, './node_modules')
                ) === 0
            )
        }
    }),
    // 将上面代码打包的vendor模块中的共用模块打包成manifest模块
    // 这里面主要包括的是webpack模块机制的底层代码(`webpack bootstrap logic`)和底层代码构建好后需要启动的模块的清单(`module manifest`)
    // 在使用webpack给生成的模块添加chunkhash后，随着业务代码的改变，业务代码的hash也随之改变
    // 本模块引用的模块清单也发生了改变，从而本模块的hash也会改变，但是却很大几率能使得由node_modules打包的vendor模块的hash无需改变
    // 从而更有效率的利用好浏览器的缓存，提高加载速度，节省用来流量等
    new webpack.optimize.CommonsChunkPlugin({
        name: 'manifest',
        filename: '[name].[hash:7].js',
        chunks: ['vendor']
    }),
]
```
这样配置的原因参考代码中的注释，其中`manifest`模块需要单独设置`filename`为`'[name].[hash:7].js'`，否则将会在打包过程中报错。
通过上述设置实现的打包hash变化过程如下：
![](http://ww1.sinaimg.cn/large/7d25d640gy1fdfndhz8w5j20hw0l0wf8)
我在这两次打包之间只是修改了一个Vue组件的标题文字，可以看到，通过上述设置，只改变了`manifest`和`app`两个模块的hash，而存放`node_modules`模块的`vendor`模块hash并为改变，从而使得用户访问改动后的页面的时候可以更大限度的利用缓存来提高访问速度。

#### HtmlWebpackPlugin
上面介绍的打包出的带有hash的js文件，如果手动去引入，那就太繁琐了，所以需要借助`HtmlWebpackPlugin`来自动注入打包出的脚本，常用配置如下：
```javascript
plugins: [
    new HtmlWebpackPlugin({
        filename: 'index.html',//生成的html的文件名，可以带有子目录信息，默认就是index.html
        template: 'index.html',//生成的html所参照的模板文件的位置，模板中可以准写入一些需要预置的信息,比如要为Vue或者React准备的mount的节点，就需要先在模板中写入
        inject: true,//为true时，会自动把css文件写入head中(如果有用ExtractTextPlugin提取的话),把js文件写入到body最后
        chunksSortMode: 'dependency',//设置为dependency可以才能使得上面提到的CommonsChunkPlugin生成的3个js文件按照依赖的顺序依次写入html中
    })
]
```

## Webpack异步加载模块
最后介绍一下webpack支持的异步按需加载模块机制。我们提供给用户的网页，如果想要在用户触发某些操作的时候，才去通过网络加载所需的模块，这样既可以加快用户首次页面加载的速度，也可以节省用户的流量。
webpack是同时支持`CommonJs`和`AMD`模块化规范的，所以，有两种异步加载模块的写法：
```javascript
function btnClickHandler(){
    /**
     * CommonJs异步加载方案
     */
    require.ensure([], () => {
        var utils = require('./js/utils').default;
        self.msg = utils;
    });


    /**
     * AMD 异步加载方案
     */
    require(['./js/utils'], utils => {
        self.msg = utils.default;
    })
}
```
比如放在一个按钮的点击事件的处理函数中，只有当用户点击该按钮，才会去下载这个`./js/utils`模块。而webpack在打包成，会自动识别这种写法的代码，把它们识别为需要按需异步加载的模块，单独进行打包，这种按需加载的文件，就是通过上文中webpack配置中`output.chunkFilename`来指定文件名的格式。
当然，也可以通过`require.ensure()`方法的第三个参数来指定这个chunk的名称，在`output.chunkFilename`中就可以使用`'[name].[chunkhash:7].js'`来指定这些异步模块包的文件名了。
异步加载文件的过程展示如下：
![](http://ww1.sinaimg.cn/large/7d25d640gy1fdfn5h0rdjj20ly06rgm3)

## Webpack 2
webpack2在前段时间发布了，相对于1.X版本有了一些改变和优化，这里简单介绍一些常用的吧。
### resolve.root, resolve.fallback, resolve.modulesDirectories
这三项被合并成了一个配置项：`resolve.modules`,我之前就觉得那三项的配置有点混乱，webpack2也为此做了精简:
```javascript
resolve: {
    modules: [
        path.join(__dirname, "src"),
        "node_modules"
    ]
}
```
### modules.preLoaders, modules.loaders, modules.afterLoaders
这三项也被精简合并成了一项：`modules.rules`,而且不再允许省略`-loader`扩展名(除非特意去配置)，`loader` 的参数配置只能写在 `rule.options`内部不能写在外面：
```javascript
module: {
    rules: [{
        test: /\.css$/,
        use: [
            { 
                loader: 'style-loader' 
            }, 
            {
                loader: 'css-loader',
                options: {
                    modules: true
                }
            }
        ]
    }]
}
```
### ExtractTextWebpackPlugin
ExtractTextWebpackPlugin 1.x版本在webpack2中将无法使用，适配webpack2的ExtractTextWebpackPlugin配置方式有所变化：
```javascript
module: {
    rules: [
        test: /.css$/,
        loader: ExtractTextPlugin.extract({
            fallbackLoader: "style-loader",
            loader: "css-loader",
            publicPath: "/dist"
        })
    ]
},
plugins: [
    new ExtractTextPlugin({
        filename: "bundle.css",
        disable: false,
        allChunks: true
    })
]
```
### webpack2模块按需加载
webpack2现在支持**ES2015**的`import()`模块异步加载语法，而且它会返回一个`Promise`对象，这样模块异步加载出错也能有方法捕捉了：
```javascript
function btnClickHandler() {
    import('./js/utils').then(module => {
        return module.default;
    }).catch(err => {
        console.log("Chunk loading failed");
    });
}
```
`import()`加载机制还支持表达式写法：
```javascript
function route(path, query) {
    return import(`./routes/${path}/route`)
        .then(route => new route.Route(query));
}
```
webpack2将会把所有可能符合表达式的模块都另外打包起来。
要使用webpack2的这个特性，需要加一个配置项，使的`Babel`不会将这些代码转化为*ES3*的代码。
```javascript
{
  "presets": [
    ["es2015", { "modules": false }]
  ]
}
```
但是`import()`不具备上文所提到的`require.ensure()`方法的第三个参数可以指定`chunkname`的功能，如果需要用到这个功能，还是可以得使用`require.ensure()`
### 模板字符串支持
就像上面一点中使用到的，webpack2现在支持**ES2015**中的模板字符串语法了。
```javascript
function route(path, query) {
    return import(`./routes/${path}/route`)
        .then(route => new route.Route(query));
}
```
### 配置项Promise化
webpack1的配置文件只能是一个配置对象，webpack2支持从配置文件中返回一个`Promise`对象，这就是使得我们可以更直观的在一个文件中支持多种环境的配置，而不需要像有的项目那样通过不同的配置文件来管理。
```javascript
module.exports = function() {
    // 异步读取语言包
    return fetchLangs().then(lang => ({
        entry: "...",
        // ...
        plugins: [
            new DefinePlugin({ LANGUAGE: lang })
        ]
    }));
};
```
<br>
以上就是我使用webpack的一点学习经验,并不全面，不对之处请帮忙指出，谢谢。