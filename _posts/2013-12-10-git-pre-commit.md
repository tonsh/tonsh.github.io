---
layout: post
title: 使用 Git hook 实现自动代码检查
date:   2013-12-10 19:50:33
categories: Git
---

我们经常为一个百思不得其解的问题调试好久之后才发现原来是变量名拼写错了，原来是括号没配对儿；团队内部经常强调代码规范，稍有不慎就会写出与团队风格不统一的代码。我们可以使用一些语法检查，代码规范的工具或 IDE 来避免这些事情的发生，每次发布程序之前进行代码检查是个好习惯，但对于开发人员来说需要随时记得去做这些事情，稍不注意就会因为一个拼写错误被小伙伴们嘲笑起来，这些事情能不能自动实现呢？如果你们的版本管理工具使用的是 Git，那么接下来要讲的 Git hooks 一定能帮到你。

Git 的 hooks 脚本放在项目目录的 .git/hooks 下, .sample 文件只是样例脚本，如果需要使用提交前的 hook 需要将 pre-commit.sample 文件重命名为 pre-commit, 这些脚本可以使用 shell, python, ruby 等任何可以调用 shell 命令的语言实现。

以下为使用 pyflakes, pep8 检查 Python 语法, 并自动进行单元测试的脚本样例(仅做参考):
{% highlight python linenos %}
#! /usr/bin/env python
# coding=utf-8

import re
import os
import sys
import subprocess


def syntax_checker():
    """
        pyflakes 与 pep8 进行语法与代码风格检查。
        有严重语法错误时提交会被直接拒绝；
        如果有风格问题，会询问提交人员是否继续进行提交
    """
    errors = ''
    warning = ''
    pep8_info = ''

    # 获取有改动的文档
    staged_cmd = 'git diff --staged --name-only HEAD'
    proc = subprocess.Popen(staged_cmd, shell=True, stdout=subprocess.PIPE)
    with proc.stdout as std_out:
        for staged_file in std_out.readlines():
            staged_file = staged_file.strip()

            if not (staged_file.endswith('.py') or os.path.exists(staged_file)):
                continue

            stdout, stderr = process('pyflakes %s' % staged_file)
            warning += stdout
            errors += stderr

            stdout, _ = process('pep8 %s' % staged_file)
            pep8_info += stdout

    failed = False
    if is_error(errors) or is_warning(warning) or is_warning(pep8_info):
        failed = True
    return failed


def process(cmd):
    """ 执行命令, 返回标准输出与错误信息 """

    proc = subprocess.Popen(cmd, shell=True,
                            stdout=subprocess.PIPE,
                            stderr=subprocess.PIPE)
    return proc.communicate()


def is_error(errors):
    """ 严重错误，如语法错误等 """
    failed = False

    if len(errors) > 0:
        failed = True

        print "[failed] You cann't commit, repair the errors or run `pyflakes file.py` view details:"
        print "------------------------------"
        print errors

    return failed


def is_warning(warning):
    """ 代码规范建议信息 """
    failed = False

    if len(warning) > 0:
        print warning
        print "Encounter some non-standard syntax! Do you want to correct them? y(es) or n(o):"
        while True:
            input_row = sys.stdin.readline().strip().lower()

            if input_row in ['y', 'yes']:
                failed = True
                break
            elif input_row in ['n', 'no']:
                failed = False
                break
            print 'Input y(es) or n(o):'

    return failed

print '=================== syntax check start ======================='
failed = syntax_checker()
print '=================== syntax check end ========================='
sys.exit(1 if failed else 0)
{% endhighlight %}

只要将该脚本移至您项目中的 .git/hooks/pre-commit, 记得修改该文件为可执行权限。每次 git commit 之前就会自动进行语法检查了, 如果有语法错误的提交，git commit 将被拒绝。如果有代码规范问题会询问对方是否停止提交进行修改。

如果在提交时不想进行 pre-commit, 只需要添加 --no-verify 参数即可。

```
git commit --no-verify -m '提交信息'
```

<pre class="reference">
参考:
    《Pro Git》
    <a href="http://fitzgeraldnick.com/weblog/9" target="_blank">Git Hooks and Pylint</a>
</pre>
