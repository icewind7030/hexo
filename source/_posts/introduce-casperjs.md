title: "CasperJS介绍"
date: 2017-07-19 17:14:00
tags: [Javascript, Node]
categories: Node
---

本文主要介绍的是使用基于`PhantomJS`的`CasperJS`实现一些之前需要在网页端手动实现的操作。

<!--more-->

之前开发重构云阅读客户端资讯模板的时候，有一部分其实是并没有很好的实现，就是webpack打包完成后自动将生成的模板zip包上传到我们的管理后台网站上。因为出于安全原因，只能开放登陆用户的上传权限，对于未登陆用户，最多只能开发测试环境的上传权限。所以，当时的线上环境的模板zip包还是需要我自己一个个去手动上传的，这一直是我想要解决的问题，最近终于有点时间来研究一下。

## PhantomJS
> PhantomJS is a headless WebKit scriptable with a JavaScript API. It has fast and native support for various web standards: DOM handling, CSS selector, JSON, Canvas, and SVG.

之前我在学习中就听说过`PhantomJS`这个工具，简单来说，它实现的就是一个无界面的浏览器，它使得我们通过编写`NodeJS`脚本来模拟浏览器访问网页，从而做到抓取页面信息或者其他的一些测试。

虽然它的基础功能足够强大，但是它的API对于我来说比较简陋，比如我想访问某一个网页，如果在未登陆状态下，会需要先跳转到登陆页，输入账号密码进行登陆以后才能访问到我们的目的页。这个场景，`PhantomJS`虽然也能实现，但是由于相关能够使用的API只有`onNavigationRequested`或者`onUrlChanged`, 这些API都只能知道什么时候页面跳转触发了，但是什么时候跳转完成了，没有API可以知道，**只能手动的添加一些`setTimeout`**，假定延时多少秒后，跳转后的页面载入成功了，这样的API开发出来的脚本太难看也太难维护了。