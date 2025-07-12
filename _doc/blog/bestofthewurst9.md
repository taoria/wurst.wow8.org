---
title: Wurst 精选 9 - 节日快乐
layout: post
excerpt: 我们来看看堆栈跟踪
icon:
  type: fa
  name: fa-servicestack
color: blue
author: Frotty & Coke
date: 2018-12-27
banner: /assets/images/blog/bestof9/wurstbanner9.png
---
------


![](/assets/images/blog/bestof9/wurstbanner9.png)
{: .img-responsive}

在过去的几个月里，我们在 GitHub 上获得了超过 100 颗星，更新了我们的[主页](https://wurstlang.org)，并一如既往地跟进了对魔兽争霸 3 补丁 `1.30.2` 及更高版本的支持。


工具更新
---
* Wurst 系列工具现已支持最新的战网稳定补丁 (1.30.2)。
* Wurst 语言已更新，允许 `class` 包含元组数组成员。
* 不当使用泛型现在会抛出正确的编译错误。
* 尝试访问已知已被销毁的对象现在会在编译时失败。
* 修复了局部优化中一个导致意外行为的错误。
* Wurst 不再需要 JavaFX，这使得 Wurst 可以在更广泛的 Java 发行版中进行构建。

标准库亮点
---
* 合并了十几个很棒的拉取请求。
* 在 `InstantDummyCaster` 的基础上，为持续施法类法术和有延迟效果的法术添加了 `DummyCaster`。
* 修复了 `HashList`、`HashSet` 和 `Knockback3D` 中的一些严重错误。
* 我们新增了[代码规范](https://wurstlang.org/manual.html#coding-conventions) - 快去统一你的格式吧！
* 我们已经开始编写深入的[包文档](https://wurstlang.org/stdlib)

焦点：堆栈跟踪
---

你知道吗？WurstScript 会为来自 `ErrorHandling` 包的 `error(string msg)` 调用生成堆栈跟踪。
你可能之前在 Wurst 错误中见过这个——它会显示在错误发生前，你的代码中哪些行以何种顺序被执行了。

看看这个例子：

```wurst
package Test
import ErrorHandling

function foo()
	new A().bar()

class A

	function bar()
		error("This is an error message")

init
	foo()
```

它在游戏中会产生以下堆栈跟踪：


![](/assets/images/blog/bestof9/stacktrace.png)
{: .img-responsive}

如你所见，最近的调用行在最上面，最旧的在最下面。堆栈跟踪极大地帮助了错误调试，因为它为你提供了错误发生前的历史记录。当然，它们会造成相当大的性能开销。如果你已经验证了地图运行良好，可以通过相应的运行参数 `-stacktraces` 来禁用它们，该参数默认是启用的。