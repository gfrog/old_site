---
comments: true
date: 2010-12-16 00:02:38
layout: post
slug: setup-an-amazon-ec2-account
title: 注册Amazon EC2账户一枚
wordpress_id: 1068
categories:
- virtualization[Virtualization]
- gfrog-say[青蛙的杂烩]
---

青蛙今天在水木社区LinuxApp版看到有人推荐Amazon EC2，没有忍住诱惑，去注册了一个帐号。

每个EC2帐号可以免费使用1年，试用的配置(每月)


> 750 hours of EC2 running Linux/Unix Micro instance usage
750 hours of Elastic Load Balancing plus 15GB data processing
10GB EBS存储空间、100万次IO操作
1GB 快照存储、10,000 snapshot Get Requests and 1,000 snapshot Put Requests
全部服务合计15GB流入和15GB流出带宽.


今天晚上帐号Active了，上去看了一下，实际上EC2就是一个特别一点的VPS。再加上Amazon的工具套件，可以很方便的启动/关闭虚拟机实例，这样网站就完全不用考虑硬件的问题，负载不够马上再开一个实例就是了。

另外，AWS上提供的虚拟化方案是基于XEN的，传言EC2的IO性能不够好，这可能跟XEN的实现也有关。

青蛙还发现AWS界面上的大堆bug，虚拟机启动信息里的大堆oops信息，看来领先的虚拟化提供商提供的服务也不是完美的，虚拟化的发展之路还很漫长。

附记，申请EC2的攻略（似乎图片不太稳定，大家凑合看吧 ：P）：
[http://www.imtnt.net/amazon-ec2-micro-instance-and-tunnel-guide/](http://www.imtnt.net/amazon-ec2-micro-instance-and-tunnel-guide/)
