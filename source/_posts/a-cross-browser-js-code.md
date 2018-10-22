title: 一段跨浏览器事件绑定代码
date: 2014-05-22 10:13:43
categories: 前端
tags: [Javascript]
---

今天看一位前辈的博客看到一段跨浏览器为Dom元素绑定事件监听函数的代码，初看没能看懂，再通过看原作者的进一步解释后理解了该代码的意义，现分享出来。

<!--more-->

**作者原文地址——[Flexible Javascript Events](http://ejohn.org/projects/flexible-javascript-events/)**

代码如下：

```Javascript
function addEvent( obj, type, fn ) {
  if ( obj.attachEvent ) {
    obj['e'+type+fn] = fn;
    obj[type+fn] = function(){obj['e'+type+fn]( window.event );}
    obj.attachEvent( 'on'+type, obj[type+fn] );
  } else
    obj.addEventListener( type, fn, false );
}
function removeEvent( obj, type, fn ) {
  if ( obj.detachEvent ) {
    obj.detachEvent( 'on'+type, obj[type+fn] );
    obj[type+fn] = null;
  } else
    obj.removeEventListener( type, fn, false );
}
```

非IE浏览器直接用`obj.addEventListener( type, fn, false );`即可，主要看IE浏览器部分：

`obj['e'+type+fn] = fn;`原作者解释如下： 
> This makes the function a child of the specified object. The key, which is placed in the object hash, is (hopefully) unique and won’t collide with any other function additions.

意思是为`obj`对象添加一个尽可能不重复的属性key——`['e'+type+fn]`,并将其value赋值为事件处理程序的引用`fn`;

`obj[type+fn] = function(){obj['e'+type+fn]( window.event );}`原作者解释如下：
> This line creates an anonymous function who, once executed, will fire the previously attached function – passing it the global event object. This whole function is being attached to the object so that it can be removed later, using the removeEvent() function.

意思是该语句创建了一个匿名函数，匿名函数内部是之前的`obj['e'+type+fn]`,即`fn`,该匿名函数一旦触发，就会用`window.event`为`fn`传参并运行。
这个匿名函数添加在`obj`的`[type+fn]`上，为的是之后可以方便的被移除函数`removeEvent()`移除;

最后用`obj.attachEvent( 'on'+type, obj[type+fn] );`将`obj[type+fn]`绑定在obj上。

该事件绑定代码意在使得IE浏览器下Dom元素绑定事件处理函数时,使用正确的`window.event`作为`attachEvent`中`function`的`event`参数。
