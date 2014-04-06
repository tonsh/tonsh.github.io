---
layout: post
title: Git Submodule 实践
date: 2013-06－06 18:33:00
categories: git
---


公司的多个项目往往需要引入同一个版本库。这个版本库一旦被修改，就要同步到多个项目中。可以使用 Git Submodule 解决同步问题。下面以一个样例展示如何使用 Git Submodule 实现同步。

#### 准备工作
创建一个项目库 proj1 及一个依赖库 lib。

1. 创建 Git 仓库
 
	```
	cd /home/demo/repo
	git --git-dir=proj1.git init --bare
	git --git-dir=lib.git init --bare
	```
1. 克隆项目

	```
	cd /home/demo
	git clone repo/lib.git lib
	cd lib
	echo 'Hello Lib!' >> readme
	git -am 'initialize'
	git push origin master  任意提交些内容，保证每个仓库不为空
	
	cd /home/demo
	git clone repo/proj2.git proj1
	cd proj
	echo 'Hello Proj!' >> readme
	git -am 'initialize'
	git push origin master
	```

#### 添加子模块，直接修改 lib 库同步至 proj1
1. 使用 `git submodule add` 添加 lib 为 proj1 的子模块

	```
	cd /home/demo/proj1
	git submodule add /home/demo/repo/lib.git lib  克隆子项目
	git status  此时，项目目录下新增了 .gitmodule 文件，记录了子模块的仓库地址。
	git commit -am 'Added submodule lib'
	git push origin master
	git submodule init	将 submodule 信息注册到 git config
	cat lib/reame
	```
	
1. 修改 lib 库

	```
	cd /home/demo/lib
	git checkout master
	git pull origin master
	echo 'Added something!' >> readme
	git commit -am 'Added something'
	git push origin master
	```
1. 在 proj1 中同步 lib

	```	
	cd /home/demo/proj1
	git pull origin master
	
	# 拉取子模块最新提交
	cd lib/
	git checkout master
	git pull origin master
	cd ..
	
	git diff  引用子模块的commit id 有修改
	cat lib/readme  查看修改内容已更新
	
	＃ 将 lib 的修改添加至项目库
	git commit -am 'Edit submodule lib'
	git push origin master
	```
以上展示如何将 lib 库的修改同步至项目中。如果此时 proj1 的仓库被第二次克隆，则克隆的新代码库并没有对子模块的引用。

#### 克隆含有子模块的代码库，实现proj1, proj2 之间的同步
1. 克隆 proj2
	
	```
	cd /home/demo
	git clone repo/proj.git proj2
	cd /home/demo/proj2
	git config -l  此时并没有对lib子模块的引用，运行以下命令注册子模块并同步
	
	git submodule init
	git submodule update
	cat lib/readme
	```
	
	或者用一行命令代替
	
	```
	git clone --recursive /path/to/repos/foo.git
	```
	
	
1. 修改 proj2 的 lib
	进入 proj2/lib 会提示没有使用分支。这是因为主版本库只引用子模块的 commit id; 在主库中 submodule update 会修改主库引用子模块的 commit id。
	
	```
	cd /home/demo/proj2/lib
	git checkout master    checkout 到目标分支（如 master）
	git pull origin master
	echo 'Added something!' >> readme
	git commit -am 'Added something'
	git push origin master
	```
	
	回到主库, 提示子模块有了新提交, 将修改内容添加至主库。
	
	```
	cd /home/demo/proj2
	git status
	git commit -am 'Edit submodule lib'
	git push origin master
	```
	
1. 回到 proj1, 同步子模块

	```	
	cd /home/demo/proj1
	git pull origin master
	git diff  引用子模块有修改，通过 submodule udpate 进行同步
	git sumodule update
	cat lib/readme  查看修改内容已更新
	```
	
#### 删除 submodule
删除子模块需要进行如下修改：

1. 删除子模块的目录

	```
	cd /home/demo/proj1
	git rm -r --cached lib
	```
	
1. 编辑 .gitmodules 文件，从文件中删除对子模块的引用

	```
	[submodule "lib"]                                                           	    path = lib
		url = /home/demo/repo/lib.git/
	```
1. 从 .git/config 中删除子模块的配置信息

	```
	[submodule "lib"]
  		url = /home/demo/repo/lib.git/
	```
1. 提交修改内容

	```
	git add .gitmodules
	git push origin master
	```
	
<pre class="reference">
参考资料:

    <a href="http://git-scm.com/book/zh/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97" target="_blank">Git 工具 - 子模块</a>
    <a href="http://gitbook.liuhui998.com/5_10.html" target="_blank">Git Community Book 子模块</a>
    <a href="http://www.kafeitu.me/git/2012/03/27/git-submodule.html" target="_blank">Git Submodule 使用完整教程</a>
</pre>

	
