---
comments: true
date: 2013-01-06 22:22:44
layout: post
slug: foward-dns-packet-with-tunnel
title: 我们来打洞：转发DNS包
wordpress_id: 3466
categories:
- linux[Linux]
---

基于某些众所周知的原因，在天朝上网非常需要勇气和技术。
今天在跟某人聊天时了解到Chrome竟然没办法使用proxy进行DNS查询<1>， 导致DNS查询结果持续被污染。
虽然用proxychains能把一切流量塞进代理里面，
不过用proxychains启动浏览器的话，各种自动代理插件就失效了，所以这是个得不偿失的方法。
青蛙晚上的时候突然想到iptables是不是可以把DNS查询流量转发进ssh tunnel呢？
上网google一番之后，发现这是可行的，而且其实用不到iptables，只要nc就够了。

要用SSH Tunnel转发DNS流量，最大的问题是要把DNS协议使用的UDP协议转换为TCP协议，然后在proxy端还要转换回来。
所以目前青蛙想到的方案有一个很大的缺陷，就是如果ssh服务器上不提供nc命令的话，下面的操作是没法完成的。

首先，用SSH连接ssh服务器，这里青蛙打了一套组合拳，ssh -L + -D组合，分别负责DNS流量和普通数据流量：

    ssh -D1080 -L5353:127.0.0.1:5353 user@ssh-server


-L 参数的目的是在本地机器上打开一个5353端口，发送到这个端口的数据都会被转发到ssh服务器上的5353端口上。

然后，在ssh服务器上执行：

    server$ mkfifo /tmp/fifo
    server$ nc -l -p 5353 < /tmp/fifo | nc -u 8.8.8.8 53 > /tmp/fifo


这两条命令用普通用户就可以执行，在大部分ssh服务器上应该都可以用。
另外<2>中还提供了一个socat版本，这个东东可以省去创建fifo管道的步骤，
看起来比较简洁，但是做的事情跟nc是一样的：
把ssh服务器上TCP5353端口收到的包转发到8.8.8.8的UDP53端口上去。

    socat tcp4-listen:5353,reuseaddr,fork UDP:8.8.8.8:53

接下来在本地机器上执行：

    local# mkfifo /tmp/fifo
    local# sudo nc -l -u -p 53 < /tmp/fifo | nc localhost 5353 > /tmp/fifo

在本地UDP53端口上监听，一旦有数据包，就转发到本地的TCP5353端口上去。

socat版本：

    socat udp4-listen:53,reuseaddr,fork tcp:localhost:5353



好了，到此打洞的部分完成，最后的包转发流程应该是：

    localhost:UDP53 -> localhost:TCP5353 -> ssh-server:TCP5353 -> 8.8.8.8:UDP53


主要就是使用nc/socat把UDP和TCP流量进行转换，以便可以把他们扔进SSH Tunnel里。

然后，修改/etc/resolv.conf：

    nameserver 127.0.0.1


这样再用Chrome浏览器就应该没有该死的DNS污染问题了。

这个解法不算太好，因为需要在Server端执行命令，如果是一些不给pty的host，就没办法用这个方法了。
可能比较好的方案还是透明代理之类的。

参考资料：

<1>. Issue 29914: DNS queries not forwarded through SOCKS v5 proxies

 [https://code.google.com/p/chromium/issues/detail?id=29914](https://code.google.com/p/chromium/issues/detail?id=29914)

<2>. UDP traffic through SSH tunnel

 [http://superuser.com/questions/53103/udp-traffic-through-ssh-tunnel](http://superuser.com/questions/53103/udp-traffic-through-ssh-tunnel)
