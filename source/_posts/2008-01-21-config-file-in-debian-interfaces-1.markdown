---
comments: true
date: 2008-01-21 23:27:22
layout: post
slug: config-file-in-debian-interfaces-1
title: Debian里的配置文件：网卡配置/etc/network/interfaces（1）
wordpress_id: 279
categories:
- linux[Linux]
---

_青蛙准备写一个系列文章，介绍一些Debian/Ubuntu里面常用的配置文件。
当然，Linux系统中的配置文件多如牛毛，青蛙见过配过的也只是寥寥几个。
不过，青蛙会把配置过的配置文件尽量详细的介绍一下，
一是方便自己以后查阅，二来也是为广大Debianer做贡献了。_

_下面开始正文。_

话说Debian系的网卡配置跟Redhat系很不一样，
Redhat是放在/etc/sysconfig/network-scripts目录下面的一大堆文件里面，
要修改？你一个一个文件来过吧。
Debian系的则是存在/etc/network/interfaces文件里面，
无论有多少块网卡，统统扔在这个文件里。下面就来看一下这个文件的内容。

首先，一个基本的配置大概是下面这个样子：

{% codeblock lang:ruby %}
auto lo
iface lo inet loopback

# The primary network interface
auto eth0
iface eth0 inet static
     address 192.168.0.42
     network 192.168.0.0
     netmask 255.255.255.0
     broadcast 192.168.0.255
     gateway 192.168.0.1
{% endcodeblock %}

上面的配置中，

第1行跟第5行说明lo接口跟eth0接口会在系统启动时被自动配置；

第2行将lo接口设置为一个本地回环（loopback）地址；

第6行指出eth0接口具有一个静态的（static）IP配置；

第7行-第11行分别设置eth0接口的ip、网络号、掩码、广播地址和网关。


再来看一个更复杂点的：

{% codeblock lang:ruby %}
auto eth0
iface eth0 inet static
    address 192.168.1.42
    network 192.168.1.0
    netmask 255.255.255.128
    broadcast 192.168.1.0
    up route add -net 192.168.1.128 netmask 255.255.255.128 gw 192.168.1.2
    up route add default gw 192.168.1.200
    down route del default gw 192.168.1.200
    down route del -net 192.168.1.128 netmask 255.255.255.128 gw 192.168.1.2
{% endcodeblock %}

这次，有了一个复杂一些的掩码，和一个比较奇怪的广播地址。
还有就是增加的接口启用、禁用时的路由设置；

第7行和8行配置的左右是在接口启用的时候，添加一条静态路由和一个缺省路由；

第9行和10行会在接口禁用的时候，删掉这两条路由配置。

至于配置路由的写法，仔细看，它就是route命令嘛。


继续，下面是一个物理网卡上多个接口的配置方法：

{% codeblock lang:ruby %}
auto eth0 eth0:1
iface eth0 inet static
    address 192.168.0.100
    network 192.168.0.0
    netmask 255.255.255.0
    broadcast 192.168.0.255
    gateway 192.168.0.1
iface eth0:1 inet static
    address 192.168.0.200
    network 192.168.0.0
    netmask 255.255.255.0
{% endcodeblock %}

8行到11行在eth0上配置了另外一个地址，
这种配置方法在配置一块网卡多个地址的时候很常见：有几个地址就配置几个接口。
冒号后面的数字可以随便写的，只要几个配置的名字不重复就可以。


下面是pre-up和post-down命令时间。
这是一组命令（pre-up、up、post-up、pre-down、down、post-down），分别定义在对应的时刻需要执行的命令。

{% codeblock lang:ruby %}
auto eth0
iface eth0 inet dhcp
    pre-up [ -f /etc/network/local-network-ok ]
{% endcodeblock %}

第3行会在激活eth0之前检查/etc/network/local-network-ok文件是否存在，如果不存在，则不会激活eth0。


再更进一步的例子：

{% codeblock lang:ruby %}
auto eth0 eth1
iface eth0 inet static
    address 192.168.42.1
    netmask 255.255.255.0
    pre-up /path/to/check-mac-address.sh eth0 11:22:33:44:55:66
    pre-up /usr/local/sbin/enable-masq
iface eth1 inet dhcp
    pre-up /path/to/check-mac-address.sh eth1 AA:BB:CC:DD:EE:FF
    pre-up /usr/local/sbin/firewall
{% endcodeblock %}

第5行和第8行中，check-mac-address.sh放在/usr/share/doc/ifupdown/examples/目录中，
使用的时候需要给它加上可执行权限。
这两行命令会检测两块网卡的MAC地址是否为11:22:33:44:55:66和AA:BB:CC:DD:EE:FF，如果正确，则启用网卡。
如果MAC地址错误，就不会启用这两块网卡。

第6行和第9行是假定在这两块网卡上分别执行的命令，你可以把它们替换成你想要的任何玩意 ：）

手册上说，这种方法主要是用来检测两块网卡的MAC地址交换（If their MAC addresses get swapped），
其实就是两块网卡名互换了，这种情况在debian系统上再常见不过了， 主要是因为内核识别网卡的顺序发生了变化。
（__更新： 2013-05-19 自从有了udev，这种情况应该比较少发生了。__）

这个问题可以用下面的这种方法来避免。


{% codeblock lang:ruby %}
auto eth0 eth1
mapping eth0 eth1
    script /path/to/get-mac-address.sh
    map 11:22:33:44:55:66 lan
    map AA:BB:CC:DD:EE:FF internet
iface lan inet static
    address 192.168.42.1
    netmask 255.255.255.0
    pre-up /usr/local/sbin/enable-masq $IFACE
iface internet inet dhcp
    pre-up /usr/local/sbin/firewall $IFACE
{% endcodeblock %}

第3行中的get-mac-address.sh也在/usr/share/doc/ifupdown/examples/目录里，
也同样要加可执行权限。
这个脚本的作用，就是获得每块网卡的MAC地址。

这段配置首先配置了两个逻辑接口（这个名词的定义请参见\[1\]）lan和internet，
然后根据网卡的MAC地址，将逻辑接口映射（mapped）到物理接口上去。


再来看下面这段配置：

{% codeblock lang:ruby %}
auto eth0
iface eth0 inet manual
    up ifconfig $IFACE 0.0.0.0 up
    up /usr/local/bin/myconfigscript
    down ifconfig $IFACE down
{% endcodeblock %}

这段配置只是启用一个网卡，但是ifupdown不对这个网卡设置任何ip，而是由外部程序来设置ip。


最后一段配置，这段配置启用了网卡的混杂模式，用来当监听接口。

{% codeblock lang:ruby %}
auto eth0
iface eth0 inet manual
    up ifconfig $IFACE 0.0.0.0 up
    up ip link set $IFACE promisc on
    down ip link set $IFACE promisc off
    down ifconfig $IFACE down
{% endcodeblock %}


好了，interfaces中对于以太网卡的配置基本上介绍完了。
下一篇，青蛙打算介绍interfaces中更进一步的配置，例如ppp、vpn和无线网络等等，敬请期待 :p

__更新： 2013-05-19 这个系列没有继续下去确实很可惜。
不过5年过去了，Linux系统的网络配置发生了巨大的变化。
所以，重新再写一个关于Linux的系统配置的文章对于SysAdmin
来说还是有意义的。那么，就让青蛙找时间继续吧。__

Reference:

\[1\] [debian参考手册](http://www.debian.org/doc/manuals/reference/ch-gateway.zh-cn.html#s-net-reconf)
