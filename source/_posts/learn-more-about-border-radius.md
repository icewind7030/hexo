title: 详解border-radius
date: 2015-07-06 19:30:56
categories: 前端
tags: [CSS,HTML]
---

好久没写博客了，找工作、毕业、入职，总算差不多安顿下来了，先从一篇简单的border-radius开始恢复博客吧！

说到border-radius,应该有不少人像我一样，最常用的就是这样`border-radius:5px`，设置一个5px的圆角，就完了。但是，如果面对一个这样的css设置，`border-radius: 2em 1em 4em / 0.5em 3em`，当时我就懵逼了。

<!--more-->

## 定义和用法

先来看看W3School上的属性介绍：
![](http://ww2.sinaimg.cn/large/7d25d640gw1etsyn61o55j20kh0d0q55.jpg)

## 解释

看完上面的说明基本能了解border-radius属性的属性对应关系，也就是每4个长度值依次顺时针代表 左上角、右上角、右下角以及左下角，下面的两个角如果没有指定圆角值，则和其**对角的值**相同，而如果右上角的值也省略了，则其他3个角的都与唯一的那个值也就是**左上角的值**相同。

但是，还有一点，这语法中间的`/`是什么意思，为什么它左右两边都有4个值，这些值又是按照什么规则进行组合的呢？

## 示例
- 光看W3School上的解释还是没那么容易理解的，还是得亲手试一试才知道。

```html
	<!DOCTYPE html>
	<html>

	<head>
	    <meta charset="utf-8" />
	    <title>border-radius测试</title>
	    <style>
	    div {
	        margin: 0 auto;
	        width: 200px;
	        height: 80px;
	        padding: 10px;
	        background-color: #CCC;
	        border: solid 1px #333;
	        border-radius: 100px / 50px;
	        text-align: center;
	    }
	    </style>
	</head>

	<body>
	    <div>
	    </div>
	</body>

	</html>
```

上面的代码的运行结果如下图
![](http://ww4.sinaimg.cn/large/7d25d640gw1etv6njto0cj209v03h0sk.jpg)

可见，`/`的作用就是分隔圆角的X轴半径和Y轴半径的，这就使得上面的代码形成了一个X轴半径100px,Y轴半径50px的椭圆。

<br>

- 再看下面的代码：
```html
	<!DOCTYPE html>
	<html>

	<head>
	    <meta charset="utf-8" />
	    <title>border-radius测试</title>
	    <style>
	    div {
	        margin: 0 auto;
	        width: 200px;
	        height: 80px;
	        padding: 10px;
	        background-color: #CCC;
	        border: solid 1px #333;
	        border-radius: 100px 80px 50px/ 100px 50px;
	        text-align: center;
	    }
	    </style>
	</head>

	<body>
	    <div>
	    </div>
	</body>

	</html>
```

运行结果如下图
![](http://ww3.sinaimg.cn/large/7d25d640gw1etv6usbtksj20b103cglf.jpg)

这个就稍显复杂了，但是根据W3School上给出的相关规则，还是可以很好的理解的：
- 左上角是100px 100px的圆角；
- 右上角是80px 50px的圆角；
- 右下角只给了X轴半径，那么它的Y轴半径就参照它的对角的值，也就是左上角的100px；
- 同理，左下角2个值都未指定，则与它的对角——右上角的值相同。

写这篇文章也不是因为这个知识有多稀奇，只是给自己的一个重拾博客的动力，希望能继续学习，将来能写出一些有质量的文章。