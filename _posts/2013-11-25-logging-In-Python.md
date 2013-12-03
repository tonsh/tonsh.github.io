---
layout: post
title: Python 的日志模块
date:   2013-11-27 23:50:33
categories: python logging
---


平时调试程序时习惯使用 print 打印调试信息，通过之后再把它们删掉。如:

{% highlight python %}
print 'server start ...'
s = server.start()

print 'do something ...'
s.do_something()

s.stop()
print 'server stop ...'
{% endhighlight %}

在一些简单的调试程序中可以这样做，但如果是在生产环境或者一个很复杂的项目里最好不要这样做。首先，这些调试信息无法被灵活保存在日志里，即使被保存了，也很难在日志看到更详细的信息，如错误级别，时间，出错地点等等。 在项目中记录日志是很重要的。倘若有一天顾客向你反馈问题，这时候首先想到的应该是去查看日志信息了解到底发生了什么错误，前提是你的日志记录很完善。

Pyhton 标准库提供了灵活易用的 logging 模块。一般用法如下：

{% highlight python %}
import logging

logging.basicConfig(format="%(asctime)s %(name)s %(levelname)s %(message)s",
                    level=logging.DEBUG)

logger = logging.getLogger(__name__)

logger.error('This is a error message !!')
logger.warning('This is a Warning message!')
logger.info('This is an info message!')
logger.debug('This is a debug message !')
{% endhighlight %}

输出结果如下:

```
2013-11-27 18:48:42,591 __main__ ERROR This is a error message !! 
```

basicConfig 可以配置日志的输出格式，日志级别，目标输出流等等。日志级别可参考<a href="http://docs.python.org/2/library/logging.html" target="_blank">Python 文档</a>, 低于日志级别的消息将被忽略。如将上例中的日志级别改为 logging.EBUG，输出结果如下：

```
2013-11-27 18:49:43,659 __main__ ERROR This is a error message !!
2013-11-27 18:49:43,658 __main__ WARNING This is a Warning message!
2013-11-27 18:49:43,658 __main__ INFO This is an info message!
2013-11-27 18:49:43,658 __main__ DEBUG This is a debug message !
```

logger 默认将日志内容输出到终端。还可以输出到文件，发送到邮箱或远程服务器等，这里不做详细介绍，可以参考<a href="http://docs.python.org/2/library/logging.handlers.html#module-logging.handlers" target="_blank">Python 文档</a>。下面以文件为例:

{% highlight python %}
import logging

logger = logging.getLogger(__name__)
logger.setLevel(logging.INFO)

# create a file handler
handler = logging.FileHandler('./output.log')
# 如果没有设置handler的日志级别，会使用 logger 的日志级别
handler.setLevel(logging.ERROR)
fmt = logging.Formatter('%(asctime)s %(name)s %(levelname)s %(message)s') 
handler.setFormatter(fmt) 
# add the handler to logger
logger.addHandler(handler) 
 
logger.info('This is an info message!') 
logger.debug('This is a debug message !') 
logger.warning('This is a Warning message!') 
logger.error('This is a error message !!') 
{% endhighlight %}

output.log

```
2013-11-27 18:49:43,659 __main__ ERROR This is a error message !!
```

### 日志级别的使用习惯
以下为我自己理解的使用习惯，仅供参考:

* debug: 用于需要记录调试信息的地方，如循环体内计数

{% highlight python %}
for i in range(10):
    logger.debug('number: %s', i)

    # do something with for-loop
{% endhighlight %}

* Info: 用于记录运行状态等

{% highlight python %}
def demo():
    logger.info('demo method start ...')
    # do something
    logger.info('demo method end ...')
{% endhighlight %}

* warning: 用于逻辑异常但不是错误时

{% highlight python %}
if pwd != user.password:
    logger.warning('Login password error')
{% endhighlight %}

* error: 当遇到错误时

{% highlight python %}
try:
    a = 1 / 0
except ZeroDivisionError as e:
    logger.error(e.message)
{% endhighlight %}


### 记录异常

当程序抛出异常时，我们更希望将所有的异常信息写进日志，方便查看具体的出错信息。这时可以设置 logger 的 exc_info 参数为 True, logger 将会记录完整的异常信息。

{% highlight python %}
try:
    a = 1 / 0
except Exception as e:
    logger.error('have a exception: ', exc_info=True)
{% endhighlight %}

输入如下:

```
2013-11-27 20:23:42,092 __main__ ERROR have a exception:
Traceback (most recent call last):
  File "mylog.py", line 26, in <module>
    a = 1 / 0
ZeroDivisionError: integer division or modulo by zero 
```

### 切分日志文件 (Rotating file handler)

使用 FileHandler 写日志，日志文件会一直增长下去。也许某天它会占满你的磁盘。为了避免这种情况，建议使用 RotatingFileHandler 代替 FileHandler。

{% highlight python %}
import logging
import logging.handlers

logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)

# create rotating file handler
handler = logging.handlers.RotatingFileHandler('output.log',
                                                maxBytes=20,
                                                backupCount=5)
fmt = logging.Formatter('%(asctime)s %(name)s %(levelname)s %(message)s') 
handler.setFormatter(fmt) 
logger.addHandler(handler)

for i in range(10):
    logger.info('num: %s', i)
{% endhighlight %}

最后生成 6 个 output 文件，日志信息分别存放在 6 个文件里。backupCount 为最多保留的日志文件数，超出这些数量的文档将不会被保留。所以如果 backupCount=6 则最多能生成 7 个日志文件。(文件数 = backupCount + 1)

### logger 的名字

以上的例子都是使用 `__name__` 作为 logger 的 name 参数。Python 的 `__name__` 是当前包的名字。在foo.bar.mylog文件里调用logging.getLogger(`__name__`)等价于logging.getLogger('foo.bar.mylog')。"foo.bar.mylog", "foo.bar" 都是 "foo" 的子级，子级会继承父级的配置。

### logger 的配置文件

截至到这里，我们都是在程序中直接对 logger 进行配置，其实配置 logger 最好使用logging.config。配置可以是字典，由 dictConfig 解析, 也可以是文件，由 fileConfig 解析，fileConfig 基于 ConfigParser 格式。配置必须包含 loggers, heandlers, formatters 三个部分。<a href="http://docs.python.org/2/library/logging.config.html" target="_blank">参考手册</a>

假设在 logger 部分定义了一个个名为 "foo" 的 logger, 那么就要同时定义一个名为 [logger_foo]的部分；`[handlers], [handler_foo], [formatters], [formatter_foo]` 也都要有对 foo 的定义。下面以三个名为 "root", "foo", "bar" 的 logger 为例,这里需要了解 root 是 logger 的默认名字。配置 root 输出到终端, foo 输出到文件，bar 输出到切分文件，它们的输出格式，日志级别等可以不同:

logging.conf

```
[loggers]
keys = root, foo, bar

[handlers]
keys = root, foo, bar

[formatters]
keys = root, foo, bar

[logger_root]
level=DEBUG
handlers=root

[logger_foo]
level=DEBUG
handlers=FileHandler
qualname=foo
propagate=0

[logger_bar]
level=DEBUG
handlers=RotatingFileHandler
qualname=bar
propagate=0

[handler_root]
class=StreamHandler
level=INFO
formatter=root
args=(sys.stdout,)

[handler_foo]
class=FileHandler
level=WARNING
formatter=foo
args=('foo.log', 'a')

[handler_bar]
class=handlers.RotatingFileHandler
level=DEBUG
formatter=bar
args=('bar.log', 'a', 20, 9)

[formatter_root]
format=simpleFormatter
datefmt=

[formatter_foo]
format=%(asctime)s %(name)s %(levelname)s %(message)s
datefmt=%Y-%m-%d %H-%M-%S
class=logging.Formatter

[formatter_bar]
format=%(asctime)s %(name)s %(levelname)s %(message)s
datefmt=%Y-%m-%d %H-%M-%S
class=logging.Formatter
```

{% highlight python %}
import logging
import logging.config

logging.config.fileConfig('cfg.ini') 
 
logger = logging.getLogger() 
logger_foo = logging.getLogger('foo') 
logger_bar = logging.getLogger('bar') 
 
for i in range(20): 
    logger.info('num: %s', i) 
    logger_foo.error('num: %s', i) 
    logger_bar.info('num: %s', i) 
{% endhighlight %}

需要注意的是加载配置信息时，之前创建的 logger 会失效。

{% highlight python %}
import logging
import logging.config

logger_foo = logging.getLogger(__name__)

# logger_foo 被创建之后加载了配置信息
logging.config.dictConfig(dict_config)

logger.INFO('logger is not work!')
{% endhighlight %}

最好的做法是在需要 logger 的时候才去获取。

<pre class="reference">
参考资料:

    <a href="http://victorlin.me/posts/2012/08/26/good-logging-practice-in-python" target="_blank">Good logging practice in Python</a>
    <a href="http://docs.python.org/2/library/logging.html" target="_blank">logging</a>
    <a href="http://docs.python.org/2/library/logging.handlers.html" target="_blank">logging.handlers</a>
    <a href="http://docs.python.org/2/library/logging.config.html" target="_blank">logging.config</a>
   《Python 标准库》712P
</pre>
