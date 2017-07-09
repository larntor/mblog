---
layout: post
title:  win10 uwp csdn 博客阅读器 
category: uwp 
---

本文告诉大家如何写一个 rss 阅读器，并且用它来获得 csdn 博客。

因为 csdn 的首页有很多文章，但是我喜欢的文章很少，于是我需要一个可以过滤关键词的工具。如果文章包含了某些关键词就把文章给我，这样就可以减少我去找文章的时间。

<!--more-->

<!-- csdn -->

为了做这个软件，我需要下面的技术：

 - [win10 uwp 读写XML - lindexi_gd的专栏 - CSDN博客](http://blog.csdn.net/lindexi_gd/article/details/71077198)

 - [win10 UWP RSS阅读器](https://lindexi.github.io/lindexi/post/win10-UWP-RSS%E9%98%85%E8%AF%BB%E5%99%A8/)

 - [win10 UWP ListView](https://lindexi.github.io/lindexi//post/win10-UWP-ListView/)

 - Piovt 的相关知识

一开始我使用的是 ms 的 Rss 类，但是很不好用，很多信息无法得到，于是我就对 csdn 使用了 xml 的方法解析。需要知道，因为我是对 csdn 博客使用 xml 解析，所以这个软件可能无法在其他地方使用，因为 csdn 使用的不是标准的。最少不是垃圾微软的标准，所以对于一些其他博客，可能解析就出错了。

项目放在 [全球交友平台](https://github.com/lindexi/UWP/tree/master/uwp/src/Boleslav) ，如果有兴趣 开发的话，请直接下载代码到本地，编辑之后提交。如何使用 github 参见 [Git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

于是可以看到，我需要的软件只有两个页面，一个是显示博客，一个是设置。

为了简单，我也不上 MVVM 啦，直接的代码就是点击确定，扫描所有的 rss ，获取他们的内容，然后判断是否存在关键词，如果存在的话，就显示，如果不存在，就不显示。

