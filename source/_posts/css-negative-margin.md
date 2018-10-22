title: CSS中负margin原理及应用
date: 2014-05-27 10:07:13
categories: 前端
tags: [CSS,HTML]
---

负margin是CSS布局中非常有效的一种方法，下面先来说说margin的相关知识。

<!--more-->

### margin基础知识

![](http://ww1.sinaimg.cn/large/005zzpHjjw1eh31fp6opqj30g70el0uc.jpg "margin")


上图就是margin的示意图，其基本知识不难理解，可以查阅W3School中的[CSS margin 属性](http://www.w3school.com.cn/cssref/pr_margin.asp)中关于margin的基本讲解。

这里主要介绍布局中margin用法需要理解的知识：

> **当margin四个值都为正数值的话，那么margin按照正常逻辑同周围元素产生边距。当元素margin的top和left是负值时会引起元素的向上或向左位置移动。而当元素margin的bottom和right是负值时会影响右边和下边相邻元素的参考线,即其右边和下边的相邻元素会进入覆盖它的一部分**

以上文字可能不够直观，举个例子吧
```html
<!doctype html>
<html>
<head>
<meta charset="utf-8">

<title>Margin参考线举例说明</title>

<style type="text/css">

*{margin:0;
 padding:0;}

.wrap{width:400px;
 border:5px solid #aaa;}

.example{width:200px;
 height:200px; background:#CCCCFF;}

.normal{width:200px;
 height:200px; background:#CCE8CF;}

/*.example{margin:-10px
 20px -30px 40px;}*/


</style>

</head>

<body>

<div class="wrap">

  <div class="example">example元素：margin参考线举例说明文字，请查看此元素由于margin的变化所移动的位移量。</div>

  <div class="normal">一个普通的Box</div>

</div>

</body>

</html>
```
---

设置margin前后（html代码中CSS注释部分）效果对比
![](http://ww4.sinaimg.cn/large/005zzpUijw1eh31gt67n6j30be0bddge.jpg "no-margin")  ![](http://ww3.sinaimg.cn/large/005zx7JVjw1eh31h68ru4j30bi0aljrx.jpg "and-margin")

<br/>
---
<br/>

### 负margin在布局中的应用

#### 多栏布局中宽度自适应

从上图的对比就可以看出之前文字的意思了，下面介绍负margin在两栏以及三栏布局中的使某栏宽度自适应的用法，先看一个例子,也就是之前那篇博客中**float+负margin（一侧定宽，一侧自动）**的代码。
该例子初看有些复杂，我们先通过几个小例子对比就很容易知道其原理了：

##### box未设置为100%时
```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8" />
<style type="text/css">
.center {float:left;width:800px;background:#ccc;}
.left {float:left;width:170px;background:#aaa;border:1px solid #fff;}
</style>
<title>解释</title>
</head>
<body>
<div class="wrap">
	<div class="center">
		<div class="box">
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间<br />
		</div>
	</div>
	<div class="left">
		浮动<br />
		浮动<br />
		浮动<br />
	</div>
</div>
</body>
</html>
```
<br/>

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8" />
<style type="text/css">
.center {float:left;width:800px;background:#ccc;}
.left {float:left;width:170px;margin-left:-800px;background:#aaa;border:1px solid #fff;}
</style>
<title>解释</title>
</head>
<body>
<div class="wrap">
	<div class="center">
		<div class="box">
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间<br />
		</div>
	</div>
	<div class="left">
		浮动后使用margin左负值<br />
		浮动后使用margin左负值<br />
		浮动后使用margin左负值<br />
	</div>
</div>
</body>
</html>

```
上面两例的效果如下图，下图1只是2个简单的向左浮动的元素，我们知道，浮动的元素，只有在位置不够的时候才会被挤到第二行，所以左例在父级wrap元素宽度足够的情况下，就在一行中并排显示，并且按照他们在html中书写的顺序排列；而图2在给left元素设置`margin-left:-800px;`后,它就会向左平移800px，跑到本行的最左边并且覆盖了box元素的一部分
![图1](http://ww2.sinaimg.cn/large/005zC6zTjw1eh31hy9quxj30ti04wt8t.jpg "float1") ![图2](http://ww3.sinaimg.cn/large/c6d2c180jw1eh31icp6dhj20mh04tjri.jpg "float2")

---
<br/>

##### box设置为100%时

同理，在为中间列也就是`class="box"`设置了`100%`宽度后，它就占据了该行的全部空间，默认情况下会把left元素挤到第二行显示，这时如果为left元素设置了`margin-left:-100%;`left元素就会像之前一样，跑到第一行的最左边，并且覆盖box元素的一部分了，代码和效果展示如下：
```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8" />
<style type="text/css">
.center {float:left;width:100%;background:#ccc;}
.left {float:left;width:170px;background:#aaa;border:1px solid #fff;}
</style>
<title>解释</title>
</head>
<body>
<div class="wrap">
	<div class="center">
		<div class="box">
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间100%<br />
		</div>
	</div>
	<div class="left">
		浮动<br />
		浮动<br />
		浮动<br />
	</div>
</div>
</body>
</html>
```
<br/>

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8" />
<style type="text/css">
.center {float:left;width:100%;background:#ccc;}
.left {float:left;margin-left:-100%;width:170px;background:#aaa;border:1px solid #fff;}
</style>
<title>解释</title>
</head>
<body>
<div class="wrap">
	<div class="center">
		<div class="box">
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间<br />
			中间<br />
		</div>
	</div>
	<div class="left">
		浮动后margin负值<br />
		浮动后margin负值<br />
		浮动后margin负值<br />
	</div>
</div>
</body>
</html>
```
![图3](http://ww3.sinaimg.cn/large/005zpJKJjw1eh31iq2l7gj30kt06fgln.jpg "float3") ![图4](http://ww4.sinaimg.cn/large/005zpK6cjw1eh31j1l3fyj30kt054glp.jpg "float4")

---
<br/>

从上面几个例子就可以理解之前那篇博客中**float+负margin（一侧定宽，一侧自动）**的两栏布局和**float+margin（两侧定宽，中间自适应）**的三栏布局的意义了，**它们都是通过负margin使得写在后面的浮动元素无需因为前面的浮动元素占满了整行而另起一行显示**，`margin-left:-100%;`使其在该行靠左显示，`margin-left:该元素宽度`使其在该行靠右显示（即元素只向左平移等于该元素宽度的距离），剩下的只不过是为上例的box元素添加了一个正值的`margin-left`使其不被负margin元素覆盖从而达到定宽和自适应的效果的。

---
<br/>

#### 去除列表右边框

应用中经常会使用浮动列表展示信息，为了美观通常为每个列表之间设置一定的间距（margin-right）,当父元素的宽度固定式，每一行的最右端的li元素的右边距就多余了，去除的方法通常是为最右端的li添加class，设置margin-right:0; 这种方法需要动态判断为哪些li元素添加class，很麻烦，而利用负margin就可以更简便的实现同样的效果：
```html
<!DOCTYPE HTML>
<html>
<head>
    <meta charset="UTF-8">
    <title> 去除列表右边框 </title>
    <style type="text/css">
       body,ul,li{ padding:0; margin:0;}
       ul,li{ list-style:none;}
       #test{
        width:320px;
        height:210px;
        background:#CCC;
        
    }
    #test ul{
        margin-right:-10px;
        zoom:1;
    }
    #test ul li{
        width:100px;
        height:100px;
        background:#F60;
        margin-right:10px;
        margin-bottom:10px;
        float:left;
    }</style>
</head>
<body>
   <div id="test">
    <ul>
        <li>子元素1</li>
        <li>子元素2</li>
        <li>子元素3</li>
        <li>子元素4</li>
        <li>子元素5</li>
        <li>子元素6</li>
    </ul>
</div>
</body>
</html>
```

效果：
![](http://ww1.sinaimg.cn/large/005zm84ejw1eh31jrhvg0j30ex06idgc.jpg "去除列表右边框")

---
<br/>

#### 负边距+定位：水平垂直居中

使用绝对定位将div定位到body的中心，然后使用负margin（content宽高的一半），将div的中心拉回到body的中心，以到达水平垂直居中的效果。
```html
<!DOCTYPE HTML>
<html>
<head>
    <meta charset="UTF-8">
    <title> 居中 </title>
    <style>
     body{margin:0;padding:0;}
     #test{
        width:200px;
        height:200px;
        background:#F60;
        position:absolute;
        left:50%;
        top:50%;
        margin-left:-100px;
        margin-top:-100px;
    }
    </style>
</head>
<body>
 <div id="test"></div>
</body>
</html>
```
效果：
![](http://ww1.sinaimg.cn/large/005zm8p1jw1eh31k9v5rzj30lp0b9mxl.jpg "居中")

---
<br/>

#### 去除列表最后一个li元素的border-bottom
列表中我们经常会添加border-bottom值，最后一个li的border-bottom往往会与外边框重合，视觉上不雅观，往往要移除。
```html
<!DOCTYPE HTML>
<html>
<head>
    <meta charset="UTF-8">
    <title> 去除列表最后一个li元素的border-bottom </title>
    <style>
      body,ul,li{margin:0;padding:0;}
      ul,li{list-style:none;}
      #test{
        margin:20px;
        width:390px;
        background:#F4F8FC;
        border-radius:3px;
        border:2px solid #D7E2EC;
    }
    #test li{
        height:25px;
        line-height:25px;
        padding:5px;
        border-bottom:1px dotted #D5D5D5;
        margin-bottom:-1px;
    }
    </style>
</head>
<body>
   <ul id="test">
    <li>Test</li>
    <li>Test</li>
    <li>Test</li>
    <li>Test</li>
    <li>Test</li>
    </ul>
</body>
</html>
```
效果：
![](http://ww3.sinaimg.cn/large/005zHPWgjw1eh31ltfz82j30f306h74g.jpg "border-bottom")

---
<br/>

#### 多列等高
此例关键是给每个框设置大的底部内边距，然后用数值相似的负外边距消除这个高度。这会导致每一列溢出容器元素，如果把外包容器的overflow属性设为hidden，列就在最高点被裁切。此方法比之前总结的等高布局容易理解，也基本能应付常用情况，只是由于为外包容器使用了`overflow:hidden;`使得如果在其内部有相对于该容器绝对定位的元素尺寸超过容器时会显示不全的问题，详细解释可以参考[CSS布局奇淫技巧之-多列等高](http://www.cnblogs.com/2050/archive/2012/07/31/2616460.html)这篇博客。
```html
<!DOCTYPE HTML>
<html>
<head>
    <meta charset="UTF-8">
    <title> 居中 </title>
    <style>
     body,p{
        margin:0;
        padding:0;
    }
    #wrap{
        overflow:hidden;
        width:580px;
        margin:0 auto;
    }
    #left,#center,#right{
        margin-bottom:-200px;
        padding-bottom:200px;
    }
    #left {
        float:left;
        width:140px;
        background:#777;
    }
    #center {
        float:left;
        width:300px;
        background:#888;
    }
    #right {
        float:right;
        width:140px;
        background:#999;
    }
    p {color:#FFF;text-align:center}
    </style>
</head>
<body>
    <div id="wrap">
        <div id="left">
            <p style="height:50px">style="height:50px"</p>
        </div>
        <div id="center">
            <p style="height:100px">style="height:100px"</p>
        </div>
        <div id="right">
            <p style="height:200px">style="height:200px"</p>
        </div>
    </div>
</body>
</html>
```
效果：
![](http://ww4.sinaimg.cn/large/005zzpHjjw1eh31m9s4k4j30hd05yt8r.jpg "等高")
