title: CSS中两栏布局，三栏布局，等高布局
date: 2014-05-26 21:04:38
categories: 前端
tags: [CSS,HTML]
---

网上找到的常见CSS各类布局问题解决方案汇总，并附带一些必要的解释,本文较长，建议配合文章右上角导航栏进行阅读，也可以点击侧边栏右上角的隐藏按钮![](http://ww2.sinaimg.cn/large/7d25d640gw1egs18bw61nj201400x3y9.jpg "隐藏按钮")，从而显示出侧边导航栏，方便阅读

<!--more-->

## 两栏布局:

### float+margin（一侧定宽，一侧自动）
``` html
<!DOCTYPE HTML>
<html>
<head>
<meta charset="UTF-8">
<title> 左定宽，右自动 </title>
<style type="text/css">
html,body{margin:0; padding: 0;}
.m-box .left,.m-box .right { height: 200px; line-height:200px; text-align:center; }

.m-box .left{float:left; width:200px; background-color:#ccc; }
.m-box .right{margin-left:210px;  background-color:#666;}
</style>
</head>
<body>
<div class="m-box">
    <div class="left">左</div>
    <div class="right">右</div>
</div>
</body>
</html>
``` 
![](http://ww1.sinaimg.cn/large/7d25d640gw1egs0hbnmz6j20kz05pmx3.jpg "一侧定宽，一侧自动")
这个比较简单，通过左栏左浮动使div脱离文档流，不再占用整行，再通过`margin-left`为右栏定位  

---
<br/>

### position+margin（一侧定宽，一侧自动）
```html
<!DOCTYPE HTML>
<html>
<head>
<meta charset="UTF-8">
<title> 左定宽，右自动 </title>
<style type="text/css">
html,body{margin:0; padding: 0;}
.m-box .left,.m-box .right { height: 200px; line-height:200px; text-align:center; }

.m-box .left{position: absolute; width:200px; background-color:#ccc; }
.m-box .right{margin-left:210px;  background-color:#666;}
</style>
</head>
<body>
<div class="m-box">
    <div class="left">左</div>
    <div class="right">右</div>
</div>
</body>
</html>
```
![](http://ww1.sinaimg.cn/large/7d25d640gw1egs0hbnmz6j20kz05pmx3.jpg "一侧定宽，一侧自动")
这个也比较简单，通过将左栏的定位声明为`absolute`来使其脱离文档流

---
<br/>

### float+负margin（一侧定宽，一侧自动）
```html
<!DOCTYPE HTML>
<html>
<head>
<meta charset="UTF-8">
<title> 左定宽，右自动 </title>
<style type="text/css">
html,body{margin:0; padding: 0;}
.m-box .cont,.m-asider { height: 200px; line-height:200px; text-align:center; }

.m-box { float:left; width: 100%;}
.m-box .cont {margin-left:210px;background-color: #ccc;}
.m-asider {float: left; width: 200px; margin-left: -100%; background-color: #ccc;}
</style>
</head>
<body>
<div class="m-box">
    <div class="cont">右</div>
</div>
<div class="m-asider">左</div>
</body>
</html>
```
![](http://ww1.sinaimg.cn/large/7d25d640gw1egs0hbnmz6j20kz05pmx3.jpg "一侧定宽，一侧自动")
这是通过`margin-left: -100%;`来使html中处于靠后位置的元素出现在靠前元素的左侧，有关**负margin**实现该效果的原理以及负margin的详细解释请查看[CSS中负margin原理及应用](http://icewind-blog.com/2014/05/27/css%E4%B8%AD%E8%B4%9Fmargin%E5%8E%9F%E7%90%86%E5%8F%8A%E5%BA%94%E7%94%A8/)。

---
<br/>

## 三栏布局：

### float+margin（两侧定宽，中间自适应）
```html
<!DOCTYPE HTML>
<html>
<head>
<meta charset="UTF-8">
<title> 左右固定，中间自适应 </title>
<style type="text/css">
html,body{margin:0; padding: 0;}
.left,.right,.center { height: 200px; line-height:200px; text-align:center; }

.left {float:left; width: 200px; background-color: #ccc;}
.right {float:right; width: 200px; background-color: #ccc;}
.center { margin: 0 210px; background-color: #666;}
</style>
</head>
<body>
<div class="left">左</div>
<div class="right">右</div>
<div class="center">中</div>
</body>
</html>
```
![](http://ww3.sinaimg.cn/large/7d25d640gw1egs0oxmrxoj20kz05qq2w.jpg "两侧定宽，中间自适应")
本方法比较好理解，但是要**注意**的是左右两侧的div需要在html中写在中间的div之前，因为一旦右栏div写在中间div之后，在html渲染时，中间一栏div会占据左栏浮动div之后的整行空间，使得右栏div会从下一行开始向右浮动，而不是浮动在中间div的右侧

---
<br/>

### position+margin（两侧定宽，中间自适应）
```html
<!DOCTYPE HTML>
<html>
<head>
<meta charset="UTF-8">
<title> 左右固定，中间自适应 </title>
<style type="text/css">
html,body{margin:0; padding: 0;}
.left,.right,.center { height: 200px; line-height:200px; text-align:center; }

.left {position:absolute; top:0; left:0; width: 200px; background-color: #ccc;}
.right {position:absolute; top:0; right:0;  width: 200px; background-color: #ccc;}
.center { margin: 0 210px; background-color: #666;}
</style>
</head>
<body>
<div class="left">左</div>
<div class="center">中</div>
<div class="right">右</div>
</body>
</html>
```
![](http://ww3.sinaimg.cn/large/7d25d640gw1egs0oxmrxoj20kz05qq2w.jpg "两侧定宽，中间自适应")
这个原理也比较简单，这种方法则不必右栏div写在中间div之前，可见`float:left;`和`position:absolute;`虽然都是使元素脱离了文档流，但是它们在html渲染时还是有区别的

---
<br/>

### float+负margin（两侧定宽，中间自适应）
```html
<!DOCTYPE HTML>
<html>
<head>
<meta charset="UTF-8">
<title> 三列布局 </title>
<style type="text/css">
html,body{margin:0; padding: 0;}
.left,.right,.m-box .center { height: 200px; line-height:200px; text-align:center; }

.m-box { float:left; width: 100%;}
.m-box .center {margin:0 210px;background-color: #666;}
.left,.right {float: left; width: 200px; margin-left: -100%; background-color: #ccc;}
.right {margin-left: -200px;}
</style>
</head>
<body>
<div class="m-box">
    <div class="center">中</div>
</div>
<div class="left">左</div>
<div class="right">右</div>
</body>
</html>
```
![](http://ww3.sinaimg.cn/large/7d25d640gw1egs0oxmrxoj20kz05qq2w.jpg "两侧定宽，中间自适应")
本例也是负margin的运用，在稍后的一章博客[CSS中负margin原理及应用](http://icewind-blog.com/2014/05/27/css%E4%B8%AD%E8%B4%9Fmargin%E5%8E%9F%E7%90%86%E5%8F%8A%E5%BA%94%E7%94%A8/)会得到解答。

此外还有两种两侧自动、中间定宽情形的解决方案，待我研究清楚了再贴上来。

---
<br/>

## 等高布局：

### float+margin+position（两列等高布局）
```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>两列等高布局</title>
<style type="text/css">
html,body{margin:0; padding: 0;}
.clearfix { zoom: 1 }
.clearfix:after { content: ""; display: block; height: 0; line-height: 0; font-size: 0; visibility: hidden; clear: both; }

.wrap { width: 950px; margin: 0 auto; background: #ccc; }
.content { margin-left: 150px; border-left: 1px solid #666; background: #ddd; }
.main { float: left; width: 100%; }
.side { float: left; width: 150px; margin-left: -950px; position: relative; }
</style>
</head>
<body>
<div class="wrap">
    <div class="content clearfix">
        <div class="main">
            中-等高吗？<br />
            中-等高吗？<br />
            中-等高吗？<br />
            中-等高吗？<br />
            中-等高吗？<br />
        </div>
        <div class="side">
            左-等高吗？<br />
        </div>
    </div>
</div>
</body>
</html>
```
![](http://ww1.sinaimg.cn/large/7d25d640gw1egs0vkpzgjj20qg02odfx.jpg "两列等高布局")
这个代码是我找来的，老实说，看不太懂，好像是通过构造一个背景来制造等高的假象，但是具体实现查了挺多资料也没看太懂，希望知道原理的可以留言告诉我，不胜感激！

---
<br/>

### float+position（三列等高布局）
```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>三列自适应高布局</title>
<style type="text/css">
html,body{margin:0; padding: 0;}

.fl { float: left; }
.container3 { background:green; overflow: hidden; position: relative; }
.container2 { background:yellow; position: relative; right: 30%; }
.container1 { width: 100%; background:red; position: relative; right: 40%; }
.col1 { width: 26%; position: relative; left: 72%; }
.col2 { width: 36%; position: relative; left: 76%; }
.col3 { width: 26%; position: relative; left: 80%; }
</style>
</head>
<body>
<div class="container3 fl">
    <div class="container2 fl">
        <div class="container1 fl">
            <div class="col1 fl">左<br/>小时候我有一个梦想，长大了我就忘了...</div>
            <div class="col2 fl">中<br/>小时候我有一个梦想，长大了我就忘了...小时候我有一个梦想，长大了我就忘了...</div>
            <div class="col3 fl">右<br/>小时候我有一个梦想，长大了我就忘了...小时候我有一个梦想，长大了我就忘了...小时候我有一个梦想，长大了我就忘了...</div>
        </div>
    </div>
</div>
</body>
</html>
```
![](http://ww2.sinaimg.cn/large/7d25d640gw1egs0xx3nbqj20l2038gn6.jpg "三列等高布局")
此方法是纯CSS无hacks的跨游览器多列等高布局的解决方法，由国外大神**Matthew James Taylor**的文章[Equal Height Columns with Cross-Browser CSS and No Hacks](http://matthewjamestaylor.com/blog/equal-height-columns-cross-browser-css-no-hacks)提出的，国内也有博客作者进行了翻译和解释以便理解，参看[纯CSS无hacks的跨游览器多列布局](http://www.cnblogs.com/rubylouvre/archive/2009/07/15/1523683.html)，其中除了把`position: relative; right: 30%;`错误的说成右移而把`position: relative; left: 72%;`说成左移外，整个原理讲的比较清楚了。


