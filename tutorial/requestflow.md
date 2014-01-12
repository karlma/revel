---
title: 请求流程
layout: tutorial
---

在 [前文](createapp.html) 中，我们创建了一个新的 Revel 应用，叫做  **myapp**.
在此文中，我们要看一下Revel是如何处理HTTP http://localhost:9000 请求，返回欢迎消息的。

### 路由

Revel做的第一件事就是检查 **conf/routes** 文件 -- 产生的路由文件有一个路由：

	GET     /                                       App.Index

这个路由告诉Revel，当它收到 **/** 上的 **Get** 请求时，调用 **App** 控制器的 **Index** 方法。

### 动作（Actions）

让我们跟着这个调用到代码里看一下， **app/controllers/app.go**:

	package controllers

	import "github.com/robfig/revel"

	type App struct {
		*revel.Controller
	}

	func (c App) Index() revel.Result {
		return c.Render()
	}

所有的控制器都必须是在第一个slot嵌入　`*revel.Controller` 的结构体。
在控制器的所有返回　`reve.Result` 的exported的方法，都会被认为是一个Action.

Revel 控制器为产生Result提供了许多有用的方法。
在这个例子中的 [`Render()`](../docs/godoc/mvc.html#Controller.Render) 就是其中之一,
它告诉Revel去找一个模板并以（**200 OK**)响应渲染它。

###  模板

所有的模块都在 **app/views** 目录里. 当没有明确指定模板名的时候，Revel会找一个与action名字匹配的模块
在这个例子中，Revel会找到 **app/views/App/Index.html** 文件, 并用
 [Go template](http://www.golang.org/pkg/html/template)来渲染它。

{% raw %}

	{{set . "title" "Home"}}
	{{template "header.html" .}}

	<header class="hero-unit" style="background-color:#A9F16C">
	  <div class="container">
	    <div class="row">
	      <div class="hero-text">
	        <h1>It works!</h1>
	        <p></p>
	      </div>
	    </div>
	  </div>
	</header>

	<div class="container">
	  <div class="row">
	    <div class="span6">
	      {{template "flash.html" .}}
	    </div>
	  </div>
	</div>

	{{template "footer.html" .}}

{% endraw %}

除了Go模板提供的功能外，Revel还增加了
[一些有用的](../manual/templates.html)它自己的模板功能。

这个模板非常简单：

1. 在render上下文中增加一个新的 **title** 变量。
2. 包含 **header.html** 模拟 (它会用到 title)。
3. 显示 welcome 消息。
4. 包含 **flash.html** 模板, 它会显示任何的flash消息。
5. 包含 **footer.html**。

如果你看一下 header.html, 你就能看到更多的action里的模板标记：

{% raw %}

	<!DOCTYPE html>
	<html>
	  <head>
	    <title>{{.title}}</title>
	    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
	    <link rel="stylesheet" type="text/css" media="screen" href="/public/stylesheets/main.css">
	    <link rel="shortcut icon" type="image/png" href="/public/images/favicon.png">
	    <script src="/public/javascripts/jquery-1.5.2.min.js" type="text/javascript" charset="utf-8"></script>
	    {{range .moreStyles}}
	      <link rel="stylesheet" type="text/css" href="/public/{{.}}">
	    {{end}}
	    {{range .moreScripts}}
	      <script src="/public/{{.}}" type="text/javascript" charset="utf-8"></script>
	    {{end}}
	  </head>
	  <body>

{% endraw %}

你能看到设置了title, 你还看到它通过 **moreStyles** 和 **moreScripts** 变量里引入进来的一些JS和CSS文件。

### 热加载

让我们修改一下welcome消息。  在 **Index.html**　文件里, 修改

	<h1>It works!</h1>

成

	<h1>Hello World</h1>

刷新你的浏览器, 你就会立即见到修改的结果!
Revel 注意到了你的模板修改过，并且会重新加载它。

Revel 监视:

* 所有在 **app/** 目录下的go代码
* 所有有 **app/views/** 目录下的模板
* 你的路由文件： **conf/routes**

上面这些东西的任何一个有所改变都会引起 Revel 用新的代码更新你的正在运行的app。
现在，让我们试一下： 打开 **app/controllers/app.go** 文件，搞出一点错误。

修改

	return c.Render()

成

	return c.Renderx()

刷新页面， Revel 会显示一个有帮助的错误信息页面：

![A helpful error message](../img/helpfulerror.png)

最后，让我们将一些数据传递给模板。

在 **app/controllers/app.go** 文件里, 修改:

	return c.Renderx()

成:

	greeting := "Aloha World"
	return c.Render(greeting)

在 **app/views/App/Index.html** 文件里，修改:

{% raw %}

	<h1>Hello World</h1>

{% endraw %}

成:

{% raw %}

	<h1>{{.greeting}}</h1>

{% endraw %}

刷新页面，你将会看到 Hawaiian greeting.

![A Hawaiian greeting](../img/AlohaWorld.png)

**Next: [创建一个简单的 Hello World 应用](firstapp.html).**
