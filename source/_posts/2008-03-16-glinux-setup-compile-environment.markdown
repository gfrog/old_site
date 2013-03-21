---
comments: true
date: 2008-03-16 19:16:47
layout: post
slug: glinux-setup-compile-environment
title: glinux开发摘要(1)--编译环境制作
wordpress_id: 296
categories:
- linux[Linux]
---

在uty同学的积极鼓励下，青蛙开始了glinux下一个版本的制作（关于glinux的历史，欢迎回顾青蛙从前的日志）。
一开始，青蛙准备利用debian现成的编译环境，但是后来突然想自己构建一个工具链比较好。工具稳定下来，做出来的东西才比较有保障。于是，青蛙从新拾起lfs，开始第二轮lfs征程，不过这次lfs只做三分之一，青蛙的目标就是：lfs6.3的Toolschain。
具体操作过程没啥好说的，简单的step by step就好，但是在制作过程中，遇到了几个bug（可能也算不上bug，可能是青蛙系统的原因）。

这第一个，就是在glibc-2.5.1这步，会遇到下面这个错误：


> mawk: scripts/gen-sorted.awk: line 19: regular expression compile failed (bad class -- [], [^] or [)
/[^
mawk: scripts/gen-sorted.awk: line 19: syntax error at or near ]
mawk: scripts/gen-sorted.awk: line 19: runaway regular expression /, "", subd ...


再往上翻还有这么一句：


> mawk: scripts/gen-sorted.awk: line 19: syntax error at or near ]


拿google一划拉，发现这个错误是因为青蛙系统上用了mawk的问题，据说换用gawk可以解决。
在babble.com上面[有人给出了一个patch](http://www.nabble.com/small-fix-for-crosstool-with-glibc-2.7-td13448350.html)，很简单，给三个地方加个“”就ok了。但是这个patch是给glibc2.7用的，在lfs6.3里带的glibc2.5.1里面没法直接用。青蛙按图索骥，改了这个gen-sorted.awk，就没有上头那些错误了。
虽说一个补丁可以解决这个问题，但是[有前辈告诉我们](http://www.linuxquestions.org/questions/showthread.php?p=2969014#post2969014)，lfs里面的用的都是awk的脚本，如果用mawk很有可能不会有正确的结果，所以建议大家还是安装gawk的好。
gawk源码[在这里下载](http://ftp.gnu.org/pub/gnu/gawk/)，debian系的系统只需要aptitude install gawk就好了。其他发行版要注意检查/etc/alternatives/awk是不是正确指向了gawk。

（顺路说一句，cpu在卖力的cc的时候正好可以过来写blog，真是很不错，可以随时把过程记录下来 :p）

第二个错误是在glibc2.5.1的make check过程中：


> /tools/bin/ld: cannot find -lstdc++
collect2: ld returned 1 exit status
make[2]: *** [/mnt/lfs/sources/glibc-build/nptl/tst-cancel24] Error 1
make[2]: Target `tests' not remade because of errors.
make[1]: *** [nptl/tests] Error 2
make[1]: Target `check' not remade because of errors.
make: *** [check] Error 2


青蛙没有找到这个错误的原因，大概就像lfs文档里说的，第一次编译lfs跟宿主系统依赖关系太大的原因造成的。

第三个错误发生在bash3.2的make过程中，它会提示没有找到yacc这个程序，解决方法很简单，通过apt装上就好了。

到这里，工具链的制作就完成了，下一步就可以制作系统的目录结构并编译内核了。
