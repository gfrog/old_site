---
comments: true
date: 2009-04-08 20:13:36
layout: post
slug: test-debian-boot-time-with-bootchart
title: 测试linux系统的启动速度
wordpress_id: 342
categories:
- linux[Linux]
---

今天看到blog一则，话说[ubuntu9.04竟然可以在14秒内启动](http://linuxdesktop.cn/2009/04/08/ubuntu-904-fast-boot.html)。于是青蛙拿[bootchart](http://www.bootchart.org)测了一下自己的debian。

 

青蛙自己的系统用了33秒，其中有启动vmware和virtualbox的服务的时间，还有启动ntfs-3g、lighttpd、transmission、wicd的，比较起来，成绩还不算太差。      
不过wicd是用python写的，加载确实比较耗时，但是network-manager的可定制性实在不高，不知道还有没有同类的软件可以替换。

 

另外，bootchart加载之前的一段时间应该是内核加载的时间，看来青蛙还要继续精简内核才行。

 

顺便记录一下bootchart的使用方法。      
说起来也很简单，统共分3步。

 

  
  1. 装好bootchart之后，配置自己的bootloader，例如grub，在内核选项里面加上一句： init=/sbin/bootchartd。 
   
  2. 重启，bootchart自己就会在后台记录启动信息，默认保存在/var/log/bootchart.tgz中。 
   
  3. 完成启动后，运行bootchart命令，它就会把启动信息转化为下面这张图啦。 
 

 

[![bootchart](http://gfrog.net/wp-content/uploads/2009/04/bootchart-thumb.png)](http://gfrog.net/wp-content/uploads/2009/04/bootchart.png)
