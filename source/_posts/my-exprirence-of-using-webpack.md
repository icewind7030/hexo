title: "使用Webpack重构代码的一点经验分享"
date: 2016-07-01 14:36:33
categories: 前端
tags: [Javscript,Webpack]
---

前段时间对云阅读客户端资讯模板的重构工作初具规模，其中使用到了webpack作为前端的构建工具，配合其他一些常见的npm模块，实现了一键构建、打包、上传，大大提高了这部分工作的开发和维护效率，这两个月有点忙，今天有时候来写写，做个记录。

<!--more-->

接手云阅读客户端资讯模板的维护工作也有近一年了，资讯模板是用于客户端中显示各类新闻和自媒体文章的webview前端模板，拥有完整的html结构和各版式对应的CSS和JS，再由客户端读取用，填充进具体的资讯内容，形成用户所见的各类版式的资讯文章。

这里主要不是讲这部分的原理，而我是接手了这部分工作以后，由于资讯模板的版式有近20种，之前开发的人是将这20种模板各自独立的进行开发和维护，这使我在跟进版本需求和维护的工作中，深深的感受到了什么叫事倍功半，大部分的时间是在重复着机械的粘贴、复制、打包和上传等操作，这使我越来越迫切的想要重构掉整个资讯模板代码。

重构的核心其实就是代码的模块化复用，结合现今非常流行的webpack，实现一次开发，应用到所有模板。下图是我重构初具规模后的代码结构：
![](http://easyread.nos.netease.com/web/trunk/1467356153654/arc.png)
应该很容易看懂，`cssLib`和`jsLib`就是模块化后的css和js代码，在模板中按需加载；`images`则是模板中会用到的一些图片资源（之前图片数量非常之多，我按照分类进行了部分sprite合并），这些图片会被各个模板的代码引用后通过webpack构建保存到各个模板自己目录下的images文件夹中；再下面的5个文件夹则是已经完成重构的几个模板；package.json主要就是记录了改

主要记录一下我的webpack配置和用途，一个典型的模版的文件结构如下：
![](http://easyread.nos.netease.com/web/trunk/1467366214031/t5.png)
我的webpack配置如下:
```javascript
var ExtractTextPlugin = require('extract-text-webpack-plugin');
var WebpackOnBuildPlugin = require('on-build-webpack');
var uploadZip = require('../jsLib/uploadZip');
var generateZip = require('../jsLib/generateZip');
var env = process.env.NODE_ENV || 'dev';
var genName = env === 'dev' ? 'source_code' : 'template';
//测试服务器模板id和线上服务器模板id
var configJson = require('./package.json');
var templateId = env === 'online' ? configJson.onlineTemplateId : configJson.testTemplateId;

module.exports = {
    entry: "./js/original_code.js",
    output: {
        path: './',
        publicPath: "../",
        filename: './js/'+ genName +'.js'
    },
    module: {
        loaders: [{
            test: /\.css$/,
            loader: ExtractTextPlugin.extract("style", "css")
        }, {
            test: /\.(png|jpg|GIF)$/,
            loader: 'file-loader?name=images/[name].[ext]'
        }, {
            test: /\.scss$/,
            loader: ExtractTextPlugin.extract("style", "css!sass?outputStyle=expanded")
        }]
    },
    plugins: [
        new ExtractTextPlugin('./css/'+ genName +'.css', {
            publicPath: "../"
        }),
        new WebpackOnBuildPlugin(function(stats) { 
            if(env !== 'dev'){
                generateZip();
                uploadZip(templateId, env);
            }
        })
    ]
};
```
js文件夹下的`original_code.js`是整个模板的核心文件，控制了css和js模块的加载，以及各个模板所特有的样式的设置。部分代码如下：
![](http://easyread.nos.netease.com/web/trunk/1467366449668/original.png)
其中prisUtils和versionUtils就是从各个模板中抽取出来的通用模块，而prisUtils之所以要合并到window.pris上，是为了给客户端调用。

而webpack的使用中，我用到了一个比较特别的模块：`on-build-webpack`，这个模块的功能是在webpack构建完成后可以继续执行自己指定的功能，如上面代码所示，我在webpack构建完成后，使用自己编写的`generateZip`方法对文件进行了zip打包，然后再上传到了服务器以供客户端调用。
```javascript
new WebpackOnBuildPlugin(function(stats) { 
    if(env !== 'dev'){
        generateZip();
        uploadZip(templateId, env);
    }
})
```
为了使客户端webview的css能够被缓存以提高性能，我还需要把css代码输出成一个独立的CSS文件，这就需要用到`extract-text-webpack-plugin`模块，而且由于我为了兼容以前版本的客户端，就需要做到打包出来的模板zip包的文件结构与原来一样，所以使用了设置功能更为强大的`file-loader`插件来处理图片，使得构建得到的模板图片都存放在指定的**images**文件夹中,最后，为了在最终输出的css中能够访问到这些图片，`extract-text-webpack-plugin`模块还需要设置`publicPath`变量如下：
```javascript
new ExtractTextPlugin('./css/'+ genName +'.css', {
    publicPath: "../"
})
```

还有一点比较特别的是那3个批处理文件，它们的用途可以从文件名上清楚的看到，实现的原理就是通过windows的shell命令执行webpack操作并带入相应的表示当前运行环境和目的的参数,比如说`一键更新测试环境.bat` 文件,代码很简单
```shell
set NODE_ENV=test&& webpack -p --progress && pause
```
表示设置运行环境的变量的值`NODE_ENV`为`test`,然后执行压缩代码的webpack构建。
`webpack.congif.js`执行时，通过如下代码实现区分运行环境和设置：
```javascript
var env = process.env.NODE_ENV || 'dev';
var genName = env === 'dev' ? 'source_code' : 'template';
//测试服务器模板id和线上服务器模板id
var configJson = require('./package.json');
var templateId = env === 'online' ? configJson.onlineTemplateId : configJson.testTemplateId;
```
执行结果如下:
![](http://easyread.nos.netease.com/web/trunk/1467370609705/webpack.png)
接着通过上面提到的`on-build-webpack`模块，在webpack构建完成后，通过判断当前运行环境为非本地开发环境，运行`jsLib`目录下的`generateZip`进行zip压缩打包，再通过`uploadZip`模块使用读取到的模板在服务器的id发送请求到服务器更新对应的模板。

`generateZip`使用的是`npm`中的`node-zip`模块,我这里的打包过程用的同步模式，好处是条理清晰，不用嵌套回调，因为我这只是在本地运行用，如果是运行在服务端的打包逻辑，还是使用异步模式来避免磁盘I/O一直被占用的好。
```javascript
var generateZip = function () {
	var fs = require('fs');
	var zip = new require('node-zip')();
	//config.json
	var stream1 = fs.readFileSync('./config.json');
	zip.file('config.json', stream1);
	//template.html
	var stream2 = fs.readFileSync('./template.html');
	zip.file('template.html', stream2);
	//template.js
	var stream3 = fs.readFileSync('./js/template.js');
	zip.folder('js').file('template.js', stream3);
	//template.css
	var stream4 = fs.readFileSync('./css/template.css');
	zip.folder('css').file('template.css', stream4);
	//images folder
	var imagesList = fs.readdirSync('./images'),
		imagesLength = imagesList.length,
		imagesFolder = zip.folder('images'),
		imageStream;
	for(var i = 0; i < imagesLength; i++){
		imageStream = fs.readFileSync('./images/' + imagesList[i]);
		imagesFolder.file(imagesList[i], imageStream);
	}
	//generate zip
	var data = zip.generate({base64: false, compression: 'DEFLATE'});
	fs.writeFileSync('output.zip', data, 'binary');
	console.log('generate zip success');
}
module.exports = generateZip;
```
`uploadZip`模块代码如下：
```javascript
var uploadZip = function (templateId, env) {
	var domain;
	if(env == 'online'){
		console.log('线上环境');
		domain = 'http://backend.pris.netease.com';
	}else{
		console.log('测试环境');
		domain = 'http://hzbxs-yuedutest21.server.163.org:8280';
	}

	var request = require('request');
	var fs = require('fs');

	var req1 = request.post(
	    domain + '/imgUpload.do?op=upload&fileType=1',
	    function(error, response, body) {
	        if (error) {
	            console.log('Error!');
	        } else if (response.statusCode === 200) {
	            if (typeof body === 'string') {
	                var result = JSON.parse(body);
	                var zipId = result.fileId;
	                console.log('upload success zipId is ' + zipId);
	                if(typeof templateId === 'object' && templateId.length){
	                	for (var i = 0; i < templateId.length; i++) {
	                		var updateTemplateId = templateId[i];
	                		(function (id) {
                				request.post(
                			        domain + '/articleTemplate.do',
                			        {form: {
                			            'id': id,
                			            'op': 'update',
                			            'newId': zipId,
                			            'previewImage': ''
                			            }
                			        },function (error, response, body) {
                			            if(error){
                			                console.log('Error!');
                			            }else if(response.statusCode === 200){
                			                console.log('update templateId '+ id +' success');
                			            }else{
                			                console.log(response.statusCode);
                			            }
                			        }
                				)
	                		})(updateTemplateId)
	                	}
	                }else{
	                	var req2 = request.post(
	                	        domain + '/articleTemplate.do',
	                	        {form: {
	                	            'id': templateId,
	                	            'op': 'update',
	                	            'newId': zipId,
	                	            'previewImage': ''
	                	            }
	                	        },function (error, response, body) {
	                	            if(error){
	                	                console.log('Error!');
	                	            }else if(response.statusCode === 200){
	                	                console.log('update '+ templateId +' success');
	                	            }else{
	                	                console.log(response.statusCode);
	                	            }
	                	        }
	                	)
	                }
	            }
	        } else {
	            console.log(response.statusCode);
	        }
	    });
	var form = req1.form();
	var filepath = './output.zip';
	form.append('file', fs.createReadStream(filepath));
};
module.exports = uploadZip;
```