---
title: 编译boost的正确方法
tags:
  - boost
  - C++
abbrlink: 46203
date: 2019-02-12 01:19:39
categories:
  - Coding
---

提到[boost](https://www.boost.org)，大家都不会陌生，这篇文章简要的聊一聊boost的编译。

{% blockquote %}

2020.3.17按：

我知道基本上每个人编译boost都会在百度上搜编译boost的相关内容，然后就搜出来铺天盖地的CSDN博客，太常见了。
这些博客可以说没有一个是没夹带私货的，一大堆options，谁他妈吃饱了撑着去复制？然而在你读了官方的docu之后你会发现
大多数都是他们自己的私货，有可能是他们需要这个东西，所以要编译，而你不需要，你编译来做什么？另一方面，他们自己本身就是傻货，他们根本不清楚自己的options是什么，反正编译成功了，boost也可以用了，是时候写一篇教程出来水了。

然后一群妖魔鬼怪机械化操作疯狂转载，一搜全是相似的内容，8成以上的文章options都一模一样，这还不可笑吗？

这篇文章实际上是以编译boost为例子，目的在于告诉大家除了看垃圾CSDN博客实际上有更好的选择。(然而有些东西官方文档确实写得很烂，比如hexo的官方文档，我认为就是烂文档的典范(特指中文版，英文版不清楚))

{% endblockquote %}



刚开始你或许和我一样不知道编译boost该从何下手，于是你只能寻求百度和google，然而网上的文章不一定总是最新的，他那些`--with`也并不一定就是你想要的，那编他做啥？ 是的，这篇文章我们不聊怎么编译boost，然后丢给你一堆命令，你放在自己电脑上能跑就开心了，不能跑就换下一个教程，实在是浪费了手头就有的大好资源啊。

{% blockquote %}

接下来阅读的内容可能需要读者有一定的洋文阅读水平， 这是十分需要且必要的技能！

{% endblockquote %}

首先你既然要整boost那么一定得用它的库吧， boost现在依旧是非标准的最全C++第三方库，每次的标准都有不定量的boost库内容入选其中，即使现在标准库越来越强悍，有了更多库的支持，加上boost就如虎添翼了。



## 按需求选择编译or Not

如果你是`Windows`用户， 你大可以选择`预编译版本`， 这么周到的服务， 何乐而不为呢？ 如果不是有些库必须在特定平台重新编译， 否则谁愿意折腾呢？（~~当然，我相信愿意折腾的大有人在~~）。哪里找到预编译版本？ 传送门:[boost官网](https://www.boost.org)，找到`Download`, 然后找到`prebuilt`!

如果你是`linux`用户，那么编译是少不了的，当然不编译你还是可以使用一大堆现成的库， 无需编译。

## 从下载源码说起

这有啥好说的， 下载源码， 从[官网](https://www.boost.org)开始。

## 文档阅读

下载解压后， 未免迷茫了， 接下来咋整？ 编译啊！ 我们参观一下最新版`boost-1.69`的解压目录：

{% asset_img boost-directory.png %}

如图所示， 我们可以看到一个叫做`doc`的文件夹，这个是boost送给你的文档(Document)！ 我们
进入doc文件夹。

{% asset_img boost-doc.png %}

进入doc我们发现， 里面有几个文件夹一下子就引起了我们的关注， 比如`html`和`pdf`， 我们通过文件夹名称就可以猜测出里面分别放的是html格式和pdf格式的文档， 准备的多么精心， 不看可惜了！

进入html文件夹打开`index.html`即可在浏览器里查看boost文档（其实第一层目录就有一个`index.html`, 不过是一样的，这里打开的html可以通过点击网页上的`HOME`来到第一层的`html`）， 通过阅读文档， 我们可以知道如何编译， 而且是如何编译自己想编译的东西。 如文档所云（打开后点击页面上的HOME， 找到Geting Started后进入guide，在页面右下角可以看到两个超链：分别指向Windows和linux，此时按照自己的环境选择就成）
以linux为例：
{% asset_img build-boost.png %}

可以看到一个目录， 看到`5.1 Easy Build and Install`可以看到相关编译安装的内容，点击跳转， 便可以看到关于如何编译和安装的内容。

接下来的就是自己尝试， 阅读文档， 除非实在是遇到以个人力无法解决的困难， 不然还是以官方文档为准。





