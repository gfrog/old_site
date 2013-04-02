---
comments: true
date: 2008-01-10 21:57:39
layout: post
slug: how-to-configure-scalable-multicasting-on-helix-server
title: How do I configure scalable multicasting on Helix Server
categories:
    - linux[Linux]
---

_原文地址在
[这里](http://na3.salesforce.com/_ui/selfservice/pkb/PublicKnowledgeSolution/d?orgId=00D500000007Hzn&id=501500000007taI&retURL=%2Fsol%2Fpublic%2Fsolutionbrowser.jsp%3Fsearch%3Dmulticast%26cid%3D02n500000008z1P%26orgId%3D00D500000007Hzn%26t%3D4&ps=1)
我把它翻译成了中文,方便查看。_



* * *

Helix Server可以通过web方式的管理工具配置为使用可扩展多播，这篇文档只关注可扩展多播功能的配置。
关于Helix Server功能的完整细节可以在管理向导中找到。
Helix Server 管理向导：（第8章 多播）
[http://www.realnetworks.com/support/docs.html](http://www.realnetworks.com/support/docs.html)

1.打开Helix Server的web管理界面。

2. 点击广播分发，可扩展多播。

3. 默认的加载点是/scalable/。RealNetworks建议你使用这个默认值，但是你可以随意更改它。

4. 要建立一个新的频道，点击“+”图标，然后在“编辑频道描述”文本框中为这个多播连接输入一个名称。

5. 在“启用频道”下拉框中选择“是”来启用可扩展多播。

6.在“启用SAP”下拉框中选择"是"来发布这个多播连接。

7. 在“路径”文本框，添加要被广播发布的流名称。如果要使用所有的广播流，那么就保留默认的星号（\*）。

8. 下一步，定义可扩展多播使用的端口号和地址。

a. 在“端口范围”文本框，输入要使用的端口号。
（青蛙注：这个端口号有一个变态的要求，就是要以偶数端口开始，并且范围中要包含偶数个端口，例如5000-5003。）

b. 在“IP地址范围”中，输入要使用的地址。
Helix Server使用这个地址范围中第一个可用的地址。
如果使用一个单独的地址而不是一个地址范围，在这两个文本框中输入一样的地址。

9. 在“生存时间”文本框中，指出多播包可以在你的网络中传送多远。
（注：原文就这么写的，TTL每过一个路由器减1，减到0这个包就被丢弃。）

10. 输入一个超时时间，这个时间表示一个客户端停止等待多播包或者使用交替URL之前所经过的时间（秒）。

11. 在“重新使用地址”中，如果你想给在一个视频中的每个流都使用一个单独的地址，那么就选“否”。
如果你想在两个流中都使用一个地址，那选择“是”。

12. 下一个选项是选择是否将收不到多播包的播放器转换为单播。这个功能只在使用RealNetworks的播放器时才有效。

a. 在“转换为单播”列表框中，如果你不想让客户端转换成单播，那么就选择“否”。
当播放一个高码率的节目时应该选择“否”，因为大量的单播流会消耗太多的带宽。如果你选择了“否”，可以忽略下一个文本框。

b. 要在同一个Helix Server中使备份的单播有效，保持“交替的单播地址”文本框是空的。
如果你想将单播转换到另外一个Helix Server上，需要填上那个服务器的地址和路径。下面就是一个例子：

rtsp://helixserver.example2.com/broadcast/vivaldi.rm

如果你不想把播放器转向到一个交替流，也可以把他们指向一个web页面来输出信息，
像“这个节目只对支持单播的RealPlayer有效。
”在“交替的单播地址”文本框中，输入你的web页的URL，像下面这种：

http://www.example.com/no_multicast.html

13. （可选）下一个选项是控制是否要把客户端的统计信息记录到Helix Server、Web服务器或者不记录。

14. 点击应用。

Helix Server就被配置为使用可扩展多播了。

可扩展多播的连接有如下形式：

http://address:port/mount_point/path/file.rm.sdp

 ------传说中的分割线------

这两天yupoo不知道在倒腾啥,没法传图片.青蛙只好把没图的文章发出来了.等yupoo好了再传图吧。
