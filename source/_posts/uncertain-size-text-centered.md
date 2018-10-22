title: 长度不定的多行文字如何在大小确定的容器中垂直居中
date: 2014-06-12 10:55:17
categories: 前端
tags: [CSS,HTML]
---

昨天去百度面试，被问了一道关于长度不定的文字如何在容器中水平和垂直居中的问题，当时答的不好，没有想到不支持CSS3 box或者`display:table-cell;`的IE6/7下的实现方式，当时抓住机会问了问面试官应该怎么实现，回来查了相关资料，总结如下：

<!--more-->

容器元素大小确定，内部文字内容长度不确定，对于水平居中，比较简单，只需要设置容器元素的样式为`text-align:center;`即可，一行文字也好办，只需让容器元素的`line-height`和`height`相等即可,下面主要讨论的是多行文字垂直居中的实现方式。

既然在支持`display:table-cell;`的浏览器中用它可以很轻松的实现垂直居中，那么我们就没有理由不在支他的浏览器中使用它，剩下的就是针对IE6/7编写hack了。

这里利用了在三层结构，即先用P元素包裹文字后，再在其外层套一层div，最后才是容器div，先将大小指定的容器元素定位声明为`position: relative;`使内层`position:absolute;`的div元素能够利用top和left相对于容器元素绝对定位,最后再将P元素定位声明为`position:relative;top:-50%;left:-50%;`当然，这些都使用了针对IE6/7的hack的，这一实现方式是利用的IE6/7的一个布局BUG。

下面的整体实现代码：
```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8" />
<title>文本长度不定时垂直居中</title>
<style type="text/css">
.main{
width:500px;
height:400px;
border:1px solid #777;
text-align:center;
display:table-cell;
vertical-align:middle;
position: relative;
}
/*FOR IE6/7*/
.main div {
* width: 230px;
*position:absolute;
*top:50%;
*left:50%;
background: blue;
}
.main div p {
*position:relative;
*top:-50%;
*left:-50%;
background:#ff0;
}
</style>
</head>
<body>
<div class="main">
<div><p>垂直居中，多行内容也会垂直居中。垂直居中，多行内容也会垂直居中。垂直居中，多行内容也会垂直居中。垂直居中，多行内容也会垂直居中。</p></div>
</div>
</body>
</html>
```

以上算是一种针对文字长度不定的多行文字在大小确定的容器元素中垂直居中的方案，应该也适用于非文字的其它元素的情形，是我在面试过程中通过向面试官请教而了解到的，如果你有其他更好的方法，欢迎告诉我！

这题也暴露了自己浏览器兼容性和CSS hack方法方面所学还是太少，还得继续努力！
