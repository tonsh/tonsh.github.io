---
layout: post
title: Python 里的字符串赋值
date:   2013-11-21 12:09:33
categories: jekyll update
---

在 python 里字符串是被当作不可变变量处理的。

{% highlight bash %}
>>> a = 'a'
>>> b = a
>>> a == b
True
>>> b = 'b'
>>> a
a
>>> b
b
{% endhighlight %}

说明 Python 没有把字符串赋值当作引用传递处理。

继续研究以下代码，查看字符串赋值过程中，变量标识是如何变化的：

##### 当变量b初次赋值时，与 a 指向相同的内存空间。

{% highlight bash %}
>>> a = 'a'
>>> id(a)
4464547200
>>> b = a
>>> id(b)
4464547200
{% endhighlight %}

##### 若 a或b 任意一个值改变时, 被改变的变量指向的内存空间会变化。

{% highlight bash %}
>>> a = 'a'
>>> b = a
>>> b = 'b'
>>> id(b)
4464546880
>>> id(a)
4464547200
{% endhighlight %}

##### 如果增加字符串的长度？

{% highlight bash %}
>>> a = 'a'
>>> id(a)
4464547200
>>> a += 'b'
>>> id(a)
4465546784
>>> a += 'c'
>>> id(a)
4465546784
{% endhighlight %}

神奇的事情发生了，第一次修改字符串 a 时，理论上应该只申请两个字符的空间，但是Python会额外多分配几个字符（大于2个字符），所以之后的继续增加a的长度，a指向的地址并没有变化，直到分配的空间占满后，a 的地址才会变化，下面给些具体的数值，大家感受一下：

{% highlight python linenos %}
a = ''
aid = id(a)

for i in range(0, 50000):
    a += 'a'
    aid_2 = id(a)

    if aid_2 != aid:
        print i + 1
        aid = aid_2
{% endhighlight %}

输出结果：

```
1
2
4
12
20
28
36
44
52
60
68
76
84
92
100
108
116
124
132
140
148
156
164
172
180
188
196
204
212
220
236
284
636
652
668
684
972
988
2012
2524
4572
36316
36828
37340
37852
38364

```
