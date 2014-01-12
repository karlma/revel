---
title: 创建新的 Revel 应用
layout: tutorial
---

在你的GOPATH目录下，用 Revel 命令行工具创建一个空项目，然后运行它：

	$ cd $GOPATH

	$ revel new myapp
	~
	~ revel! http://robfig.github.com/revel
	~
    Your application is ready:
        /Users/robfig/code/gocode/src/myapp

    You can run it with:
        revel run myapp

	$ revel run myapp
	~
	~ revel! http://robfig.github.com/revel
	~
	2012/09/27 17:01:54 run.go:41: Running myapp (myapp) in dev mode
	2012/09/27 17:01:54 harness.go:112: Listening on :9000

在你的浏览器中打开 http://localhost:9000/ ，你会看到，你的app已经准备好啦。

![Your Application Is Ready](../img/YourApplicationIsReady.png)

产生的项目结构在[这里](../manual/organization.html).

**Next: [学习 Revel 如何处理请求.](requestflow.html)**
