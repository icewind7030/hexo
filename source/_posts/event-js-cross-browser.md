title: 跨浏览器的事件对象JS代码
date: 2014-05-15 16:16:47
tags: [Javascript]
categories: 前端
---

整理的跨浏览器事件处理相关JS代码

<!--more-->
```javascript
//跨浏览器的事件对象JS代码
 var EventUtil = {
 	//事件处理程序,3个参数：要操作的元素，事件名称，事件处理程序函数
 	addHandler: function  (element, type, handler) {
 		if(element.addEventListener){
 			element.addEventListener(type, handler, false);//DOM2级事件处理程序,false表示事件冒泡阶段调用事件处理程序
 		}else if(element.attachEvent){
 			element.attachEvent("on" + type, handler);//IE事件处理程序，记得加“on”
 		}else {
 			element["on"+type] = handler;
 		}
 	},
 	removeHandler: function  (element, type, handler) {
 		if(element.removeEventListener){
 			element.removeEventListener(type, handler, false);
 		}else if(element.detachEvent){
 			element.detachEvent("on"+ type, handler);
 		}else{
 			element["on"+type] = null;
 		}
 	},

 	//获得event对象
 	getEvent: function  (event) {
 		return event ? event : window.event;//主要是为了IE中，当用DOM0级方法添加事件处理程序时，event对象是作为windows对象的一个属性存在
 	},

 	//获得事件的目标
 	getTarget: function  (event) {
 		return event.target || event.srcElement;
 	}，

 	//取消事件的默认行为
 	preventDefault: function  (event) {
 		if(event.preventDefault){
 			event.preventDefault();
 		}else{
 			event.returnValue = fasle;//IE
 		}
 	}

 	//阻止事件冒泡,IE不支持事件捕获，所以只能取消事件冒泡
 	stopPropagation: function  (event) {
 		if(event.stopPropagation){
 			event.stopPropagation();
 		}else{
 			event.cancelBubble = true;
 		}
 	}

 };

 //事件委托
 var list = document.getElementById("myLinks");

 EventUtil.addHandler(list, "click", function  (event) {
 	event = EventUtil.getEvent(event);
 	var target = EventUtil.getTarget(event);

 	switch(target.id){
 		case "1": document.title = "I change the document's title";
 		break;

 		case "2": location.href = "http://www.wrox.com";
 		break;

 		case "3": alert('xxx');
 		break;
 	}
 });
 ```