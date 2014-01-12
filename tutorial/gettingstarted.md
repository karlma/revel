---
title: 入门指南
layout: tutorial
---

此文档描述了安装过程。

#### 安装 Go

为了使用Revel，你必须先安装GO [install Go](http://golang.org/doc/install).

#### 设置你的 GOPATH

如果你在安装时没有创建GOPATH目录，现在就创建吧。
你的GOPATH是你的所有GO代码应该呆在的地方。下面就是步骤：

1. 创建目录： `mkdir ~/gocode`
2. 告诉Go用这个目录做为GOPATH： `export GOPATH=~/gocode`
3. 保存你的GOPATH，为了将来可以在shell里使用: `echo GOPATH=$GOPATH >> .bash_profile`

现在你的Go已经安装完啦。

#### 安装 git 和 hg

在用 `go get` clone 各种依赖资源的时候，Git 和 Mercurial 都可能用到。

* [安装 Git](http://git-scm.com/book/en/Getting-Started-Installing-Git)
* [安装 Mercurial](http://mercurial.selenic.com/wiki/Download)

#### 获取 Revel 框架

运行下面的命令获取 Revel 框架：

	go get github.com/robfig/revel

这个命令做两件事：

* Go用 git clone 资源库到 `$GOPATH/src/github.com/robfig/revel/`
* Go传递的查找所有的依赖关系，运行 `go get` 获取所有的依赖库。

#### 创建 Revel 命令行工具

Revel 命令行工具是你用来创建，运行，打包 Revel 应用时用的。

用 `go get` 安装它：

	go get github.com/robfig/revel/revel

接下来，确保 $GOPATH/bin 目录在你的PATH环境变量里，这样你就可以在任何地方直接Revel命令啦。

	export PATH="$PATH:$GOPATH/bin"
	echo 'PATH="$PATH:$GOPATH/bin"' >> .bash_profile

最后，让我们验证一下它是否可能工作啦：

	$ revel help
	~
	~ revel! http://robfig.github.com/revel
	~
	usage: revel command [arguments]

	The commands are:

	    run         run a Revel application
	    new         create a skeleton Revel application
	    clean       clean a Revel application's temp files
	    package     package a Revel application (e.g. for deployment)

	Use "revel help [command]" for more information.

现在，我们都一切都设置好啦。

**Next: [创建新的 Revel 应用.](createapp.html)**
