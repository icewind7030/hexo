title: 利用requestAnimationFrame方法来实现canvas动画
date: 2014-07-31 18:32:35
categories: 前端
tags: [canvas,CSS,HTML,Javascript]
---

这几天有个新闻特别火，关于打“老虎”的，网易新闻很迅速的做了一版展示效果既美观又直观的[“老虎家族关系网图解”](http://news.163.com/special/data_zyk/)，一打开就有惊艳的感觉，直观的数据可视化展现，加上炫酷简洁的动画加强，效果非常棒。所以我研究了一下这个网页的代码实现，我的水平离直接编写这么复杂的数据可视化页面肯定还有距离，但是先分解出其中的动画效果学习下总是好的。

<!--more-->

通过分析这个页面的动画实现JS代码发现，它的小圆点沿着人员关系弧线运动这一动画是通过HTML5 新增的`requestAnimationFrame`这一方法来实现的，本文就是模仿简化出该页面的动画原理并加以解释，方便学习。

##示例部分

原文的这部分JS特别长，因为它是通过几个由JS对象组成的数组来存储相关的关系网数据，然后用JS编写了几个类来实现数据的读取和呈现，其数据呈现部分主要就是人物之间的弧线以及沿着该弧线运动的小圆点，下面就是简化其JS后写的canvas动画demo。

###效果预览

<canvas id="can" width="1024" height="300"></canvas>


<script>


	//处理浏览器兼容性，初始化requestAnimFrame函数
	window.requestAnimFrame = (function(){
        return  window.requestAnimationFrame ||
          window.webkitRequestAnimationFrame ||
          window.mozRequestAnimationFrame    ||
          window.oRequestAnimationFrame      ||
          window.msRequestAnimationFrame     ||
          function(/* function */ callback, /* DOMElement */ element){
          window.setTimeout(callback, 1000 / 60);
          };
    })();


    var canvas = document.getElementById("can");
	var main = canvas.getContext("2d");
	var time = 0;

	//圆心坐标，半径，步数
	var x = 500;
	var y = 200;
	var r = 150;
	var n = 200;

	//小圆点行走路径x,y坐标
	var xpath = new Array();
	var ypath = new Array();


	//计算小圆点行走路径坐标
	for(var i = 0; i < n; i++){
		xpath[i] = x + Math.cos(-i/n*Math.PI)*r;
		ypath[i] = y + Math.sin(-i/n*Math.PI)*r;
	}

	(function draw () {
		requestAnimFrame(draw);
		//清空当前画布，否则小圆点的路径会在画图上连接成一条更粗的线
		main.clearRect(0, 0, canvas.width, canvas.height);

		//设置画布线条宽度，即弧线宽度
		main.lineWidth = 1;


		//绘制弧线
		main.strokeStyle = "blue";
		main.beginPath();
		main.arc(x, y, r, Math.PI, 0);
		main.stroke();

		//绘制活动小圆点
		main.fillStyle = "red";
		main.beginPath();
		main.arc(xpath[time], ypath[time], 3, Math.PI * 2, 0);
		main.fill()

		//控制循环
		time++
		if(time==n){
			time = 0;
		}

	})()
</script>

###主要代码:

```html
<canvas id="can" width="1024" height="768"></canvas>
<script>
	//处理浏览器兼容性，初始化requestAnimFrame函数
	window.requestAnimFrame = (function(){
        return  window.requestAnimationFrame ||
          window.webkitRequestAnimationFrame ||
          window.mozRequestAnimationFrame    ||
          window.oRequestAnimationFrame      ||
          window.msRequestAnimationFrame     ||
          function(/* function */ callback, /* DOMElement */ element){
          window.setTimeout(callback, 1000 / 60);
          };
    })();
    var canvas = document.getElementById("can");
	var main = canvas.getContext("2d");
	var time = 0;
	//圆心坐标，半径，步数
	var x = 500;
	var y = 200;
	var r = 150;
	var n = 200;
	//小圆点行走路径x,y坐标
	var xpath = new Array();
	var ypath = new Array();
	//计算小圆点移动路径坐标
	for(var i = 0; i < n; i++){
		xpath[i] = x + Math.cos(-i/n*Math.PI)*r;
		ypath[i] = y + Math.sin(-i/n*Math.PI)*r;
	}
	(function draw () {
		requestAnimFrame(draw);
		//清空当前画布，否则小圆点的路径会在画图上连接成一条更粗的线
		main.clearRect(0, 0, canvas.width, canvas.height);
		//设置画布线条宽度，即弧线宽度
		main.lineWidth = 1;
		//绘制弧线
		main.strokeStyle = "blue";
		main.beginPath();
		main.arc(x, y, r, Math.PI, 0);
		main.stroke();
		//绘制活动小圆点
		main.fillStyle = "red";
		main.beginPath();
		main.arc(xpath[time], ypath[time], 3, Math.PI * 2, 0);
		main.fill()
		//控制循环
		time++
		if(time==n){
			time = 0;
		}
	})()
</script>
```

##关于`requestAnimationFrame`

###什么是`requestAnimationFrame`
在过去,为了使用JavaScript脚本代码实现动画,一般都是使用定时器来指定每隔一段时间使页面显示效果产生一些变化，这对于简单或对流畅性要求不高时不会有什么问题，但现在随着对用户体验的关注度不断提高，对动画的复杂程度和流畅性都有了更高的要求，传统动画显得捉襟见肘了。于是,在HTML 5有了一个新的window.requestAnimFrame方法，提供了一个统一帧管理、提供监听帧的API。

###`requestAnimationFrame`的优点
通过window.requestAnimFrame方法的利用,浏览器可以具有将各种并发性动画结合入一个单一的页面进行创建及渲染的能力,这种能力将使得动画的实现具有更好的性能。例如,可以同时执行使用JavaScript脚本代码实现的动画与使用CSS中的transition样式属性实现的动画。另外,通过window.requestAnimFrame方法的使用,当用户将浏览器标签窗口切换到其他标签窗口时,当前页面中的动画将被暂停运行,以减少CPU、GPU与内存的消耗。

###`requestAnimationFrame`的用法
调用`requestAnimationFrame`函数，为其传递一个callback函数，则在下一个动画帧时，会调用callback。

```javascript
//浏览器兼容
window.requestAnimFrame = (function(){
    return  window.requestAnimationFrame       || 
            window.webkitRequestAnimationFrame || 
            window.mozRequestAnimationFrame    || 
            window.oRequestAnimationFrame      || 
            window.msRequestAnimationFrame     || 
            function( callback ){
                window.setTimeout(callback, 1000 / 60);//定义每秒执行60次动画
            };
})();
//相当于使用setInterval(render, 16)方法,但是具有更高的性能
(function animloop(){
    requestAnimFrame(animloop);
    render();
})();
```

这篇网易新闻页面就是考虑到该网页动画效果较多，为了降低资源消耗尤其是用户把本新闻页切换到后台后时的资源消耗，从而使用的`requestAnimationFrame`方法。

参考文章：
- [requestAnimationFrame 动画接口 - 司徒正美](http://www.cnblogs.com/rubylouvre/archive/2011/08/22/2148797.html)
- [利用requestAnimationFrame实现智能动画 - HTML5在线](http://html5online.com.cn/articles/2012081102.html)