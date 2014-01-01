---
comments: true
date: 2008-12-27 22:45:53
layout: post
slug: tips-about-freebsd-installation-and-upgrade
title: 关于FreeBSD系统安装和升级的几个技巧
wordpress_id: 330
categories:
- freebsd[FreeBSD]
---

![](http://www.freebsd.org/logo/logo-full.png)

青蛙今天又开始在VMWare上继续倒腾FreeBSD，话说上次给FreeBSD划分的虚拟硬盘太小了，csup了一下系统和ports就把分区吃光光了。所以今天青蛙从头又装了一遍FreeBSD，在安装和升级的过程中遇到了不少问题，在这里统一做一个记录。

首先，是青蛙又把root密码搞丢了。这已经是第N次刚刚装上系统就要恢复密码了。不过今天的原因是这样的，青蛙在安装的时候不小心修改了系统的keymap设置，结果按照正常键盘顺序输入的字符，变成了不知道是什么符号。当青蛙发现这个问题并改过来keymap之后，root密码就一直错误了。没办法，上网google一下。流程如下[1]：


> 第一步，启动到freebsd的single user模式，也就是出现freebsd的启动菜单的时候按4；等着系统启动，最后会提示指定shell的位置或者按回车继续，这里直接按回车就ok。
![freebsd-boot](http://gfrog.net/wp-content/uploads/2008/12/windowslivewriterfreebsdtipsaboutfreebsdinstallationandup-b1ffreebsd-boot-3.jpg)
第二步，在shell里执行如下命令：
# fsck -p
# mount -u /
# mount -t ufs -a
第三步，用passwd修改root密码。


青蛙在这啰嗦一句：现在想想当时的错误操作就是在设置终端那步，青蛙打开keymap的设置对话框看了一眼，估计那个时候就直接选择了第一个键盘类型，然后就那么错了。对比各种Linux的安装过程，keymap的设置有一个默认不做任何改变的选项，青蛙在FreeBSD里面就没有找到，看来FreeBSD在这种细节上还需要继续努力。

由于FreeBSD的默认shell是csh，所以青蛙用起来十分的不顺手，上网google到几个贴心小设置，让csh用起来更加方便[2]：


> alias ls    ls -w      #让csh可以正常显示汉字 
alias ls    ls -G       #按照文件类型高亮显示

set autolist      #按tab键时自动补全，如果有多个符合就显示列表。
set nobeep      #关闭终端响铃
set prompt="`/bin/hostname -s`:%~# "      #在命令提示符中显示当前目录。


青蛙上次搞FreeBSD已经是很久以前的事了，记得那时候好像FreeBSD 5-CURRENT刚刚出来，再后来青蛙就一头扎进Debian的世界去了。所以FreeBSD的新东西青蛙几乎都没有见过，例如，更新ports。记得当年的做法应该是先从sysinstall里面装一个ports，然后在这个ports中装一个cvsup-without-gui（似乎是叫这个名字吧），接着编辑port-supfile文件，再然后才是用cvsup重新更新ports。现在看起来，这套程序简直麻烦到家，对于新人来说十分的不友好。不过，这些东东都是历史了，从FreeBSD的handbook看，更新ports已经简单了很多了。

说一个青蛙认为最简单的吧，portsnap方式[3]。说这种方式简单，到底有多简单？ 总共分三步：


> 先下载一个ports包：
#portsnap fetch
然后解压到/usr/ports里面：
#portsnap extract
以后需要更新的时候，直接执行：
#portsnap update


就这么简单，比cvsup还有稍后引入的csup方式简单的多。 ：） 当然portsnap自身也还有几个参数，青蛙觉得最常用的应该就是-s参数了，这个参数用来指定更新时使用的更新服务器，默认是portsnap.FreeBSD.org。国内有一系列portsnap的镜像可用，速度还是很快的，例如hssh.org的镜像：


> portsnap.hshh.org
portsnap2.hshh.org
portsnap3.hshh.org (网通)
portsnap4.hshh.org


今天先写到这里，未完待续...

参考资料：

[1]：FreeBSD恢复root密码
      [http://www.zhaocs.info/linux-technic-26.html](http://www.zhaocs.info/linux-technic-26.html)
[2]：csh一些简单舒服的设置
      [http://www.linuxsir.org/bbs/showthread.php?s=&threadid=112343](http://www.linuxsir.org/bbs/showthread.php?s=&threadid=112343)
[3]：4.5.1 获得Ports Collect
      [http://www.freebsd.org/doc/zh_CN.GB2312/books/handbook/ports-using.html](http://www.freebsd.org/doc/zh_CN.GB2312/books/handbook/ports-using.html)
