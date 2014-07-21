---
layout: post
title: Git 基础命令汇总
date:   2013-12-02 11:50:33
categories: Git
---

这篇文章只是记录了一些平时在项目中经常使用的 Git 基础命令。之后会陆续写几篇关于 Git 的工作原理及高级用法。

### 新建版本库
* git init    从当前目录创建项目仓库
* git clone \<url> 从远程仓库克隆项目

### 查看状态
* git status  查看文件状态

### 添加或更新文件
* git add \<file-name> 将添加或修改的文件添加至缓存区
* git commit -m '提交信息' 提交缓存区的内容至HEAD
* git commit -a 等价于 git add filename; git commit; 这样可以跳过暂存区, 但不太建议这样写，除非你非常确定需要提交哪些些内容。

### 撤销操作
* git checkout -- \<file-name> 从工作目录取消修改
* git reset HEAD -- \<file-name> 从暂存区取消修改
* git rebset HEAD^ --hard 回滚到 HEAD 的前一次提交，不保存修改
* git commit -amend 修改最后一次提交；有时候提交完发现漏掉了几个文件没有加或者提交信息写错了，可以使用这个命令进行修改。

```
git commit -m '提交信息'
git add forget_me.py
git commit -amend
```

* 修改历史提交

```
git rebase -i HEAD~3  --> 要修改当前版本的倒数第三次提交内容

pick: xxxxxxxx
pick: xxxxxxxx
pick: xxxxxxxx

选择要修改的提交，将对应的 “pick” 改为 “edit” 保存退出；这时 git 显示已经在历史版本分支了。
提交修改的内容。

git commit --amend

git rebase --continue  --> 修改完毕，回到最新版本
```

### 内容比较
* git diff \<file-name> 查看工作目录的文件与暂存区的差异
* git diff --cached 查看暂存区与上次提交(HEAD)的差异

### 删除移动文件
* git rm \<file-name> 删除文件
* git mv \<file-from> \<file-to> 移动文件

### 查看历史
* git log 查看提交历史记录
* git log -p 查看提交历史记录并显示修改内容
* git log [-p] -\<number> 查看前 number 条历史记录
* git log [-p] --stat 查看历史记录并显示增改统计
* git log --pretty=oneline  每个提交显示一行，方便快速浏览提交历史
* git log --graph --pretty=oneline 方便查看分支提交图形

### 远程仓库
* git remote -v 显示所有的远程仓库
* git  remote show \<remote-name> 显示远程仓库信息

```
>>> git remote show origin
* remote origin
  Fetch URL: git@github.com:tonsh/tonsh.github.io.git
  Push  URL: git@github.com:tonsh/tonsh.github.io.git
  HEAD branch: master   --> 当前所在分支
  Remote branches:      --> 目前存在的远程分支
    demo    tracked
    develop tracked
    master  tracked
  Local branch configured for 'git pull':   --> 运行 git pull 时自动合并的分支
    master merges with remote master
  New remote branches (next fetch will store in remotes/origin) --> 还没有同步到本地的远程分支
    demo_3
  Stale tracking branches (use 'git remote prune')  --> 已同步到本地的远端分支在远端服务器上已被删除
    demo_2
```
* git remote add \<remote-name> \<url> 添加远程仓库
* git push \<remote-name> \<branch-name> 将 HEAD 中的数据提交至远程版本库。该命令需要有远程仓库服务器的写权限，如果有权限限制的版本库可以参考 GitHub 的 forking 使用。
* git pull 从远程版本库更新最新代码
* git remote rename \<from-remote-name> \<to-remote-name> 重命名远程仓库的简称。
* git remote rm \<remote-name> 删除远程仓库

```
git remote add location url
git remote -v
git remote rename location local
git remote rm local
git remote -v
```

### 分支
* git brand \<brand-name> 新建brand-name 分支
* git checkout \<brand-name> 切换至 brand-name 分支
* git branch -l 查看本地分支列表
* git branch -r 查看远程分支
* git branch -a 查看远程与本地分支
* git branch -d \<brand-name> 删除 brand-name 分支，并将brand-name 分支合并至当前分支
* git branch -D \<brand-name> 删除 brand-name 分支，但不合并
* git push \<remote-name> :\<branch-name> 删除远程分支
* git remote prune origin  本地与远程分支同步
* git merge \<brand-name> 合并分支
* 

#### 标签
* 查看最近两条tag列表

    ```
    git describe --tag `git rev-list --tags --max-count=2` 
    ```
* git show-ref --tags 显示所有tags 及 对应的提交版本号（SHA1)
* git show-ref $TAG 输出一个 $TAG 的 SHA1
* 添加标签

    ```
    git tag v1.0.19 master   # git tag $TAG $BRANCH
    git push origin v1.0.19 
    ```
* 删除标签

  ```
  git tag -d v1.0.19
  git push origin :refs/tags/v1.0.19
  ```

### 配置
* git config [--global] user.name \<Firstname Lastname> 配置用户信息
* git config [--global] user.email \<your-email>
* git config [--global] core.editor emacs 设置文本编辑器为 emacs, 默认为 vim
* git config autocrlf true  保证 Mac 和 Linux 系统不会引入 CRLF 换行符（会把 CRLF 换行符替换为 LF）

<pre class="reference">
参考资料:
    《Pro Git》
    <a href="http://rogerdudler.github.io/git-guide/index.zh.html" target="_blank">《git - 简易指南》</a>
    <a href="http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000" target="_blank">《Git教程》</a>
    <a href="http://oli.jp/2012/git-powerup/" target="_blank">《Git config powerup with aliases, diff & log》</a>
    <a href="https://github.com/fengzimaster/systemConfig/blob/master/gitTest.md" target="_blank">《Git test commond》</a>
    <a href="http://www.worldhello.net/gotgithub/index.html" target="_blank">《GotGithub》</a>
</pre>
