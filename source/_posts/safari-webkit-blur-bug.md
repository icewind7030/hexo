title: iOS显示CSS3的blur效果出现的越界问题及解决办法
date: 2015-11-13 17:02:36
categories: 前端
tags: [CSS,HTML]
---

装win10的双系统不小心清空了我的博客文件，修复起来有点麻烦，导致这篇博客拖了很久才开始写。这个问题是我在开发应用于iOS设备的webview中的图片模糊效果时发现的这个问题，写出来分享一下。

<!-- more -->

关于使页面上的图片呈现类似于原生iOS的毛玻璃模糊效果的方法，张鑫旭的[这篇文章](http://www.zhangxinxu.com/wordpress/2013/11/%E5%B0%8Ftip-%E4%BD%BF%E7%94%A8css%E5%B0%86%E5%9B%BE%E7%89%87%E8%BD%AC%E6%8D%A2%E6%88%90%E6%A8%A1%E7%B3%8A%E6%AF%9B%E7%8E%BB%E7%92%83%E6%95%88%E6%9E%9C/)已经总结的非常详细和周全了，我也就不复述了，值得一读。

## 问题

由于我所需要开发的功能只是在移动设备访问，所以能用几条CSS3就解决问题再好不过了。但是，在使用CSS3的这个blur特性的时候，碰到了一些问题。

那就是：应用了CSS3 blur效果的子元素，在Safari浏览器中，即使父元素设置了`overflow:hidden;`，子元素的模糊效果依然会渗透到父元素的边界之外。

可以看下面的代码：
```html
	<!doctype html>
	<html>
	<head>
		<meta charset="utf-8">
		<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no">
	    <title>CSS3模糊测试</title>
	    <style>
			.wrapper{
				height: 90px;
				margin-top: 100px;
				border: 1px solid red;
				overflow: hidden;
			}
			.blur-bg{
				height: 100%;
				background: url(http://ww1.sinaimg.cn/large/7d25d640gw1exzhnyodjdj20480483yj.jpg) center center / cover no-repeat;
				-webkit-filter: blur(20px);
				filter: blur(20px);
			}
	    </style>   
	</head>
	<body>
		<div class="wrapper">
			<div class="blur-bg"></div>
		</div>
	</body>
	</html>
```
我所希望的效果是下图这样的：
![](http://ww2.sinaimg.cn/large/7d25d640gw1exzhq7q7y3j20bc04c3yj.jpg)
这也的确是在大部分手机上所呈现的效果，简单粗暴。

但是，到了Safari浏览器或者微信及其他客户端的webview中，效果是这样的：
![](http://ww4.sinaimg.cn/large/7d25d640gw1exzhvi6t88j20dn05ujro.jpg)
如图所示，模糊效果渗透到了设置了`overflow:hidden;`的父元素之外，这显然是跟设计要求不符的。

## 解决方法

经过一些尝试，在某些情况下，把子元素的尺寸设置的比父元素稍微小一些或者通过CSS`clip`属性能部分解决这个问题，但是并不能全部解决，而且显示效果并不太好，与代码的追求也相悖。

最后，我抱着试一试的心态给这个父元素加了下面这条属性：
```css
-webkit-transform: translate3d(0, 0, 0);
transform: translate3d(0, 0, 0);
```
问题奇迹般的得到了解决。

我猜测，这个CSS3的blur特性在Safari上的渲染机制可能跟其他设备不一样，所以出现了`overflow:hidden;`无效的情况，这应该是一个BUG，而通过上面的那段CSS,触发了父元素GPU渲染，从而呈现了正常的CSS渲染规则。

以上就是全部内容，欢迎指正。