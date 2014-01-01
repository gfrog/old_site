---
comments: true
date: 2008-03-02 10:21:58
layout: post
slug: solution-of-qq-null-group-name-in-pidgin
title: pidgin中群名称变为NULL的解决办法
wordpress_id: 292
categories:
- linux[Linux]
---

前阵子pidgin从2.x版本升级到了2.3.1以后，青蛙的QQ的群名称就全部变成了NULL，然后所有群的消息都跑到了一起，看的我眼花缭乱。
于是青蛙就眼巴巴的等着pidgin的升级。这不，它终于升级到了2.4.0了，结果这个问题还是没改！

看来老外是靠不住的，只能靠自己了。青蛙拿起google一划拉，还真找到点东西。
这不，[Linuxsir上的linuxahah同学说](http://www.linuxsir.org/bbs/thread323211.html)，这是因为死TX修改了协议导致的，并且放出了一个补丁。但是这个补丁需要打到pidgin的源码里面重新编译，鉴于青蛙前N次编译pidgin的结果都是失败而告终，这个方法还是放弃了。

于是青蛙继续google，发现[Linuxsir的marklf大牛说](http://www.linuxsir.org/bbs/post1798882-10.html)，这个问题还是因为pidgin中的libqq.so引起的，只要用以前版本的libqq.so替换现在的版本就可以了。这个方法很好很强大，只要找到2.x版本的libqq.so文件就，然后把它复制到/usr/lib/purple-2/就可以了。

困扰了青蛙好久的问题解决了，终于可以开心的聊QQ了 :D



* * *

**更新：**[2008-03-02 11:33] 放上日志里提到的补丁文件和libqq.so。


[qq patch for pidgin 2.3.1 (pidgin-2_3_1-qq_tar.bz2)](http://www.divshare.com/download/3927060-345)


[libqq.so in pidgin 2.2.2 (libqq-2_2_2.zip)](http://www.divshare.com/download/3927059-f35)


补丁文件需要解压到pidgin的源码目录下，然后用patch -p1 < xxx.patch 来打源码补丁。大家各取所需吧。
