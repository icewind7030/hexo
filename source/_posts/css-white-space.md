title: CSS中的white-space
date: 2014-07-20 19:30:56
categories: 前端
tags: [CSS,HTML]
---

最近在项目中遇到了一个需要呈现原文中就带有空格和换行符的邮件内容的需求，其中就用到了之前没怎么接触过的white-space属性，特此记录。

<!--more-->

## 定义和用法

white-space 属性设置如何处理元素内的空白。
这个属性声明建立布局过程中如何处理元素中的空白符。值 pre-wrap 和 pre-line 是 CSS 2.1 中新增的。
![](http://ww2.sinaimg.cn/mw690/7d25d640gw1eiji9cs5g2j20jv039aag.jpg)


##可能的值
![](http://ww1.sinaimg.cn/mw690/7d25d640gw1eijidtyupaj20k305fjtc.jpg)


##区别示例
只写出它们的区别可能不好理解，还是通过实例来展现:

首先看看原文字的排版，主要注意每行文字前的空格、文字间的空格和第一行和第二行之间空行以及文字的换行情况等。
![](http://ww2.sinaimg.cn/mw690/7d25d640gw1eijj6szns4j20q503zmzd.jpg)

接下来是包含以上5种`white-space`CSS属性的html代码示例和图片结果

```html
<!DOCTYPE html> 
<html> 
<head> 
    <meta charset="utf-8"> 
    <title>white-space</title>
    <style>
    	p{
    		border: red 1px dotted;
    		width: 600px;
    	}
		.pre{white-space: pre;}
		.normal{white-space: normal;}
		.nowrap{white-space: nowrap;}
		.pre-wrap{white-space: pre-wrap;}
		.pre-line{white-space: pre-line;}
    </style>	
</head>
<body>
<p class="pre">
pre
	在此，我们总结 Markdown 的优点如下：

	纯文本，    所以兼容性极强，可以用所有文本编辑器打开。
	让你专注于文字而不是排版。
	格式转换方便，    Markdown 的文本你可以轻松转换为 html、电子书等。
	Markdown     的标记语法有极好的可读性。当然，我们既然如此推崇 Markdown ，也必定会教会你使用 Markdown ，这也是本文的目的所在。
</p>
<p class="normal">
normal
	在此，我们总结 Markdown 的优点如下：

	纯文本，    所以兼容性极强，可以用所有文本编辑器打开。
	让你专注于文字而不是排版。
	格式转换方便，    Markdown 的文本你可以轻松转换为 html、电子书等。
	Markdown     的标记语法有极好的可读性。当然，我们既然如此推崇 Markdown ，也必定会教会你使用 Markdown ，这也是本文的目的所在。
</p>
<p class="nowrap">
nowrap
	在此，我们总结 Markdown 的优点如下：

	纯文本，    所以兼容性极强，可以用所有文本编辑器打开。
	让你专注于文字而不是排版。
	格式转换方便，    Markdown 的文本你可以轻松转换为 html、电子书等。
	Markdown     的标记语法有极好的可读性。当然，我们既然如此推崇 Markdown ，也必定会教会你使用 Markdown ，这也是本文的目的所在。
</p>
<p class="pre-wrap">
pre-wrap
	在此，我们总结 Markdown 的优点如下：

	纯文本，    所以兼容性极强，可以用所有文本编辑器打开。
	让你专注于文字而不是排版。
	格式转换方便，    Markdown 的文本你可以轻松转换为 html、电子书等。
	Markdown     的标记语法有极好的可读性。当然，我们既然如此推崇 Markdown ，也必定会教会你使用 Markdown ，这也是本文的目的所在。
</p>
<p class="pre-line">
pre-line
	在此，我们总结 Markdown 的优点如下：

	纯文本，    所以兼容性极强，可以用所有文本编辑器打开。
	让你专注于文字而不是排版。
	格式转换方便，    Markdown 的文本你可以轻松转换为 html、电子书等。
	Markdown     的标记语法有极好的可读性。当然，我们既然如此推崇 Markdown ，也必定会教会你使用 Markdown ，这也是本文的目的所在。
</p>
</body>
</html>
```
![](http://ww1.sinaimg.cn/mw690/7d25d640gw1eijjjpbkw3j20tv0iz7cc.jpg)

从上面代码就可以看出：
`pre`完全保存原文字的空格和换行；
`normal`则会过滤掉原文自带的空格和换行，但是文字碰到父元素边界时会自动换行；
`nowrap`除了会无视原文的空格和换行外，文字碰到父元素边界依然不换行，所以只会显示一行；
`pre-wrap`与`pre-line`比较难区别，其中`pre-wrap`保存原文字中的空格，甚至连换行也保存了，但是它与`pre`的区别在于文字碰到父元素边界要自动换行，而不像`pre`要保持原文字排版不惜超过父元素边界，`pre-line`则只是保留了换行，合并了空格，同时遇到父元素边界也得自动换行。



