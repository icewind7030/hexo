title: Javascript禁止跨域的原理
date: 2014-08-22 15:55:05
tags: [Javascript,跨域]
categories: 前端
---

很早就知道了JavaScript是出于安全方面的考虑，不允许跨域调用其他页面的对象。但是只是知其然，却不知其所以然，前端时间在测试学习CSRF原理的时候才通过自己动手测试了解到了Javascript阻止跨域的原理。

<!-- more -->

先稍微介绍一下Javascript的跨域判定原则吧，至于具体的跨域解决方法，网上很多了，抄过来也意义不大。

##什么是跨域

简单地理解就是因为JavaScript同源策略的限制，浏览器将限制客户端的JavaScript在一个安全沙箱内，通常JS不能直接同一台不同的域的远程服务器通信。更详细的说明可以看下表：
![](http://ww4.sinaimg.cn/mw690/7d25d640jw1ejlh6q509fj20jz0bcwgh.jpg)

##Javascript阻止跨域的原理
那么Javascript到底是如何阻止跨域通信的呢？是直接禁止了向跨域服务器提交数据，还是阻止了其他环节呢？

最直观的方法还是写一个页面测试一下,我在本机用tomcat运行了一个如下静态页面，利用项目中的一台远程服务器进行接收测试：
```html
<!doctype html>
<html>
<head>
	<meta charset="utf-8">
	<title>cross-domain</title>
	<script src="http://lib.sinaapp.com/js/jquery/1.7.2/jquery.min.js" type="text/javascript"></script>
</head>
<body>
	<button onclick="test()">click</button>
	<script>

		var test = function  () {
			var data = {};
			data.type = "2";
			data.day = "1";
			data.time = "00:00";
			$.post("http://10.16.66.8/config/update_auto",data).then(function  (data) {
				console.log(data);
			});
		}		
	</script>
</body>
</html>
```
点击click按钮提交跨域post请求,这时浏览器就会报错了，我们来看看这条错误

> XMLHttpRequest cannot load http://10.16.66.8/config/update_auto. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://localhost:8080' is therefore not allowed access. 

这就说明我们的请求其实是发送到了远程的服务器的，因为服务器发回了响应，但是由于远程服务器的http响应的header中缺少了`"Access-Control-Allow-Origin"`字段，浏览器就阻止了我们的本地服务器页面获取响应的内容。只有当目标页面的response中，包含了Access-Control-Allow-Origin这个header，并且它的值里有我们发起请求的域名时，浏览器才允许我们拿到它页面的数据进行下一步处理。如：`Access-Control-Allow-Origin: http://abc.com`，或者`Access-Control-Allow-Origin: *`代表谁都可以用。

##解决办法
以上就是Javascript禁止跨域的原理。至于跨域的方法有很多:
1. document.domain+iframe的设置
2. 动态创建script
3. 利用iframe和location.hash
4. window.name实现的跨域数据传输
5. 使用HTML5 postMessage
6. 利用flash
具体使用方法可以参考：[JavaScript跨域总结与解决办法](http://www.cnblogs.com/rainman/archive/2011/02/20/1959325.html)