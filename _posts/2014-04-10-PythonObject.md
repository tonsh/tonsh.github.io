---
layout: post
title: Python 的 object 基类
date: 2014-04-10 12:09:33
categories: python
---

今天尝试使用 super() 方法初始化子类时，遇到异常 `TypeError: must be type, not classobj`。原因是 super() 方法只作用于 Python 的新式类(New-Style)。在 Python 中, 继承 object 的类称为新式类。与不继承 object 的类(经典类)的区别可以通过如下查看：

{% highlight python linenos %}
class A:
    def __init__(self):
        print "I'm Old-Style"


class B(object):
    def __init__(self):
        print "I'm New-Style"


a = A()
b = B()

print type(A)
# <type 'classobj'>
print type(B)
# <type 'type'>

print dir(a)
# ['__doc__', '__module__']

print dir(B())
# ['__class__', '__delattr__', '__dict__', '__doc__', '__format__', '__getattribute__', '__hash__', '__init__', '__module__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__']
{% endhighlight %}

由输出可以看出，新式类除了提供了很多内置方法，更重要的是两种方式声明的类类型也不同，参考<a href="http://www.cafepy.com/article/python_types_and_objects/python_types_and_objects.html" target="_blank">Python Types and Objects</a>

回到刚开始的继承问题:
{% highlight python linenos %}
class A:
    def __init__(self):
        print "I'm A"

class B(A):
    def __init__(self):
        super(A, self).__init__()

b = B()
# 报错 TypeError: must be type, not classobj
{% endhighlight %}

如果 A 是第三库提供的类（如 twisted), 不方便更改为新式类, 那么就要放弃 super() 的实现方式，更改如下:

{% highlight python linenos %}
class A:
    def __init__(self):
        print "I'm A"

class B(A):
    def __init__(self):
        A.__init__(self)

b = B()
# I'm A
{% endhighlight %}

