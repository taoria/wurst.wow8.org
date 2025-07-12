---
title: 介绍
sections:
- 概念
- 值与类型
- 语法
- 基础
---

### 概念

WurstScript是一种命令式的、面向对象的、静态类型的、对初学者友好的编程语言。

WurstScript提供了一个舒适的工作流程来生成可读和可维护的代码。 易于使用和无压力的地图开发优先于最终产品的执行速度。 WurstScript易于学习和使用，特别是对于有Jass或任何其他编程语言先前知识的人来说，同时对非程序员也保持了简单和易读性。

虽然我们知道WurstScript不会取代WC3地图制作领域的vJass（原因之一是vJass脚本无法轻松移植），但我们仍然希望它能成为一个非常好的选择，特别是对于那些尝试学习Jass的人。

> 请注意，本手册不是新手入门教程，需要读者具备编程知识。
[点击此处查看入门教程](tutorials.html)

### 值与类型

WurstScript是一种静态类型语言。这意味着变量只能持有相同类型的值。
此外，由于所有类型都在编译时确定，不正确的赋值会抛出错误。

Wurst拥有与Jass相同的5种基本变量类型：**null、boolean、int、real、string**。

在下面的示例中，你可以看到左侧变量的静态类型。如果右侧的赋值与左侧的类型不匹配，则会引发错误。
```wurst
boolean b = true // true or false
int i = 1337 // integer, i.e. value without decimals
real r = 3.14 // floating point value
string s = "wurst is great" // text

// Examples
int i = 0 // OK
int j = "no" // Error
string r = "yes" // OK
string s = i // Error
```

### 语法

WurstScript使用基于缩进的语法来定义代码块。您可以使用空格或制表符进行缩进，但混合使用两者会引发警告。
在下文中，我们使用单词“tab”来指代一个制表符或4个空格字符。

```wurst
// Wurst example. 'ifStatements' need to be indented
if condition
	ifStatements
nextStatements

// Other example using curly brackets (e.g. Java or Zinc)
if condition {
	ifStatements
}
nextStatements
// Other example using keywords (e.g. Jass)
if condition
	ifStatements
endif
nextStatements
```

通常，换行符会结束一个语句，但以下情况除外：

- 当一行以 `(` 或 `[` 结束时。
- 当下一行以 `)`、`]`、`.`、`..` 或 `begin` 开头时。
- 当行尾是以下符号之一时：
    `,`, `+`, `*`, `-`, `div`, `/`, `mod`, `%`, `and`, `or`, `?`, `:`

您可以使用这些规则来分割过长的表达式、长参数列表或链式方法调用：
```wurst
someFunction(param1, param2,
	param3, param4)

someUnit..setX(...)
	..setY(...)
	..setName(...)

new Object()
	..setName("Foo")
	..doSomething()
```

WurstScript力求避免过度冗长的语句和符号，以保持代码的简洁和可读性。

### 基础

以下是Wurst核心功能的简要概述。阅读这些内容后，您就可以立即开始编程了。

关于每个主题的更详细文档，请参阅页面下方的专属章节。

Wurst代码被组织成**包（packages）**。任何代码片段都必须放在包中才能被识别。
包可以**导入（import）**其他包，以访问被导入包中定义的变量、函数和类。
包有一个可选的 `init` 块，它会在地图开始时执行。

让我们来看一个经典的 **Hello World** 示例。

```wurst
package HelloWurst
// to use resources from other packages we have to import them at the top
import Printing

// the init block of each package is called when the map starts
init
	// calling the print function from the Printing package
	print("Hello Wurst!")
```

如果您运行此示例，地图加载后将显示“Hello Wurst!”。

您可能已经注意到，`import Printing` 会引发一个警告，因为它已经被自动导入了。
您可以删除此导入语句，它在这里仅用于演示如何导入包。

有关包的更多信息，请参阅**包（Packages）**章节。

您仍然可以在包之外使用普通的Jass语法/代码——Wurst World Editor将解析Jass（如果启用了JassHelper，则解析vJass），并编译您的Wurst代码。

有关在地图中使用混合Wurst/Jass代码的更多信息，请参阅“在传统地图中使用Wurst”部分。

#### 函数

**函数（function）** 的定义由名称、形式参数列表和返回类型组成。
返回类型在参数列表之后使用 **returns** 关键字声明。
如果函数没有返回值，则省略此部分。

```wurst
// this function returns the maximum of two integers
function max(int a, int b) returns int
	if a > b
		return a
	else
		return b

// this function prints the maximum of two integers
function printMax(int a, int b)
	print(max(a,b).toString())

function foo() // parentheses instead of "takes", "returns nothing" obsolete.
	...

function foo2(unit u) // parameters
	RemoveUnit(u)

function bar(int i) returns int // "returns" [type]
	return i + 4

function blub() returns int // without parameters
	return someArray[5]

function foobar()
	int i // local variable
	i = i + 1 // variable assignment
	int i2 = i // support for locals anywhere inside a function
```

#### 变量

全局变量可以在包内的顶层声明。
局部变量可以在函数内的任何位置声明。
常量可以使用 `constant` 或 `let` 关键字声明。
变量可以使用 `var` 关键字或其类型名称来声明。

```wurst
// declaring a constant - the type is inferred from the initial expression
constant x = 5
let x2 = 25
// The same but with explicit type
constant real x = 5
// declaring a variable - the inferring works the same as 'let', but the value can be changed
var y = 5
// declaring a variable with explicit type
int z = 7
// declaring an array
int array a
// arrays can be initialized as well.
// however this is just a shorthand assignment
int array b = [1, 2, 3]
// but it allows having constant arrays
constant c = ["A", "B", "C"]
// Initialized arrays provide a length attribute which is equal to the initial amount of items in the array
// It is not updated when you modify the array and mostly intended for interation
constant blen = b.length

// inside a function
function getUnitInfo(unit u)
	let p = u.getOwner()
	var name = u.getName()
	print(name)
	var x = u.getX()
	var y = u.getY()
	let sum = x + y
```

掌握了这些基本概念，您应该能够做到在Jass中学到的一切。
我们将在接下来的章节中讨论更深入的主题。