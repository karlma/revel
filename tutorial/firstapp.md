---
title: "Hello World" 应用
layout: tutorial
---

此文做一个快速的练习，实现一个在如同
[the Play! example](http://www.playframework.org/documentation/1.2.4/firstapp).
一样的 "Hello World" 应用

让我们从我们 [前面创建的](createapp.html) 的那个 **myapp** 项目开始吧。

编辑 **app/views/App/Index.html** 模板，在包含 `flash.html` 处的正文，增加这个表单：

	<form action="/App/Hello" method="GET">
	    <input type="text" name="myName" /><br/>
	    <input type="submit" value="Say hello!" />
	</form>

刷新页面看一下我们的成果。

![The Say Hello form](../img/AlohaForm.png)

我们来提交一下。

![Route not found](../img/HelloRouteNotFound.png)

可以理解。 在 **app/controllers/app.go** 里增加一个 action:

	func (c App) Hello(myName string) revel.Result {
		return c.Render(myName)
	}


然后，我们还得创建一个view。创建文件 **app/views/App/Hello.html**, 其内容如下：

{% raw %}
	{{set . "title" "Home"}}
	{{template "header.html" .}}

	<h1>Hello {{.myName}}</h1>
	<a href="/">Back to form</a>

	{{template "footer.html" .}}
{% endraw %}

刷新页面，你应该可以看到：

![Hello Robfig](../img/HelloRobfig.png)

最后，我们再来增加一些验证。名字是必须要有的，而且不能少于3个字符。

为了达到目标，我们要用到 [验证模块](../manual/validation.html)。
编辑你的action，在 **app/controllers/app.go**文件里:

	func (c App) Hello(myName string) revel.Result {
		c.Validation.Required(myName).Message("Your name is required!")
		c.Validation.MinSize(myName, 3).Message("Your name is not long enough!")

		if c.Validation.HasErrors() {
			c.Validation.Keep()
			c.FlashParams()
			return c.Redirect(App.Index)
		}

		return c.Render(myName)
	}

现在，如果没有输入有效的名字的话，它会将用户返回到 `Index()`。
名字和验证错误保存在[Flash](../manual/sessionflash.html)里，它是一个临时cookie。

在 `flash.html` 模板中将会显示所有的错误和flash消息：

{% raw %}
	{{if .flash.success}}
	<div class="alert alert-success">
		{{.flash.success}}
	</div>
	{{end}}

	{{if or .errors .flash.error}}
	<div class="alert alert-error">
		{{if .flash.error}}
			{{.flash.error}}
		{{end}}
		{{if .errors}}
		<ul style="margin-top:10px;">
			{{range .errors}}
				<li>{{.}}</li>
			{{end}}
		</ul>
		{{end}}
	</div>
	{{end}}
{% endraw %}

现在，当我们提交一个字母的名字时，会看到如下的页面：

![Example error](../img/HelloNameNotLongEnough.png)

成功啦，我们得到了一个恰当的错误提示，我们的输入也会保存下来，等我们修改。
