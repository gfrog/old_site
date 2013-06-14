---
layout: post
title: "Python Exception class 中的args属性"
date: 2013-05-13 13:09
comments: true
categories: 
 - python[Python] 
---

话说今天青蛙遇到了一个bug，在某个Exception里抛出了TypeError Exception，
青蛙仔细查了半天，才想到Exception有一个内置属性args，刚好跟这个Exception
定义中的args重名。

首先看下这个Exception的定义：

{% codeblock lang:python %}
In [4]: class E(Exception):
    def __init__(self, a, b, c):
        Exception.__init__(self)
        self.a = a
        self.args = b
        self.c = c
    def __str__(self):
        return "a = %r, b = %r, c = %r" % (self.a, self.args, self.c)
....:
{% endcodeblock %}

当这个Exception被触发时，又引发了一个TypeError：

{% codeblock lang:python %}
In [5]: raise E('1', None, '3')
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-6-b57acf162917> in <module>()
----> 1 raise E('1', None, '3')

<ipython-input-5-fac85dd51acb> in __init__(self, a, b, c)
      2     def __init__(self, a, b, c):
      3         Exception.__init__(self)
      4         self.a = a
----> 5         self.args = b
      6         self.c = c
      7     def __str__(self):

TypeError: 'NoneType' object is not iterable
{% endcodeblock %}

这就是Exception的args属性在作怪了，如果把self.args的名字换一下，世界又恢复正常了。

{% codeblock lang:python %}
In [2]: class E(Exception):
    def __init__(self, a, b, c):
        Exception.__init__(self)
        self.a = a
        self.b = b
        self.c = c
    def __str__(self):
        return "a = %r, b = %r, c = %r" % (self.a, self.b, self.c)
....:

In [3]: raise E('1', None, '3')
---------------------------------------------------------------------------
E                                         Traceback (most recent call last)
<ipython-input-3-b57acf162917> in <module>()
----> 1 raise E('1', None, '3')

E: a = '1', b = None, c = '3'

{% endcodeblock %}

背后的原因呢，就是Python不管这个args是啥，都会转换成一个tuple，
但是当args是一个NoneType，问题就来了。

{% codeblock Objects/exceptions.c lang:c %}
  17 /*
  18  *    BaseException
  19  */
  20 static PyObject *
  21 BaseException_new(PyTypeObject *type, PyObject *args, PyObject *kwds)
  22 {
  23     PyBaseExceptionObject *self;
  24     
  25     self = (PyBaseExceptionObject *)type->tp_alloc(type, 0);
  26     if (!self)
  27         return NULL;
  28     /* the dict is created on the fly in PyObject_GenericSetAttr */
  29     self->dict = NULL;
  30     self->traceback = self->cause = self->context = NULL;
  31         
  32     self->args = PyTuple_New(0);
  33     if (!self->args) {
  34         Py_DECREF(self);
  35         return NULL;
  36     }   
  37     
  38     return (PyObject *)self;
  39 }
{% endcodeblock %}

找到原因，解决起来就很简单了。给args换个名字，一切ok。
当然编码时的坏习惯才是导致这种bug最大的根源，以后千万不能乱写变量名了。

