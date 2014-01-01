---
comments: true
date: 2008-01-19 22:23:55
layout: post
slug: number-of-domains-on-one-dreamhost-server
title: Dreamhost主机上的用户数
wordpress_id: 277
categories:
- gfrog-say[青蛙的杂烩]
tags:
- Dreamhost
- hosting
- overselling
---

ssh到我的DH主机上总感觉慢慢的，很不爽。

今天在这里发现了下面两条命令，可以分别查看主机上ssh的用户数和总用户数。

grep -i /home/ /etc/passwd | wc -l

grep -i /home/ /etc/passwd | grep /bin/.*sh$ | wc -l

查了一下，我的空间所在的这个主机上有108个ssh帐户，379个ftp/ssh用户。

vmstat看下负载，真的是够高。

procs -----------memory---------- ---swap-- -----io---- --system-- ----cpu----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in    cs us sy id wa
 0  0  41968 213368  66372 2950740    0    1     4     9    1     3 27  7 66  0
