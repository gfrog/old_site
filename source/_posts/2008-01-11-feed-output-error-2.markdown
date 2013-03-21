---
comments: true
date: 2008-01-11 22:08:28
layout: post
slug: feed-output-error-2
title: feed输出竟然有乱码
wordpress_id: 271
categories:
- gfrog-say[青蛙的杂烩]
---

再次测试一下,看看是字符编码的问题,还是wordpress系统的问题,还是google reader的问题!

-------------无奈的分割线-----------

update:2008-01-11,22:40

feed问题已经定位,出在Aizatto's Related Posts这个插件上,它会截取相关日志的部分内容输出到feed里,但是正好碰上它截取的那部分有特殊字符,所以就挂掉了.解决办法也很简单,禁用这个插件的摘要输出功能就是了.

唉,wordpress的插件啊,好用的很多,但是真是容易出毛病!
