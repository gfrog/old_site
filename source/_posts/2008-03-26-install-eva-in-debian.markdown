---
comments: true
date: 2008-03-26 01:03:12
layout: post
slug: install-eva-in-debian
title: 在debian sid上面安装eva
wordpress_id: 304
categories:
- linux[Linux]
---

![debian-ubuntu](http://haokanbu.s3.amazonaws.com/picture/external/c8b6c8df6df744f7af186747f25a35b3.jpeg)

自从青蛙人品爆发，在pidgin上面死活登录不上QQ以后，青蛙都是在wine上面跑QQ.在wine上面速度慢不说，QQ时不时的来一下非法操作就把青蛙折磨的死去活来。
据说最近eva又升级了，于是青蛙决定换用eva。

Debian的apt里面是没有eva的。青蛙上网搜了一圈，也没有发现eva的升级包，连[它的主页](http://sourceforge.net/projects/evaq)上也都还是很久很久以前的版本（大概eva已经转入地下了）。青蛙最后在ubuntu的源里面终于翻倒了eva的deb包了。

eva在ubuntu源的universe里面，随便找一个ubuntu的镜像(青蛙用的是[中科大的源](http://debian.ustc.edu.cn/ubuntu))，在/pool/universe/e/eva目录里找到[eva_0.4.9+svn20080215-0ubuntu1_i386.deb](http://debian.ustc.edu.cn/ubuntu/pool/universe/e/eva/eva_0.4.9+svn20080215-0ubuntu1_i386.deb)，下载回来备用。

在正式安装eva之前，青蛙还要搞定它的依赖性问题。

ubuntu里面的eva是依赖于kdelibs4c2a这个包的。但是debian里面的kdelibs4c2a存在一个依赖性的bug：
它依赖于包libopenexr2ldbl，但是这个包在debian的i386源里面竟然没有。

于是青蛙再从ubuntu源里把[libopenexr2ldbl](http://debian.ustc.edu.cn/ubuntu/pool/main/o/openexr/libopenexr2ldbl_1.2.2-4.4ubuntu1_i386.deb)给抓回来，dpkg -i安装之，然后执行#aptitude install kdelibs4c2a，安装一系列的依赖包。

顺便说一句，debian跟ubuntu这两个兄弟有时候还真不错，互相谁也不挑谁的，deb包拿过来一般都能直接用。

最后，执行#dpkg -i eva_0.4.9+svn20080215-0ubuntu1_i386.deb，eva安装成功。聊天去也~

![eva](http://haokanbu.s3.amazonaws.com/picture/external/c8b6c8df6df744f7af186747f25a35b3.jpeg)
