title: '使用Hexo搭建Blog中遇到的问题及解决办法'
date: 2014-05-13 21:34:40
categories: 博客 
tags: [hexo]
---

在使用Hexo搭建Blog时遇到了一些其他博客没有提及的问题，自己通过各方查找和尝试找到的解决办法如下

<!--more-->

###1. 使用个人域名代码xxxx.github.io，购买godaddy域名时，找不到支付宝支付选项###

选择左下角的back to cart,在cart页面右上角有个国家选择，设置为china后，再重复购买操作就可以看到支付宝付款选项了


###2. 使用dnspod为自己的网站提供域名解析服务时，如何添记录及填写记录值###

在dnspod注册接着添加自己网站，例如icewind-blog.com后，点击“添加记录”，添加“主机记录”为@和wwww的两条记录，记录类型都为A，其他值默认，记录值填写处，可以通过windows命令行窗口，输入`ping icewind7030.github.io`,后面 [ ] 内的ip即为所需填写的ip。
![](/img/ping.jpg "ping")


###3. hexo deploy时出现fatal: The remote end hung up unexpectedly错误###

不一定是配置错误，也可能是网络问题，换一个网络环境再次deploy试试


