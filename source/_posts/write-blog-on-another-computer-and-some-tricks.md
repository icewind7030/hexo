title: '如何在一台新电脑上继续用hexo写博客及一些技巧'
date: 2014-07-08 20:21:54
categories: 博客 
tags: [hexo]
toc: false
---

很久没写博客了，当初的hexo环境都布置在实验室的电脑上，现在在外面实习，下班回到寝室就快10点了，也没多少精力去写博客，今天好不容易有半天空，准备写两篇博客，但是因为我确实对Git知之甚少，弄了一下午才用个很土的办法在自己笔记本上重新搭起了我的hexo环境，就把我的土办法分享一下吧。

<!--more-->

首先声明，我这里写的办法的确是最土的办法，因为Google了半天，那些高大上的hexo同步方法，都因为说的太简略而我git知识太匮乏而失败了，所以我只能用最土的办法，不过确实很简单。

##博客文件转移

把实验室的hexo文件夹拷贝过来，粘贴到了我的笔记本中，里面其实最重要的就是source文件夹下的博客markdown文件和其他相关资源，theme文件夹下的主题相关文件以及ssh配置文件等。

将其根目录下的`_config.yml`文件复制出来以备后用，因为之后利用这个文件夹初始化本机hexo环境会覆盖这个文件，回到初始状态，为了节省重新设置其中参数的功夫，先复制出来回头再把这个文件粘贴回去，总之为了省事我们能粘贴复制的都尽量粘贴复制。

## 初始化hexo环境

这一步和搭建一个新的hexo环境没什么区别：

### 1.安装Git

下载[git for windows](http://msysgit.github.io/)安装即可。

### 2.安装Node.js

下载[node](http://nodejs.org/)安装。

### 3.安装hexo

安装好前两者后，右键点击任意文件夹就会有`Git bash here`菜单，进入git bash界面后，利用npm命令即可安装hexo

	npm install -g hexo

### 4.创建hexo文件夹

安装完成后，右键之前粘贴回来的文件夹(比如`F:\Hexo`)，选择`Git bash here`,输入以下命令，hexo即会自动在目标文件夹下初始化。

	hexo init

### 5.安装依赖包

输入完以上命令后，你会收到提示，按提示输入以下命令即可完成安装

	npm install

到此hexo初始环境就基本搭建好了，别忘了将之前复制出来的`_config.yml`配置文件覆盖回去，然后就可以在新的hexo文件夹下写博客了。


## hexo相关技巧

介绍2个写博客中有用的技巧：

### 1.warning: LF will be replaced by CRLF

在hexo deploy时，有时会出现这个提示信息`warning: LF will be replaced by CRLF`，虽然不影响使用，但看着也挺烦的，如果想不提示，可以使用如下方法：
1. 切换到博客的根目录，执行如下命令：`git config --global core.autocrlf false`
2. 删除掉该目录下的.git文件夹（可能是隐藏的），命令：`rm -rf .git`
3. 重新`git init`。


再deploy就没有以上提示了。

### 2.hexo deploy时重复输入用户名密码的问题
在windows上使用`hexo deploy`(`git push`)到github服务器的时候，每次都需要填写用户名和邮箱，很麻烦，可以用以下方法解决，不再每次都需要输入。
1. 添加环境变量
![](http://ww2.sinaimg.cn/large/5e8cb366jw1e51yjjv0okj20b00b5gmp.jpg)
2. 在用户文件夹(如`C:\Users\liu`)下新建一个名为`_netrc`的文件(无扩展名称)。
3. 编辑该文件：
	

	machine github.com
	login zhangsan
	password 123456

保存即可。