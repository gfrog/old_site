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

_青蛙准备写一个系列文章，介绍一些Debian/Ubuntu里面常用的配置文件。当然，Linux系统中的配置文件多如牛毛，青蛙见过配过的也只是寥寥几个。不过，青蛙会把配置过的配置文件尽量详细的介绍一下，一是方便自己以后查阅，二来也是为广大Debianer做贡献了。_

 

_下面开始正文。_

 

话说Debian系的网卡配置跟Redhat系很不一样，Redhat是放在/etc/sysconfig/network-scripts目录下面的一大堆文件里面，要修改？你一个一个文件来过吧。Debian系的则是存在/etc/network/interfaces文件里面，无论有多少块网卡，统统扔在这个文件里。下面就来看一下这个文件的内容。

 

首先，一个基本的配置大概是下面这个样子：

 

>   
>     
>     <font color="#804040">  1 </font>auto lo
>     <font color="#804040">  2 </font>iface lo inet loopback
>     <font color="#804040">  3 </font>
>     <font color="#804040">  4 </font><font color="#0000ff"># The primary network interface</font>
>     <font color="#804040">  5 </font>auto eth0
>     <font color="#804040">  6 </font>iface eth0 inet static
>     <font color="#804040">  7 </font>     address <font color="#ff00ff">192.168.0.42</font>
>     <font color="#804040">  8 </font>     network <font color="#ff00ff">192.168.0.0</font>
>     <font color="#804040">  9 </font>     netmask <font color="#ff00ff">255.255.255.0</font>
>     <font color="#804040"> 10 </font>     broadcast <font color="#ff00ff">192.168.0.255</font>
>     <font color="#804040"> 11 </font>     gateway <font color="#ff00ff">192.168.0.1</font>
> 
> 






上面的配置中，





第1行跟第5行说明lo接口跟eth0接口会在系统启动时被自动配置；





第2行将lo接口设置为一个本地回环（loopback）地址；





第6行指出eth0接口具有一个静态的（static）IP配置；





第7行-第11行分别设置eth0接口的ip、网络号、掩码、广播地址和网关。









再来看一个更复杂点的：





> 
  
>     
>     <font color="#804040"> 12 </font>auto eth0
>     <font color="#804040"> 13 </font>iface eth0 inet static
>     <font color="#804040"> 14 </font>    address <font color="#ff00ff">192.168.1.42</font>
>     <font color="#804040"> 15 </font>    network <font color="#ff00ff">192.168.1.0</font>
>     <font color="#804040"> 17 </font>    netmask <font color="#ff00ff">255.255.255.128</font>
>     <font color="#804040"> 18 </font>    broadcast <font color="#ff00ff">192.168.1.0</font>
>     <font color="#804040"> 19 </font>    up route add -net <font color="#ff00ff">192.168.1.128</font> netmask <font color="#ff00ff">255.255.255.128</font> gw <font color="#ff00ff">192.168.1.2</font>
>     <font color="#804040"> 20 </font>    up route add default gw <font color="#ff00ff">192.168.1.200</font>
>     <font color="#804040"> 21 </font>    down route del default gw <font color="#ff00ff">192.168.1.200</font>
>     <font color="#804040"> 22 </font>    down route del -net <font color="#ff00ff">192.168.1.128</font> netmask <font color="#ff00ff">255.255.255.128</font> gw <font color="#ff00ff">192.168.1.2</font>
> 
> 






这次，有了一个复杂一些的掩码，和一个比较奇怪的广播地址。还有就是增加的接口启用、禁用时的路由设置；





第19行和20行配置的左右是在接口启用的时候，添加一条静态路由和一个缺省路由；





第21行和22行会在接口禁用的时候，删掉这两条路由配置。





至于配置路由的写法，仔细看，它就是route命令嘛。









继续，下面是一个物理网卡上多个接口的配置方法：





> 
  
>     
>     <font color="#804040"> 23 </font>auto eth0 eth0:1
>     <font color="#804040"> 24 </font>iface eth0 inet static
>     <font color="#804040"> 25 </font>    address <font color="#ff00ff">192.168.0.100</font>
>     <font color="#804040"> 26 </font>    network <font color="#ff00ff">192.168.0.0</font>
>     <font color="#804040"> 27 </font>    netmask <font color="#ff00ff">255.255.255.0</font>
>     <font color="#804040"> 28 </font>    broadcast <font color="#ff00ff">192.168.0.255</font>
>     <font color="#804040"> 29 </font>    gateway <font color="#ff00ff">192.168.0.1</font>
>     <font color="#804040"> 30 </font>iface eth0:1 inet static
>     <font color="#804040"> 31 </font>    address <font color="#ff00ff">192.168.0.200</font>
>     <font color="#804040"> 32 </font>    network <font color="#ff00ff">192.168.0.0</font>
>     <font color="#804040"> 33 </font>    netmask <font color="#ff00ff">255.255.255.0</font>
> 
> 






30行到33行在eth0上配置了另外一个地址，这种配置方法在配置一块网卡多个地址的时候很常见：有几个地址就配置几个接口。冒号后面的数字可以随便写的，只要几个配置的名字不重复就可以。









下面是pre-up和post-down命令时间。这是一组命令（pre-up、up、post-up、pre-down、down、post-down），分别定义在对应的时刻需要执行的命令。





> 
  
>     
>     <font color="#804040"> 34 </font>auto eth0
>     <font color="#804040"> 35 </font>iface eth0 inet dhcp
>     <font color="#804040"> 36 </font>    pre-up [ -f /etc/network/local-network-ok ]
> 
> 






第36行会在激活eth0之前检查/etc/network/local-network-ok文件是否存在，如果不存在，则不会激活eth0。





再更进一步的例子：





> 
  
>     
>     <font color="#804040"> 37 </font>auto eth0 eth1
>     <font color="#804040"> 38 </font>iface eth0 inet static
>     <font color="#804040"> 39 </font>    address <font color="#ff00ff">192.168.42.1</font>
>     <font color="#804040"> 40 </font>    netmask <font color="#ff00ff">255.255.255.0</font>
>     <font color="#804040"> 41 </font>    pre-up /path/to/check-mac-address.sh eth0 <font color="#ff00ff">11:22:33:44:55:66</font>
>     <font color="#804040"> 42 </font>    pre-up /usr/local/sbin/enable-masq
>     <font color="#804040"> 43 </font>iface eth1 inet dhcp
>     <font color="#804040"> 44 </font>    pre-up /path/to/check-mac-address.sh eth1 <font color="#ff00ff">AA:BB:CC:DD:EE:FF</font>
>     <font color="#804040"> 45 </font>    pre-up /usr/local/sbin/firewall
> 
> 






第41行和第44行中，check-mac-address.sh放在/usr/share/doc/ifupdown/examples/目录中，使用的时候需要给它加上可执行权限。这两行命令会检测两块网卡的MAC地址是否为11:22:33:44:55:66和AA:BB:CC:DD:EE:FF，如果正确，则启用网卡。如果MAC地址错误，就不会启用这两块网卡。





第42行和第45行是假定在这两块网卡上分别执行的命令，你可以把它们替换成你想要的任何玩意 ：）





手册上说，这种方法主要是用来检测两块网卡的MAC地址交换（If their MAC addresses get swapped），其实就是两块网卡名互换了，这种情况在debian系统上再常见不过了，主要是因为内核识别网卡的顺序发生了变化。这个问题可以用下面的这种方法来避免。







> 
  
>     
>     <font color="#804040">46 </font>auto eth0 eth1
>     <font color="#804040">47 </font>mapping eth0 eth1
>     <font color="#804040">48 </font>    script /path/to/get-mac-address.sh
>     <font color="#804040">49 </font>    map <font color="#ff00ff">11:22:33:44:55:66</font> lan
>     <font color="#804040">50 </font>    map <font color="#ff00ff">AA:BB:CC:DD:EE:FF</font> internet
>     <font color="#804040">51 </font>iface lan inet static
>     <font color="#804040">52 </font>    address <font color="#ff00ff">192.168.42.1</font>
>     <font color="#804040">53 </font>    netmask <font color="#ff00ff">255.255.255.0</font>
>     <font color="#804040">54 </font>    pre-up /usr/local/sbin/enable-masq <font color="#0000ff">$IFACE</font>
>     <font color="#804040">55 </font>iface internet inet dhcp
>     <font color="#804040">56 </font>    pre-up /usr/local/sbin/firewall <font color="#0000ff">$IFACE</font>
> 
> 






第48行中的get-mac-address.sh也在/usr/share/doc/ifupdown/examples/目录里，也同样要加可执行权限。这个脚本的作用，就是获得每块网卡的MAC地址。





这段配置首先配置了两个逻辑接口（这个名词的定义请参见[debian参考手册](http://www.debian.org/doc/manuals/reference/ch-gateway.zh-cn.html#s-net-reconf)）lan和internet，然后根据网卡的MAC地址，将逻辑接口映射（mapped）到物理接口上去。







再来看下面这段配置：





> 
  
>     
>     <font color="#804040">57 </font>auto eth0  <font color="#804040">58 </font>iface eth0 inet manual  <font color="#804040">59 </font>      up ifconfig <font color="#0000ff">$IFACE</font> <font color="#ff00ff">0.0.0.0</font> up  <font color="#804040">60 </font>      up /usr/local/bin/myconfigscript  <font color="#804040">61 </font>      down ifconfig <font color="#0000ff">$IFACE</font> down 
> 
> 






这段配置只是启用一个网卡，但是ifupdown不对这个网卡设置任何ip，而是由外部程序来设置ip。







最后一段配置，这段配置启用了网卡的混杂模式，用来当监听接口。





> 
  
>     
>     <font color="#804040">177 </font>auto eth0
>     <font color="#804040">178 </font>iface eth0 inet manual
>     <font color="#804040">179 </font>    up ifconfig <font color="#0000ff">$IFACE</font> <font color="#ff00ff">0.0</font>.<font color="#ff00ff">0.0</font> up
>     <font color="#804040">180 </font>      up ip link set <font color="#0000ff">$IFACE</font> promisc on
>     <font color="#804040">181 </font>      down ip link set <font color="#0000ff">$IFACE</font> promisc off
>     <font color="#804040">182 </font>      down ifconfig <font color="#0000ff">$IFACE</font> down
> 
> 








好了，interfaces中对于以太网卡的配置基本上介绍完了。下一篇，青蛙打算介绍interfaces中更进一步的配置，例如ppp、vpn和无线网络等等，敬请期待 :p
