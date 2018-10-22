title: 关于JSONP的理解
date: 2014-05-14 21:32:12
categories: 前端
tags: [JSONP,AJAX,Javascript]
---

JSONP是一种跨域AJAX通信技术，根据同源策略，浏览器将限制客户端的JavaScript在一个安全沙箱内，通常JS不能直接同一台不同的域的远程服务器通信。

<!--more-->

JSONP是JSON with padding （填充式JSON或参数式JSON）的简写
它由两部分组成：回调函数和数据。回调函数是当相应到来时应该在页面中调用的函数，回调函数的名字一般在请求中指定，数据就是传入回调函数中的JSON数据。


JSONP通过动态创建script标签，利用script标签中的src属性可以指向跨域的js文件url的特点，在src属性末尾加上数据回传后的回调函数（预先定义好，回调函数中对传回的数据进行处理），最后通过将动态创建的script标签添加到document中完成整个JSONP通信过程。

因为JSONP是有效的javascript代码，所以在请求完成后，即在JSONP响应加载到页面中以后，就会立即执行
```javascript
function handleResponse(response){
     alert(response.ip);
};

var script = document.createElement("script");
script.src = "http://abc.com/json/?callback=handleResponse";
document.body.insertBefore(script, document.body.firstChild);
```


*此外，jquery ajax中，dataType为jsonp时，只能使用get方式通信，因为用的是动态script标签*

---

*6月10日补充:*

发现一篇非常好的解释AJAX和JSONP区别的博文[ajax 和jsonp 不是一码事 细读详解](http://blog.csdn.net/superhosts/article/details/9057301)，讲的不能再清楚了，强烈推荐。
