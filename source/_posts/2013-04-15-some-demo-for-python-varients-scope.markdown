---
layout: post
title: Python 中关于变量作用域的一些demo 
slug: "some-demo-for-python-varients-scope"
date: 2013-04-15 22:41
comments: true
categories: 
 - Python[python]
---

其实青蛙不是很想在blog里面说编程方面的东西，毕竟青蛙自己只
是个半调子，怕言多有失。不过这次遇到了一个很常见又很有趣的
问题，记下一笔，权当取乐。

话说上周一个同事在patch里面使用了一种比较hacker的方法\[1\]：

使用定义list成员的方式把函数的局部变量传递给sub-function并
在sub-function修改其内容。

这种做法被其他同事猛烈的抨击，实际上在python2.x中，因为语言
实现的问题，嵌套的子函数是没法直接写它的上层函数的变量的。

这个问题之前在Team内部没有引起足够的重视，大家习以为常的在
子函数里直接给一个父函数的变量赋值，结果都是错的，这个赋值
操作会直接引发异常。

有人总结了一下python嵌套子函数访问变量的行为\[2\]:

    In Python 2.x, it is not possible to modify a non-local variable;
    you have either read-only access to a global or non-local variable,
    or read-write access to a global variable by using the global statement,
    or read-write access to a local variable (the default). 
    That's just the way it's been designed (probably for performance and purity).

    In Python 3, the nonlocal statement has been introduced with
    a similar effect to global, but for an intermediate scope.

青蛙根据上面的总结写了几个函数的demo，然后引发了更深入的讨论，
于是终于引出了PEP-3104\[3\]。

这是青蛙最开始的例子，

`f1()`是最基本的函数调用，其中操作的foo也是f1内部的foo。

`f2()`用到了global关键字，声明全局的foo变量，这是f2操作的就是全局
的foo了。

`f3()`是错误的操作，子函数会直接抛出异常。

`f4()`是使用list的方法避开命名空间的问题，因为在嵌套函数中依然可以
读取上层函数的变量，于是`sub_func`可以访问到那个list foo，然后可以
进一步操作其成员。

`f5()`实际上操作的还是全局的foo。

{% codeblock first.py lang:python %}
#!/usr/bin/python

foo = "I'm global"

def f1():
    foo = "I'm a string"
    print "foo in f1: %s" % foo

def f2():
    global foo
    foo += " and modified in f2"
    print "foo in f2: %s" % foo

def f3():
    foo = "I'm in f3"

    def sub_func():
        try:
            foo += " and modified in f3's sub function"
            print "foo in sub_func of f3: %s" % foo
        except Exception, e:
            print "oops in sub_func of f3: %s" % e

    sub_func()
    print "foo in f3: %s" % foo

def f4():
    foo = ["I'm in f4"]

    def sub_func():
        try:
            foo[0] += " and modified in f4's sub function"
            print "foo in sub_func of f4: %s" % foo[0]
        except Exception, e:
            print "oops in sub_func of f4: %s" % e

    sub_func()
    print "foo in f4: %s" % foo[0]

def f5():
    foo = "I'm in f5"

    def sub_func():
        global foo
        try:
            foo += " and modified in f5's sub function"
            print "foo in sub_func of f5: %s" % foo
        except Exception, e:
            print "oops in sub_func of f5: %s" % e

    sub_func()
    print "foo in f5: %s" % foo

f1()
f2()
f3()
f4()
f5() 

{% endcodeblock%}

在PEP-3104中，定义了一个`nonlocal`关键字，这个字的作用就是声明
一个变量既不是`local`的也不是`global`的，而是上层函数（outer scope）
中的。

但是在Python2.x中仍然没有这个`nolocal`关键字，PEP-3104中也给出
了两种实现来解决这类问题：

{% codeblock 3104-1.py lang:python %}
#!/usr/bin/python
def f5():
     f5.foo = "I'm in f5"

     def sub_func():
         try:
             f5.foo += " and modified in f5's sub function"
             print "foo in sub_func of f5: %s" % f5.foo
         except Exception, e:
             print "oops in sub_func of f5: %s" % e

     sub_func()
     print "foo in f5: %s" % f5.foo

f5()
{% endcodeblock%}

{% codeblock 3104-2.py lang:python %}
#!/usr/bin/python
class Namespace:
    pass

def f5():
     ns = Namespace()
     ns.foo = "I'm in f5"

     def sub_func():
         try:
             ns.foo += " and modified in f5's sub function"
             print "foo in sub_func of f5: %s" % ns.foo
         except Exception, e:
             print "oops in sub_func of f5: %s" % e

     sub_func()
     print "foo in f5: %s" % ns.foo

f5()
{% endcodeblock %}

这两种方法本质上就是给变量bind到了其他地方（一个类成员，
一个函数成员）。这样在嵌套函数里可以访问一个全局的类的
成员来达到访问这个变量的目的。
本质上还是把变量扔进了`globals()`，随意青蛙并不推崇这种
方式。

而在上层函数定义list或者dict的方法，看起来hacker，所以
青蛙也不是太喜欢。

青蛙自己的方式是把上层函数的变量作为参数传入嵌套函数，
然后在嵌套函数中把修改后的结果作为返回值返回。虽然这种
方法看起来像C，有点不pythonic，但是免去了那些命名空间
猜来猜去的麻烦。

{% codeblock final-f5.py lang:python %}
def f5():
    foo = "I'm in f5"

    def sub_func(foo):
        try:
            foo += " and modified in f5's sub function"
            print "foo in sub_func of f5: %s" % foo
        except Exception, e:
            print "oops in sub_func of f5: %s" % e
        return foo

    foo = sub_func(foo)
    print "foo in f5: %s" % foo
{% endcodeblock %}


\[1\]. [How do I change nesting function's variable in the nested function](http://stackoverflow.com/questions/6198709/how-do-i-change-nesting-functions-variable-in-the-nested-function)

\[2\]. [python can't access nonlocal variable before local variable is defined with same name](http://stackoverflow.com/questions/13282910/python-cant-access-nonlocal-variable-before-local-variable-is-defined-with-same)

\[3\]. [PEP-3104](http://www.python.org/dev/peps/pep-3104/)
