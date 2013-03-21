---
comments: true
date: 2011-12-18 01:30:00
layout: post
slug: expand-tco-link-in-twip
title: 在Twip中转换t.co链接
wordpress_id: 1985
categories:
- twitter[Twitter]
---

自从Twitter强制把所有链接都转换为t.co之后，在手机上访问这些地址就成了大问题，开VPN慢，绕了一圈发现是国内地址，不开干脆访问不能。于是青蛙一直酝酿在Twip那里把短链接扩展。


今天有时间，研究了一下，本来打算自己写的，但是本着不重复造轮子的态度，写之前google了一下，这不，这位同学已经写好了： [http://hjin.me/code/727/](http://hjin.me/code/727/) ,全部修改的代码贴到了这里：[http://pastebin.com/f4Gw0DND](http://pastebin.com/f4Gw0DND) 青蛙拿过来直接扔进twip，问题解决。







青蛙在这放出一个基于twip r204版本的patch吧，另外还有一个小修改的补丁，修复一些情况下的警告信息。




[0001-twip-enable-expand-tco-link.patch](https://code.google.com/p/gfrog/downloads/detail?name=0001-twip-enable-expand-tco-link.patch&can=2&q=)




[0002-twip-fix-some-warning-msg-in-expand-tco-link-patch.patch](https://code.google.com/p/gfrog/downloads/detail?name=0002-twip-fix-some-warning-msg-in-expand-tco-link-patch.patch&can=2&q=)







这个修改只能解决t.co的问题，没办法解决其他被关照到的短网址服务，所以青蛙仍然酝酿着什么时候搞一个把所有短网址都展开的补丁。不过青蛙看php代码实在是头疼，只好下次有时间再硬着头皮读代码改吧。
