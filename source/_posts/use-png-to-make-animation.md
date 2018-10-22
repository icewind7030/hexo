title: 使用PNG图片来实现简单的动画
date: 2016-01-04 16:13:11
tags: [CSS,CSS3]
categories: 前端
---
最近的开发中碰到了一个显示loading状态的GIF图标的问题，因为GIF格式的限制，无法做到很好的半透明，这使得GIF格式的loading图标在某些场景中的显示效果非常粗糙，别说设计看了很不满意，我自己看了都觉得别扭。

<!--more-->

<style>
	.heart{
		width: 100px;
		height: 100px;
		margin: 100px;
		border: 1px solid #ddd;
		position: relative;
		display: inline-block;
		background-color: blue;
	}
	.heart:after{
		content: "";
		position: absolute;
		left: 0;
		top: 0;
		width:100px;
		height: 100px;
		background: url(http://easyread.nos.netease.com/web/trunk/1451899590845/web_heart_animation.png) 0 0 no-repeat;
		-webkit-animation: heart 1s steps(28) infinite;
		animation: heart 1s steps(28) infinite;
	}
	@-webkit-keyframes heart{
	    0% {
	        background-position: center left;
	    }
	    100% {
	        background-position: center right;
	    }

	}
	@keyframes heart{
	    0% {
	        background-position: center left;
	    }
	    100% {
	        background-position: center right;
	    }

	}
	.loading{
		width: 64px;
		height: 64px;
		margin: 100px;
		border: 1px solid #ddd;
		position: relative;
		display: inline-block;
		background-color: blue;
	}
	.loading:after{
		content: "";
		position: absolute;
		left: 0;
		top: 0;
		width:64px;
		height: 64px;
		background: url(http://easyread.nos.netease.com/web/trunk/1451899609735/loading.png) 0 0 no-repeat;
		-webkit-animation: loading 1s steps(19) infinite;
		animation: loading 1s steps(19) infinite;
	}
	@-webkit-keyframes loading{
	    0% {
	        background-position: left;
	    }
	    100% {
	        background-position: right;
	    }

	}
	@keyframes loading{
	    0% {
	        background-position: left;
	    }
	    100% {
	        background-position: right;
	    }

	}
	.value-test{
		width: 100px;
		height: 100px;
		text-align: center;
		line-height: 100px;
		margin: 100px;
		display: inline-block;
	}
	.value-test.start{
		-webkit-animation: colorChange 2s steps(1,start) infinite;
		animation: colorChange 2s steps(1,start) infinite;
	}
	.value-test.end{
		-webkit-animation: colorChange 2s steps(1,end) infinite;
		animation: colorChange 2s steps(1,end) infinite;
	}
	@-webkit-keyframes colorChange{
		0%{background: red;}
		50%{background: yellow;}
		100%{background: blue;}
	}
</style>

之后设计干脆给了40张如下这样的关键帧图，因为native是可以根据这些关键帧做成动画的，那么我也需要将它们做成一个流畅的loading动画。
![](http://ww2.sinaimg.cn/large/7d25d640gw1eznjgvwon0j20me0f8af1.jpg)
开始我的思路是用CSS的animation的keyframes各个百分比指定一张背景图片，我挑了20张，结果真机上一测试，动画跑起来的时候闪的厉害，因为动画初期需要在很短的时间内加载几十张图片，这个思路显然是不行的。

上面的方法是为了偷懒节省拼合图片的工作量而想出的，结果证明是不可行的，因为这些图片即使是储存在本机上，不需要HTTP请求，在动画初期依然存在读取图片而造成闪动问题。

所以想省工作量的愿望就破灭了，设计有其他的任务，我又等着开工，只能自己来将几十张图片拼合在一起，因为图片拼合后只需要读取一次即可，闪动的问题就能解决了。(*这里有个问题，PS拼合类似这种尺寸一样的图片有没有什么快速的方式，因为我是全程手动完成的，希望有知道的说一下。*)

因为人眼的视觉暂留特性，24帧的动画就已经足够流畅了，所以这里我间隔着挑选了40张关键帧中的20张，后面的示例中可以看到效果。

然后，我又回想起了前几天看到的阿里的前端专家[一丝的微博](http://weibo.com/1397442732/D3bpH4Kbk?type=comment#_rnd1451898544591)中介绍的Twitter的新版“喜欢”动画的实现方式，觉得完全可以应用在这个场合。

直接上代码和效果图:
```html
<!doctype html>
<html>
<head>
	<meta charset="utf-8">
	<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no">
    <title>loading 动画实验</title>
    <style>
    	.heart{
    		width: 100px;
			height: 100px;
			margin: 200px auto;
			border: 1px solid #ddd;
			position: relative;
			background-color: blue;
    	}
		.heart:after{
			content: "";
			position: absolute;
			left: 0;
			top: 0;
			width:100px;
			height: 100px;
			background: url(http://easyread.nos.netease.com/web/trunk/1451899590845/web_heart_animation.png) 0 0 no-repeat;
			-webkit-animation: heart 1s steps(28) infinite;
			animation: heart 1s steps(28) infinite;
		}
		@-webkit-keyframes heart{
		    0% {
		        background-position: center left;
		    }
		    100% {
		        background-position: center right;
		    }

		}
		@keyframes heart{
		    0% {
		        background-position: center left;
		    }
		    100% {
		        background-position: center right;
		    }

		}
		.loading{
    		width: 64px;
			height: 64px;
			margin: 200px auto;
			border: 1px solid #ddd;
			position: relative;
			background-color: blue;
    	}
		.loading:after{
			content: "";
			position: absolute;
			left: 0;
			top: 0;
			width:64px;
			height: 64px;
			background: url(http://easyread.nos.netease.com/web/trunk/1451899609735/loading.png) 0 0 no-repeat;
			-webkit-animation: loading 1s steps(19) infinite;
			animation: loading 1s steps(19) infinite;
		}
		@-webkit-keyframes loading{
		    0% {
		        background-position: left;
		    }
		    100% {
		        background-position: right;
		    }

		}
		@keyframes loading{
		    0% {
		        background-position: left;
		    }
		    100% {
		        background-position: right;
		    }

		}
    </style>   
</head>
<body>
	<div class="heart"></div>
	<div class="loading"></div>
</body>
</html>
```
<div class="heart"></div><div class="loading"></div>

以上就是效果图，可以看到，这种实现方式既结合了PNG格式透明度的灵活性,不再受限于动画的背景颜色，又使用了`animation-timing-function`的`steps()`方法成功精简了代码，省去了繁琐的`background-position`的一一编写，非常的有效而优雅。

**关于steps()**:

它是CSS3  `animation-timing-function`中的一个值，但是W3School上却把它给漏了：
![](http://ww3.sinaimg.cn/large/7d25d640gw1eznlukfh8dj20jx096mz3.jpg)
区别于其他的线性变化的动画时间函数，`steps()`指定的是一个阶跃函数，它有两个参数：
- 第一个参数指定了时间函数中的间隔数量（必须是正整数）
- 第二个参数可选，接受`start`和`end`两个值，指定在每个间隔的起点或是终点发生阶跃变化，默认为`end`
- 可以为step-start，它等同于steps(1,start)，动画分成1步，动画执行时为开始左侧端点的部分为开始
- 也可以为step-end，等同于steps(1,end)：动画分成一步，动画执行时以结尾端点为开始，默认值为end

还有需要注意的是，`steps()`的第一个参数number指的是间隔数，即把一个动画分为几个阶段，而不是类似`keyframes`中的变化次数，这就是为什么上面的loading动画我取了20帧，`steps()`中的第一个参数设置为19的原因。一开始我的理解也错了，设置了20，导致动画是错乱的。

至于第二个参数的意义，可以借用W3C上的一张示意图来理解:
![](http://ww2.sinaimg.cn/large/7d25d640gw1eznnnpz42tj20e30ed0u5.jpg)
由上图可知，这个参数规定的动画的每个阶段中，是用上一帧的图片来展示还是用下一帧的图片展示，即：
- `start`表示在动画变化过程中，使用下一帧的效果来填充这个动画阶段
- `end`表示是动画变化过程中，使用上一帧的效果来填充本动画阶段

再举个例子以便更容易的理解
```CSS
.value-test{
	width: 100px;
	height: 100px;
	text-align: center;
	line-height: 100px;
	margin: 100px;
}
.value-test.start{
	-webkit-animation: colorChange 2s steps(1,start) infinite;
	animation: colorChange 2s steps(1,start) infinite;
}
.value-test.end{
	-webkit-animation: colorChange 2s steps(1,end) infinite;
	animation: colorChange 2s steps(1,end) infinite;
}
@-webkit-keyframes colorChange{
	0%{background: red;}
	50%{background: yellow;}
	100%{background: blue;}
}
```
<div class="value-test start">start</div><div class="value-test end">end</div>

从上面的两个图可以看到，设置了`steps(1,start)`值的方块，是由黄色变为蓝色，而设置了`steps(1,end)`值的方块，是由红色变为黄色的，这样就好理解了。