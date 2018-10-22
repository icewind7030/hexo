title: 'what happens when you type in a URL in browser'
date: 2014-06-03 15:19:10
tags: [URL,browser]
categories: 前端
toc: false
---

**what happens when you type in a URL in browser，从输入 URL 到页面加载完成的过程中都发生了什么事情？**


<!--more-->

前段时间在微博上看到百度FEX部门的[吴多益](http://weibo.com/nwind)前辈发的一条微博：

> 话说这些年来我最喜欢问的面试题是『从输入 URL 到页面加载完的过程中都发生了什么事情？』，个人觉得这道题非常非常难，既考深度又考广度，至今还没有人的回答让我满意，所以我想自己挑战一下，整理出一篇文章，有哪位大牛也想来一起挑战么？

这个问题讨论的问题看似简单，实际上问题涉及的整个过程的知识非常庞大，要深入了解方方面面很难，吴前辈之后整理出了他对该问题讨论，并针对现今非常流行和普及的移动设备来展开讲述了该问题的解答，原文可以查看吴前辈的这篇博客,建议都去拜读一番——[从输入 URL 到页面加载完成的过程中都发生了什么事情？](http://fex.baidu.com/blog/2014/05/what-happen/)

写这篇博客是为了复习一下，加深理解，该文章分6个主要环节介绍了从输入 URL 到页面加载完成的整个过程：

---

1. **从输入 URL 到浏览器接收的过程中发生了什么事情**
结合现今流行的移动设备的触屏使用情景介绍了第一个环节的过程。
2. **浏览器如何向网卡发送数据？**
从浏览器到浏览器内核，之后是HTTP请求的发送,还给了一个底层网络协议的具体例子，包含由低到高：HTTP、TCP、IP、MAC各层的协议内容和讲解。
3. **数据如何从本机网卡发送到服务器？**
介绍了从内存中读取数据写入网卡后，通过WiFi路由器发送，经由传输介质，一直到服务器的过程。
4. **服务器接收到数据后会进行哪些处理？**
数据到达真正的应用服务器前，可能还会先进行负载均衡，将请求合理地分配到多个服务器上，再交由web server处理进入后端语言处理HTTP请求。
5. **服务器返回数据后浏览器如何处理？**
介绍浏览器接收到数据后的处理，推荐了一篇高大全的文章阅读[How Browsers Work](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/)，之后做了一些补充。
6. **浏览器如何将页面展现出来？**
同样，结合了触屏情况介绍了页面内容的呈现过程，还普及了LCD显示原理知识。

---

该文章所介绍的内容其实还远不止这些，还提供了很多扩展学习链接，需要进一步深入学习。此外，根据评论中的讨论，还找到一篇stackoverflow上关于此问题的简洁回答，同样也非常具有学习价值:

> In an extremely rough and simplified sketch, assuming the simplest possible HTTP request, no proxies and IPv4 (this would work similarly for IPv6-only client, but I have yet to see such workstation):<br/>
1.browser checks cache; if requested object is in cache and is fresh, skip to #9
2.browser asks OS for server's IP address
3.OS makes a DNS lookup and replies the IP address to the browser
4.browser opens a TCP connection to server (this step is much more complex with HTTPS)
5.browser sends the HTTP request through TCP connection
6.browser receives HTTP response and may close the TCP connection, or reuse it for another request
7.browser checks if the response is a redirect (3xx result status codes), authorization request (401), error (4xx and 5xx), etc.; these are handled differently from normal responses (2xx)
8.if cacheable, response is stored in cache
9.browser decodes response (e.g. if it's gzipped)
10.browser determines what to do with response (e.g. is it a HTML page, is it an image, is it a sound clip?)
11.browser renders response, or offers a download dialog for unrecognized types<br/>
Again, discussion of each of these points have filled countless pages; take this as a starting point. Also, there are many other things happening in parallel to this (processing typed-in address, adding page to browser history, displaying progress to user, notifying plugins and extensions, rendering the page while it's downloading, pipelining, connection tracking for keep-alive, etc.).

该回答非常简洁，但是也将整个过程说的非常清楚。

以上内容分享出来，便于共同学习，要走的路还很长...




