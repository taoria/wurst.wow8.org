---
title: 介绍
sections:
- 序言
- 引言
- 匿名函数
---


![](/assets/images/tutorials/closure.jpg)
{: .img-responsive}

## 序言

本教程面向中等水平、熟悉编程基础并希望进一步扩展其知识与能力的开发者。

## 引言

在开发地图的过程中，你可能会遇到某些不想受限于 `code` 类型的场景，例如需要提供自定义的事件监听器、延迟执行代码、在选取单位时使用匹配函数，或者仅仅是想把函数像对象一样存储在变量中。

在 Jass 中，虽然可以将函数（`code` 类型）存储在变量里，但终究无法对它们进行大规模操作。究其原因，是 Jass 不允许 `code array`，并且你也无法将 `code` 放入哈希表。

Jass 中确实存在一些调用和存储函数的变通方案，但这通常需要开发者运用一些技巧，比如依赖 `boolexpr` 和 `trigger`，或者使用不安全的 `ExecuteFunc(string name)` 来模拟函数变量。

而闭包解决了所有这些缺陷。

## 匿名函数

但在我们接触闭包之前，为了防止概念混淆，我们先来厘清一些术语。

Lambda 表达式允许你定义匿名函数，闭包则与 Lambda 表达式结合使用。

那么，什么是匿名函数呢？很简单，没有名称引用的函数就是匿名函数。请看下面的例子：

```wurst
function foo()
    print("Hello")

init
    CreateTrigger().addAction(function foo)
```
在这里，我们给 `addAction` 传递了一个函数名作为引用。因此，这个作为参数的函数是有名字的，也就是说，它是一个非匿名函数。

现在，让我们用 Lambda 表达式来实现一个匿名函数的版本。

```wurst
init
    CreateTrigger().addAction(() -> print("Hello"))
```


新的部分是 `() -> print("Hello")`。
如你所见，这其实就是普通函数定义中跟在函数名后面的部分：

```wurst
/*function foo*/()
    print("Hello")
```
通过添加箭头 `->` 来分隔参数列表和函数体。
这同样适用于带参数的函数。

```wurst
function foo(int i1, int i2)
    print("Hello " + i1.toString + i2.toString())

// 可以写成
(i1, i2) -> print("Hello " + i1.toString + i2.toString())
```
然而，这种方式不能用于像 `addAction` 这样的函数，因为在 Jass 中，你无法调用（invoke）一个带参数的 `code` 类型函数。你将不得不使用 `GetXXX` 这样的全局函数来获取事件数据。

为此，我们需要闭包。