title: Javascript中的undefined与null
date: 2014-06-04 21:17:36
tags: [Javascript]
categories: 前端
---

最近看到这个问题，查阅了一些相关的资料和博客，加上我的理解，稍微做下归纳，不对之处还望指正。

<!--more-->

大多数计算机语言，有且仅有一个表示"无"的值，比如，C语言的NULL，Java语言的null，Python语言的None，Ruby语言的nil。而JavaScript有两个表示"无"的值：undefined和null，那么它们有什么异同，都分别应用于什么场合呢？

##两者的异同

undefined和null,作为ECMAScript中的两种简单数据类型，都有“无”的意思，在if语句中，都会被自动转换成false，相等运算符也报告两者相等：
```javascript
if (!undefined) 
    console.log('undefined is false');
// undefined is false

if (!null) 
    console.log('null is false');
// null is false

undefined == null
// true
```

但是，暂且不讨论这两个类型最初设定时的初衷是什么，有没有错误，现在公认的理解是：
- undefined表示"缺少值"，就是此处应该有一个值，但是还没有定义
- null表示"空对象指针"，即该处不应该有值

可以认为undefined代表一个意想不到的没有值而null作为预期没有值的代表。

使用全等操作符比较两者时，也说明了两者并不是完全相同的：
```javascript
undefined === null; // false
```

下面就两者的不同用法或者说产生两种类型值的不同情形进一步比较；

##产生undefined的情形

有许多的方法产生一个undefined值的代码。它通常遇到当试图访问一个不存在的值时。在这种情况下，在JavaScript这种动态的弱类型语言中，只会默认返回一个undefined值，而不是上升为一个错误。

- 任何声明变量时没有提供一个初始值，都会有一个为undefined的默认值:
```javascript
var foo; // 默认值为 undefined
```
- 当试图访问一个不存在的对象属性或数组项时，返回一个undefined值:
```javascript
var array = [1, 2, 3];
var foo = array.foo; // foo 属性不存在, 返回 undefined
var item = array[5]; // 数组中没有索引为5的项，返回 undefined
```
- 如果省略了函数的返回语句,返回undefined:
```javascript
var value = (function(){})(); // 返回 undefined
```
- 函数调用时未提供的值结果将为undefined参数值：
```javascript
(function(undefined){
    // 参数是 undefined
})();
```
- 最后，undefined是一个预定义的全局变量(不像null关键字)，并初始化为undefined值:
```javascript
'undefined' in window; // true
```
ECMAScript 5中，这个变量是只读的，以前并非如此。

<br>

##null的用法

从逻辑角度看，null值表示一个空对象指针，如果函数或方法要返回的是对象，那么找不到该对象时，返回的通常是 null

- 如果定义的变量准备在将来用于保存对象，那么最好将该变量初始化为null而不是其他值。这样一来，只要检查null值就可以知道相应的变量是否已经保存了一个对象的引用；
```javascript
var foo = null;
if (foo != null){
	//对foo对象执行某些操作
}
```
- DOM操作中，如果试图获取一个不存在的元素，则返回一个null值
```javascript
var dom = document.getElementById('domId');//null
```
- 当一个引用不再是必需的时，通过为其分配null值，有效地清除引用，并假设对象没有引用其他代码，就可以指定垃圾收集，确保回收内存。
```javascript
var foo = function(){};
foo = null;
```
- 对象原型链的终点
```javascript
Object.getPrototypeOf(Object.prototype)
// null
```


所以，虽然undefined和null意义相像，但是实际上还是有很大不同的，无论在什么情况下都没有必要把一个变量的值显示地设置为undefined，可是同样的规则对null却不适用，这也是为什么针对undefined我写的标题是**产生undefined的情形**而null才用的是**null的用法**。

##如何检测它们？
了解到了二者在用法上的差异后，现在不禁要问：如何检测这两个类型呢？因为知道二者用`==`进行判断是得不出所需结果的，而`===`虽然能成功用于比较，但是这显然也不是我们想看到的类型检测方法。而`typeof()`方法虽然能检测出undefined类型，但是对于null类型,该方法的返回值则为"object"，所以`typeof()`也不是合适的检测方法。

在ECMAScript5中，有一个`Object.prototype.toString`方法，可以成功的进行类型检查。
```javascript
var a = undefined;
var b = null;
console.log(typeof(a));//undefined
console.log(Object.prototype.toString.call(a));//[object Undefined] 
console.log(typeof(b));//object
console.log(Object.prototype.toString.call(b));//[object Null]
```

ECMA-262 写道

> Object.prototype.toString( ) When the toString method is called, the following steps are taken: 
1. Get the [[Class]] property of this object. 
2. Compute a string value by concatenating the three strings “[object “, Result (1), and “]”. 
3. Return Result (2)

上面的规范定义了`Object.prototype.toString`的行为：首先，取得对象的一个内部属性[[Class]]，然后依据这个属性，返回一个"[object xxx]"格式的字符串作为结果。

利用这个方法，再配合`call`，我们可以取得任何对象的内部属性[[Class]]，然后把类型检测转化为字符串,最后通过和预想的字符串比如"[object Null]"比较以检测其类型，其中`call`改变`toString`的this引用为待检测的对象。

---

参考阅读：
- [阮一峰的网络日志——undefined与null的区别](http://www.ruanyifeng.com/blog/2014/03/undefined-vs-null.html)
- [颜海镜——探索JavaScript中Null和Undefined的深渊](http://www.cnblogs.com/yanhaijing/p/3505291.html)
- [Exploring the Abyss of Null and Undefined in JavaScript](http://flippinawesome.org/2013/12/09/exploring-the-abyss-of-null-and-undefined-in-javascript/)


