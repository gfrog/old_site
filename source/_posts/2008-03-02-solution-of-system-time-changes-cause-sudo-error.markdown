---
comments: true
date: 2008-03-02 22:38:04
layout: post
slug: solution-of-system-time-changes-cause-sudo-error
title: 修改系统时间引起sudo罢工的解决办法
wordpress_id: 293
categories:
- linux[Linux]
---

青蛙今天使用sudo之后，把系统时间往回调了几小时，然后sudo就罢工了，提示下面的信息：
sudo: timestamp too far in the future
改时间竟然还能改出毛病，这个sudo真是太fz了。青蛙拿着这个出错信息一google，发现了俩解决方法。

第一个，不黄很暴力，用root权限删除/var/run/sudo目录。
第二个，执行sudo -k，这个命令是用来清除sudo的时间戳，下次执行sudo的时候它会跟你重新要密码。

