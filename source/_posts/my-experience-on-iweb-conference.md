title: 2014iWeb大会见闻
date: 2014-08-29 17:16:41
tags: [iWeb,HTML5,Javascript]
categories: 前端
---

8月16日有幸去参加了在北京国际会议中心举办的2014年度iWeb大会，虽然更多的是去看个热闹，但是还是有不少收获的，写篇博客讲讲吧。

<!--more-->

一个月前就看到我微博关注的一些前端大牛们有提到本届大会，而且是在帝都举办，早就想去看看了，但是只看到一些会前互动，却错过了相关的大会网站和入会需知相关的微博信息，也不知道是不是主办人根本就没在微博发相关信息。直到大会前一晚我才通过搜索搜到了大会的介绍网站，才知道需要先申请，当时申请已经晚了，不过第二天通过微博询问主办方得知了由于参会人数众多，最终无需申请也能参加后，大中午的我还是兴冲冲的跑去了（为什么上午没去呢，难得周末睡个懒觉嘛。。。）。

赶到现场发现人确实很多，本次大会的主题是“HTML5”，会场内各种前端相关产品展位都很人呢，其中还有我实验室项目中用到的appcan web app移动客户端开发工具的展台，书籍展位前也有不少刚兴趣的程序员们。

大会上午都是些开幕式相关的活动，错过了也就错过了，下午可是有我的偶像——前端界第一逗比“大城小胖”参与演讲的互动娱乐专场，还有阿里无线前端——“寒冬winter”、360奇舞团——“月影”等其他BAT前端风云人物主讲的工具应用专场，

每个讲厅都座无虚席，各个地方都转了一圈后找到了互动娱乐专场的讲厅，我站在门口听了2位演讲人的演讲，其中第二位巴士在线的演讲人说的都是些不痛不痒的该公司的优秀公共传播渠道等资源，感觉很没意思，虽然接下去就是“小胖”的演讲了，但是其实他的题目也不是很吸引我，而且他的演讲时间正好跟“寒冬winter”以及接下来几位我非常想听的主讲人重叠了，正当我犹豫的时候，想起来既然小胖就是下一位演讲人了，估计他应该在场外不远的地方等着呢，我一找，果然在大厅的椅子上发现他了，本人没有微博上萌啊，而且跟朋友拿着mac聊得非常投入，也不好意思打断人家上去求合影什么的，投拍了一张照片了了心愿还是奔工具应用专场去了，还是学点感兴趣的知识扩充点知识面要紧（粉丝当的不合格啊...）。

好了，下面该进入正题了，虽然这篇本来就不是什么技术分享之类的博客，但是记流水账也是不行的（上面几段写的都啥啊）。

##jQuery时代到移动时代——阿里无线前端遇到挑战与技术方案分享  winter-程劭非（阿里巴巴前端高级技术专家）
来到工具应用专场讲厅，坐在地上听了一半winter前辈之前的一位英特尔软件研发经理介绍他们的一站式HTML5开发工具，特点是api数量够多，缺点是还没来得急汉化（没汉化就来中国推广了。。），听起来跟appcan差不多。

中途不断有人离场，我找到个机会坐到一个座位，英特尔的这位演讲一接近尾声，整个讲厅就很快挤满了人了，站着都挺挤得了，阿里无线前端高级技术专家果然很有分量，庆幸自己找到了座位，不然我的身高在北京还是挺吃力的。
winter前辈明显演讲技巧很熟练，PPT简洁风趣，首先分析了jQuery的成长里程和其利弊，顺带还黑了一些逗比“小胖”，提出了优秀的Javascript库对前端程序员的吸引力和帮助这个引子，之后开始介绍阿里无线前端的自主开发的[Mobile Library](https://github.com/amfe/ml)（简称M.L.。。。。。。。）无线前端库，介绍了该库的一些特点的关键技术:主要是利用rem和px实现flexible等，然后演示了一下效果。听完收获还是不错的，而且也不枯燥。

##HTML5富交互与社交传播  潘征（百度高级研发工程师）
这是位百度前端的女程序员，演讲能力一般，但是PPT做的很用心，通过前段时间样子微信传播很火的百度《移动互联网发展趋势报告2014Q2》引出了她的题目，在移动互联网大发展的现在利用好移动社交传播特性进行前端开发效果可能会比桌面端影响力大的多。

她列举了同样是他们负责开发的关于2014世界杯的放在百度贴吧首页的数据可视化页面，获得的关注度和传播性远低于这次通过微信传播的趋势报告，显现出了先进移动社交平台巨大的影响力，然后又从一些技术层面介绍了该趋势报告针对移动设备访问的场景所作的一些优化，比如动画效果的取舍，小屏幕低分辨率设备的拆分页面适配方案等，都很有学习价值。

##Web+Client高性能解决方案——AlloyKit  龙福康（腾讯AlloyTeam高级Web前端工程师）
接下来出场的这位是腾讯很有名的前端团队AlloyTeam的成员，分享的是该团队针对腾讯的前端应用场景开发的一整套工具平台，为了配合大会主题，着重介绍的是其中离线存储部分的实现方案和效果，但是感觉像是照着PPT念材料，讲的很冗长平淡，所以没有听的很投入，但是也了解到了HTML5的相关API在腾讯这种巨头的前端产品中的应用方式。

##Hybrid API 鬼道-徐凯（天猫前端）
不得不说，阿里的派出的两位技术牛人演讲水平都比较高，鬼道前辈的演讲也成功将我听了3个多小时有点涣散的注意力拉了回来。他介绍的是天猫在web产品开发中利用hybrid api实现或者正在开发的一些有趣的应用场景，包括利用到了光线感应的为汇源果汁做的广告demo，在web产品中启用摄像头进行面部及手势识别，进而开发的珠宝等首饰的试戴demo，都让人眼界大开，感受到了HTML5结合hybrid api的强大之处和应用前景。

好了，流水账记到这基本差不多完结了，等我从讲厅出来还想再去寻找小胖老师的时候发现已经不在了，估计是跟小伙伴们大吃大喝去了，iWeb晚上还有攻城师狂欢夜宴，看宣传还挺热闹的，但是我得回学校吃饭了。。。