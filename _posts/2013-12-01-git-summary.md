---
layout: post
title: Git 实践
date:   2013-12-01 23:50:33
categories: Git
---

一直想写一篇于 Git 相关的文章，想记录一下平时在实际项目中遇到或没遇到的问题。一下内容是自己总结，有些没写清楚或写错的地方，望指正！

#### Git 工作流

在熟悉 Git 基本用法之前，我们需要了解 Git 的工作流程是怎样的！项目文件的不同状态有什么含义，了解了这些，我们的工作才能开展下去.Git 有本地仓库与远程仓库。我们开发是都是在各自的本地仓库完成。

新建版本库
git init    从当前目录创建项目仓库
git clone remote_git 从远程仓库克隆项目

查看状态
git status  查看文件状态

添加或更新文件
git add filename 将添加或修改的文件至缓存区, 标记为跟踪状态
git commit -m '提交信息' 提交缓存区的内容(HEAD)
git commit -a -m '提交信息' 等价于 git add filename; git commit; 这样可以跳过暂存区, 但我不太建议这样写，除非你非常确定你需要提交那些内容。

撤销操作
git checkout -- [file] 从工作目录取消修改
git reset HEAD -- [file] 从暂存区取消修改
git commit -amend 修改最后一次提交；有时候提交完发现漏掉了几个文件没有加或者提交信息写错了，可以使用这个命令进行修改。

```
git commit -m '我以为我提交完了'
git add forget_me.py
git commit -amend
```

内容比较
git diff filename 查看当前文件(未暂存)与暂存区的差异
git diff --cached 查看暂存区与上次提交(HEAD)的差异

删除移动文件
git rm filename 删除文件
git mv file_from file_to 移动文件

查看历史
git log 查看提交历史记录
git log -p 查看提交历史记录并显示修改内容
git log [-p] -number 查看前 number 条历史记录
git log [-p] --stat 查看历史记录并显示增改统计
git log --pretty=oneline  每个提交显示一行，方便快速浏览提交历史
git log --graph --pretty=oneline 方便查看分支提交图形

分支管理
git brand brand_name 新建brand_name 分支
git checkout brand_name 切换至 brand_name 分支
git branch -l 查看本地分支列表
git branch -r 查看远程分支
git branch -a 查看远程与本地分支
git branch -d brand_name 删除 brand_name 分支，并将brand_name 分支合并至当前分支
git branch -D brand_name 删除 brand_name 分支，但不合并

远程仓库
git remote -v 显示所有的远程仓库
git remote add [shortname][url] 添加远程仓库
git push ［remote-name][branch-name] 将本地仓库(HEAD)中的数据提交至远程版本库。该命令需要有远程仓库服务器的写权限，如果有权限限制的版本库可以参考 Git 的 forking 使用。
git pull 从远程版本库更新最新代码
git remote rename [from-short-name] [to-short-name] 重命名远程仓库的简称。
git remote rm [short-name] 删除远程仓库

```
git remote add location url
git remote -v
git remote rename location local
git remote rm local
git remote -v
```

.gitignore 忽略某些文件

未完待续。。。

<pre class="reference">
参考资料:
    《Pro Git》
    <a href="http://rogerdudler.github.io/git-guide/index.zh.html" target="_blank">《git - 简易指南》</a>
    <a href="http://oli.jp/2012/git-powerup/" target="_blank">《Git config powerup with aliases, diff & log》</a>
    <a href="https://github.com/fengzimaster/systemConfig/blob/master/gitTest.md" target="_blank">Git test commond</a>
</pre>
