title: 反转DOM元素子节点顺序
date: 2014-10-20 15:38:28
tags: [Javascript,HTML]
categories: 前端
---

在微博上看到寒冬winter大神抱怨前端招聘的困难，其中提到了一道Javascript题目，
> 如何反转一个DOM元素的所有子节点的顺序

就这么个问题，看似简单，想了半天发现自己确实还做不好这题，想着利用数组的reverse方法，而完全没有充分利用到Javascript的原生DOM操作，用中文百度了一番，居然完全搜不到类似结果，还是用英文搜google靠谱...

<!-- more -->

本问题的解答是在[stackoverflow-Reverse order of a set of elements](http://stackoverflow.com/questions/7942733/reverse-order-of-a-set-of-elements)上找到，这个社区真是太强太全面了,下面就搬运一下顺便讲讲这个问题下面的让我进一步学习到原生Javascript DOM操作知识的一些改进讨论。

有一个回答是这样的：
```javascript
function reverseChildNodes(node) {
    var parentNode = node.parentNode, nextSibling = node.nextSibling,
        frag = node.ownerDocument.createDocumentFragment();
    parentNode.removeChild(node);
    while(node.lastChild)
        frag.appendChild(node.lastChild);
    node.appendChild(frag);
    parentNode.insertBefore(node, nextSibling);
    return node;
}
reverseChildNodes(document.getElementById('con'));
```
这个方法初看之下确实已经很简洁了，比我考虑的什么利用数组的reverse方法什么的实际多了，其主要思想是：
> 利用`DocumentFragment`的插入document时,实际插入只是其全部子节点这一特性，来暂时储存循环倒序的子节点。

1.先将要倒序的DOM元素的父节点以及紧邻的同级及节点储存下来
2.然后将DOM元素从其父节点移除
3.再把储存了倒序节点的`DocumentFragment`放入原DOM元素中，再用`inserBefore()`方法插入原位置

该回答的讨论中另两位朋友却提出了更简洁的方法：
```javascript
function reverseChildNodes(node) {
    var frag = node.ownerDocument.createDocumentFragment();
    while(node.lastChild)
        frag.appendChild(node.lastChild);
    node.appendChild(frag);
}
reverseChildNodes(document.getElementById('con'));
```
看完这个解答，我才想起来`appendChild()`的更完整的用法:
> appendChild()`方法不只是可以向document的某节点插入一个手动创建的节点，还可以将document中已经存在的节点移动到所需的插入位置。

上面的这个解答很好的利用了这一特性从而大大简化了代码。

从这个题目的解答中，发现确实缺乏的DOM原生Javascript操作的系统知识，确实可能离阿里的要求还有不少距离，不管怎么样，继续学习吧！