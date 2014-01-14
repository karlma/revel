---
title: 概念
layout: manual
---

Revel 是一个带电池的（batteries-included）（译注：应用是比较完备的意思）web框架，它学习了Rails和Play!框架的精神。
很多相同的理念都被吸收进了这个框架的设计和接口中。

使用Revel，你可以很容易的创建web应用。
这是因为它采用“模型－视图－控制器”（MVC）模式，并且依赖于web应用程序中结构设计的惯例。
从而，使得它在具有非常简单的配置的同时，大大的加速了开发周期。

## MVC

下面是一个简单的总结：

- *Models* 是描述你的应用域实体数据对象。模型也包含特定域的查询和更新数据的逻辑和操作。
- *Views* 描述如何展示和操纵数据。在我们这里，它就是一个向用户展示数据和让用户控制数据的模板。
- *Controllers* 处理请求的执行。它们执行用户希望的动作，决定使用哪个View来完成显示，同时它们还为渲染View准备和提供必要的数据。

网上有许多优秀的关于MVC架构的文章。 尤其是 [Play! Framework](http://www.playframework.org)，它就是我们使用的模型。

## 一个请求的生命周期

下面是请求处理框架的概览。

![Life of a Request](../img/RevelDesign.png)

概念总结:

* Revel公开一个 http.Handler, 它负责实例化一个与请求上下文相关的控制器，同时将请求传送到过滤器链（Filter Chain）。
* 过滤器们被串在一个请求处理链上。它们是由一些像请求日志，cookie策略，认证等的实现层组成的。
  大部分内置的功能都是用过滤器实现的。
* 动作(Actions)就是一些特定的应用功能，它们处理输入，并输出结果。

## HTTP 处理器

Revel建构在Go HTTP server之上, 它会针对每一个进入的请求创建一个 go-routine
(轻量级线程)。这样的实现可以让你的代码随意的阻塞，但是它必须处理并发的请求处理。

Revel处理器，除了把request扔到过滤器链中去处理，处理完成后，把结果写回到response中之外，它什么也不做。

Revel处理器默认被注册到 `"/"`，接收所有到达的连接。
然而，应用程序可以自由的覆盖些行为。例如，它们可能会使用其它现成的http.Handler，甚至用Revel框架重新实现一个也不是不可以。
详细情况请看 [FAQ](faq.html) 。

## 过滤器

[过滤器](filters.html) 实现了大部分的Revel提供的请求处理功能。
它们有一个简单的interface，允许被嵌入。

"过滤器链" 就是一个函数数组, 每一个函数会调用下一个函数, 直到最后一个，它会调用action。
举个例子，链中的第一个过滤器是 `RouterFilter`, 它来决定这个请求应该用哪个Action来处理，并将其保存在控制器中。

总的说来，过滤器和过滤器链就相当于Rack。

## 控制器和动作

每一个 HTTP 请求都会调用一个 **action**, 它来处理请求将写回应答。
相关的 **actions** 被组织到 **controllers**里。
[Controller](../docs/godoc/controller.html#Controller) 类型包含相关
字段和方法，做为每一个请求的上下文。

做为HTTP请求处理的一部分，Revel实例化你的Controller实例，并且设置嵌入到你的Controller中的`revel.Controller`的所有属性。
Revel不会在不同的请求之间共享Controller。

***

一个 **Controller** 就是直接或间接嵌入了 `*revel.Controller`类型的任何一个类型。
{% raw %}
<pre class="prettyprint lang-go">
type AppController struct {
  *revel.Controller
}
</pre>
{% endraw %}

一个 **Action** 就是 **Controller** 中符合下述标准的任何一个方法：
* 是一个导出方法
* 返回revel.Result类型

例如：
{% raw %}
<pre class="prettyprint lang-go">
func (c AppController) ShowLogin(username string) revel.Result {
	..
	return c.Render(username)
}
</pre>
{% endraw %}

这个例子调用了 `revel.Controller.Render` 去执行一个模板, 同时将username参数传给它。
**revel.Controller**中有很多方法可以产生 **revel.Result**,
但是应用程序也可以自己创建。

## 结果(Results)

一个 Result 是任何一个符合下面接口的东西：
{% raw %}
<pre class="prettyprint lang-go">
type Result interface {
	Apply(req *Request, resp *Response)
}
</pre>
{% endraw %}
通常，**action**和所有的过滤器都已经返回后，才会写应答。
此时，Revel先写应答的头和cookie（如，设置session cookie）,接着，它调用 `Result.Apply`来写应答的实质性内容。
