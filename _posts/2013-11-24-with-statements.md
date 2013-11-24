---
layout: post
title: Python 的 with 声明
date:   2013-11-24 11:30:00
categories: python with
---

{% highlight python %}
with open('demo.txt', 'r') as f:
	for line in f.readlines():
		print line
{% endhighlight %}

这段代码实现了打印目标文档的内容。 这里的 with 声明等价于：

{% highlight python %}
f = open('demo.txt', 'r)
for line in f.readlines():
	print line
f.close()
{% endhighlight %}

若忘记了关闭打开的文档，或者在读取过程中遇到异常导致没有关闭文档怎么办，可能我们还会做如下处理：

{% highlight python %}
try:
	f = open('demo.txt', 'r)
	for line in f.readlines():
		print line
finally:
	f.close()
{% endhighlight %}

无论如何我们需要保证程序结束时能关闭文档。这时候就体现了 with 语法的优势，使用 with 声明会自动处理清理操作（如 关闭文档，释放数据库连接，对象销毁等）。

#### with 声明是如何工作的呢？

一个对象如果想使用 with 声明，对象类必须实现 `__enter__`, `__exit__` 两个魔术方法。跟在 with 后的对象初始化后，会调用 `__enter__` 方法。

我们来看看 BufferedReader 的 Python 源代码是如何实现处理这两个魔术方法的。

``` 
注：
open() returns a file object whose type depends on the mode, … … in read binary mode, it returns a **BufferedReader**; in write binary and append binary modes, it returns a **BufferedWriter**, and in read/write mode, it returns a **BufferedRandom.**

Python 的内置方法 Open() 会根据第二个参数 mode 返回不同的文件对象. 如 mode='r', 则返回 BufferedWriter 类。
```

Python-2.7.4/Lib/_pyio.py  代码顺序做了调整

{% highlight python %}
# 根据继承关系找下去
class BufferedReader(_BufferedIOMixin):
	# blablabla ...
	
class _BufferedIOMixin(BufferedIOBase):
	# blablabla ...
	
class BufferedIOBase(IOBase):
	# blablabla ...
	
class IOBase:
	# blablabla ...

	__closed = False
	
	@property
    def closed(self):
        """closed: bool.  True iff the file has been closed. """
        return self.__closed

	# 检查文档是否关闭
    def _checkClosed(self, msg=None):
        """Internal: raise an ValueError if file is closed"""
        if self.closed:
            raise ValueError("I/O operation on closed file."
                             if msg is None else msg)
                             
	
	def flush(self):
    	"""Flush write buffers, if applicable. This is not implemented for read-only and non-blocking streams.
        """
        self._checkClosed()
        

	# 我们调用的 f.close() 就是这个
    def close(self):
        """Flush and close the IO object.This method has no effect if the file is already closed.
        """
        if not self.__closed:
            try:
                self.flush()
            finally:
                self.__closed = True

	# 检查文件是否能打开，self.closed 初始值为 False
	def __enter__(self):
        self._checkClosed()
        return self

    def __exit__(self, *args):
        self.close()
{% endhighlight %}

with open('demo.txt', 'r') as f, 文件对象 f 一旦创建，就会执行 f.`__enter__`() 方法, 当跳出 with 声明时，会执行 f.`__exit__`() 方法，关闭文件。

到这里我们知道了一般在需要 try ... finally ... 的时候会选择使用 with 声明； with 声明的对象类必须实现 `__enter__`, `__exit__` 两个方法; 

想到一个好恶心的例子，曾经有人大喊，上完厕所要冲厕! 那么我们实现一个高端大气上档次的自动冲厕的功能好了 >_<！［大汗］

{% highlight python lineno %}
class AutoFlushing(object):
	# 厕所初始情况下是干净的
	_flushed = True
	
	def have_a_piss(self):
		# 自行YY吧
		pass
	
	def flush(self):
		# 冲水完毕
		self._flushed = True
		
	def __enter__(self):
		self._flushed = False
		
	def __exit__(self):
		self.flush()
		
# 小明来上厕所，会自动冲水
with AutoFlushing() as fl:
	fl.have_a_piss()
{% endhighlight %}

至此写完，节操掉一地，掩面逃走！
