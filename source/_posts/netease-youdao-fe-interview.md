title: 记网易有道前端面试一面
date: 2014-05-16 17:14:39
categories: 前端
tags: [Javascript,CSS,HTML]
---

昨天到宇宙中心五道口即清华大学旁边面试了网易有道的前端，记录一下面试所问的问题以及解答。

<!--more-->

1. ##Javascript事件绑定##

  看到我项目经历中有用到jquery,本来准备问我个jQuery事件绑定问题，但是了解到我的那个项目做的比较早，用的jQuery版本比较老，就改问我原生的JS事件绑定了，让我写代码，并照顾到浏览器兼容性，我照记忆中的写了一下，但是对于`addEventlistener`方法的最后一个参数的作用好像记反了，而`attachEvent`只有2个参数我也搞混了，面试官提醒了我一下。
————*题目解答可以参考我之前的那篇[跨浏览器的事件对象JS代码](http://icewind-blog.com/2014/05/15/%E8%B7%A8%E6%B5%8F%E8%A7%88%E5%99%A8%E7%9A%84%E4%BA%8B%E4%BB%B6%E5%AF%B9%E8%B1%A1js%E4%BB%A3%E7%A0%81/)*
2. ##JS原生AJAX实现##

    手写代码，着重问了下open方法最后个参数的意义
	```javascript
	//XHR（XMLHttpRequest）1级方法
	/*
	 *新建XHR对象的函数
	 *如果只想支持IE7及更高版本，可以只用原生的XHR实现
	 *即var xhr = new XMLHttpRequest();
	 */
	function creatXHR () {
		if(typeof XMLHttpRequest != "undefined"){
			return new XMLHttpRequest();    //原生XHR对象
		}else if(typeof ActiveXObject != "undefined"){
			if(typeof arguments.callee.activeXString != "string"){
				var versions = ["MSXML2.XMLHttp.6.0", "MSXML2.XMLHttp.3.0", "MSXML2.XMLHttp"],i,len;
				for(i=0, len=versions.length; i < len; i++){
					try{
						new ActiveXObject(versions[i]);
						arguments.callee.activeXString = versions[i];
						break;
					}catch(ex){
						//处理错误
					}
				}
			}
			return new ActiveXObject(arguments.callee.activeXString);//MSXML库中的ActiveX对象
		}else{
			throw new Error("No XHR object availabel.")
		}
	}
	function submitData () {
		var xhr = creatXHR();//新建XHR对象
		xhr.onreadystatechange = function  () {
			if(xhr.readystate == 4){
				//前一条件代表接收到全部相应数据，而且可以在客户端使用了
				//后一条件表示请求的资源没有修改，可以直接使用浏览器中缓存的版本
				if ((xhr.status >= 200 && xhr.status < 300)|| xhr.status == 304){
					alert(xhr.responseText);//处理接收到的响应
				}else{
					alert("Request was unsuccessful: " + xhr.status);//请求不成功
				}
			}
		};
		//get方法,名称和值都必须使用encodeURIComponent(),true代表异步发送请求
		xhr.open("get", "example.txt?name1=value1&name2=value2", true);
		xhr.send(null);
		//post方法
		xhr.open("post", "postexample.php", true);
		xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");//模仿表单提交
		var form = document.getElementById("user-info");
		xhr.send(serialize(form));
	}

	//XMLHttpRequest 2级方法
	var xhr = creatXHR();
	xhr.onload = function () {
		if((xhr.status >= 200 && xhr.status < 300)|| xhr.status == 304){
			alert(xhr.responseText);
		}else{
			alert("Request was unsuccessful: " + xhr.status);//请求不成功
		}
	};
	xhr.open("get", "altevents.php", true);
	xnr.send(null);
	```

3. ##JS闭包的理解##
  这个只在书上和一些文章中学过，但是实际应用还没有，就让我说了一下对于闭包的理解。

  ###什么是闭包:###
   当内部函数 在定义它的作用域的外部被引用时,就创建了该内部函数的闭包,如果内部函数引用了位于外部函数的变量,当外部函数调用完毕后,这些变量在内存不会被 释放,因为闭包需要它们.

4. ##JS数据类型##

  **基本数据类型**:  Undefined、Null、Boolean、Number、String
  **复杂数据类型**:  Object

5. ##CSS几种position属性##
  先是让我列举出所知的CSS position属性
  ![](/img/position.jpg "position")
  又继续问了多重嵌套并且使用了不同position属性的多个div定位问题，即块包含问题，这个答的不好，待整理。

  [CSS中的Position属性](http://www.cnblogs.com/rainman/archive/2009/04/12/1433988.html)这篇博客关于CSS中position属性的相关知识和要点讲的非常通俗易懂，推荐参考！

6. ##CSS盒模型##
  这个我照着看过的一本书画的，结果面试告诉我我画只适用于IE6下，然后最后我问了下面试官盒模型应该怎么画，面试官就告诉我，照W3C标准的话，有padding box ,boder box, margin box等各种说法，W3C的盒模型width和IE6下盒模型的width不一样，要想取得IE6下盒模型的width需要使用属性box-sizing属性。

  **通过这题我才发现我对盒模型的了解是多么的浅显，下面是我整理的相关资料，以作警示。**
  ###标准盒模型与IE盒模型的区别###
  ![](/img/W3C.jpg "W3C盒模型")
  ![](/img/IE.jpg "IE盒模型")

  **如果在页面开头写了`<!doctype...` 所有浏览器都将使用标准盒模型，如果没有写,那么在IE下就将会使用IE盒模型解析盒子,即IE的怪异模式。**

  ###CSS3 box-sizing知识###
		box-sizing: content-box|border-box|inherit;
  ![](/img/box-sizing.jpg "box-sizing")
7. ##两列布局##
  这个答的不好，有待整理，这次面试确实暴露了CSS方面的缺点，学的很差，**急需加强！**

  整理了相关的知识，参看[两栏布局，三栏布局，等高布局，流式布局](http://icewind-blog.com/2014/05/26/%E4%B8%A4%E6%A0%8F%E5%B8%83%E5%B1%80%EF%BC%8C%E4%B8%89%E6%A0%8F%E5%B8%83%E5%B1%80%EF%BC%8C%E7%AD%89%E9%AB%98%E5%B8%83%E5%B1%80%EF%BC%8C%E6%B5%81%E5%BC%8F%E5%B8%83%E5%B1%80/)
8. ##最近正在学习的前端方面的知识##
  我谈了下我最近正在学习用github pages搭建博客，问我会不会Markdown语法，我说会一点。
  我又提了下更早前稍微看了看node.js.但是由于事情耽误了就没继续看下去，于是让我介绍了一下对node.js的了解，我说了一下，但是说的很表面，博客搭差不多了准备继续学习node.js，学无止境啊！
9. ##想问的问题##
  问了面试官在网易有道负责开发的产品，得知是惠惠购物助手，而我正好在chrome上有使用，就顺便问了下惠惠助手鼠标停留在商品预览上就能很快展示出商品的价格走势图是通过什么技术，面试官告诉我其实没有用预读取技术，只是实时的进行数据请求，因为所需的数据量并不大，但是一旦显示过该商品的价格走势图后，就会将该部分数据储存在本地，以便再次查看，节省带宽且提升速度。
  本来还可以再问问为什么有的时候有降价通知按钮可以订阅商品的降价通知，而大部分商品没有，结果当时没想到这个问题，有点遗憾，希望下次还有机会了解。