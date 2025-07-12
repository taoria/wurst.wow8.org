---
title: 基本概念
sections:
- 表达式
- 级联操作符
- 语句
- 包
---

## 表达式

Wurst 表达式的一些基本语法：
```wurst
Expr ::=
      Expr + Expr       // 加法
    | Expr - Expr       // 减法
    | Expr / Expr       // 实数除法
    | Expr div Expr     // 整数除法
    | Expr % Expr       // 实数取余（取模）
    | Expr mod Expr     // 整数取余（取模）
    | Expr and Expr     // 布尔与
    | Expr or Expr      // 布尔或
    | Expr ? Expr : Expr // 条件表达式 / 三元操作符
    | Expr < Expr       // 小于
    | Expr <= Expr      // 小于等于
    | Expr > Expr       // 大于
    | Expr >= Expr      // 大于等于
    | Expr == Expr      // 等于
    | Expr != Expr      // 不等于
    | - Expr            // 一元负号
    | not Expr          // 逻辑非
    | IDENTIFIER                    // 变量
    | IDENTIFIER(Expr, Expr, ...)   // 函数调用
    | Expr . IDENTIFIER             // 成员变量
    | Expr . IDENTIFIER(Expr, Expr, ...)    // 成员函数
    | Expr .. IDENTIFIER(Expr, Expr, ...)   // 成员函数，与上面类似，但返回当前对象（见下文）

    | new IDENTIFIER(Expr, Expr, ...)       // 构造函数调用
    | destroy Expr                  // 析构函数
    | Expr castTo Type              // 类型转换
    | Expr instanceof Type          // 实例类型检查
    | begin                         // 多行 lambda 表达式
        Statements
        end // 多行 lambda 表达式
    | (param1, param2, ...) -> Expr  // 匿名函数
    | (Expr)                        // 括号
```


`_IDENTIFIER_` 是一个函数或变量的名称。它可以由字母、数字和下划线组成。
（译者注：以单个下划线开头或结尾的函数名可能会导致编译成功但运行时出错的情况）

**提示**：关于泛型函数的调用，请查看泛型章节。


### 级联操作符 (点点操作符)

级联操作符 `..` 类似于普通的 `.`，可以用来调用函数，但它不返回函数的结果，而是返回调用者对象。这使得对同一个对象进行链式调用变得容易。
请看以下示例：
```wurst
CreateTrigger()
    ..registerAnyUnitEvent(EVENT_PLAYER_UNIT_ISSUED_ORDER)
    ..addCondition(Condition(function cond))
    ..addAction(function action)
```
以上代码等效于：
```wurst
let temp = CreateTrigger()
temp.registerAnyUnitEvent(EVENT_PLAYER_UNIT_ISSUED_ORDER)
temp.addCondition(Condition(function cond))
temp.addAction(function action)
```

### 条件操作符

条件操作符（也称为三元表达式）的格式为 `条件 ? A : B`。
首先，条件会被求值。
如果条件为 `true`，则表达式 `A` 会被求值，其结果将作为整个表达式的结果。
否则，表达式 `B` 会被求值，其结果将作为整个表达式的结果。

该表达式结果的类型是两个子表达式结果的共同超类型。也就是说，表达式 `A` 和 `B` 应该具有兼容的类型才能在同一个三元表达式中使用。

条件表达式通常不常用。
一般用于在两个备选结果中进行选择，如下所示：

```wurst
// 处理单数和复数形式的 "enemy"
let enemyCount = n.toString() + " " + (n == 1 ? "enemy" : "enemies")
```

## 语句

Wurst 为 Jass 添加了一些现代化的语句，例如 `switch` 和 `for` 循环。

### If语句
`if` 语句用于在满足特定条件时执行某些操作。
在 **else** 块中，你可以处理条件不满足的情况。
```wurst
// 简单示例
if u.getHP() < 100
    print("unit s hurt") // if 块内的操作必须缩进

// if 和 else
if x > y
    print("x 大于 y")
else if x < y // 关闭 if 块，开始 else-if 块
    print("x 小于 y")
else // 处理其余情况
    print("x 等于 y")

// 通过缩进编写 if 语句

// 更多示例：

if caster.getOwner() == BOT_PLAYER
    print("caster owned by BOT_PLAYER")

if GetSpellAbilityId() == SPELL_ID
    flashEffect(getSpellTargetPos(), FX_PATH)

if x > 0 and x < 100 and y > 0 and y < 100
    print("inside box")
```
### Switch 语句
```wurst
// i 是一个整型变量
switch i
    case 1
        print("1")
    case 3
        print("3")
    case 88
        print("88")
    default
        print("默认情况")
```
`switch` 语句用于处理有许多 `if-else if` 条件的场景。

###  循环
```wurst
while a > b // while 循环
    ...

for i = 0 to 10 // for 循环，i 每次递增 1
    ...

for i = 0 to 10 step 2 // for 循环，步长为 2
    ...

for i = 10 downto 0 // 也可以递减
    ...

for u in someGroup // 遍历单位组中的单位
    ...

for u from someGroup // 遍历单位组中的单位，并将其从组中移除
    ...

for i in myList // 用于标准库中的集合类型
    ...
```

### For 循环

`for-in` 可以用于遍历任何可迭代的对象。
`for-in` 可以简单地转换为 `while` 循环：

```wurst
for A a in b
    Statements

// 等效于：
let iterator = b.iterator()
while iterator.hasNext()
    A a = iterator.next()
    Statements*
iterator.close()
```

**提示**：`iterator.close()` 会在 `while` 循环内的任何 `return` 语句之前被调用。

如果你已经有了一个迭代器，或者想要访问它的每个函数，你可以使用 `for-from` 循环。其转换也非常简单：
```wurst
let iterator = myList.iterator()
for segment from iterator
    //Statements
iterator.close()

// 等效于：
let iterator = myList.iterator()
while iterator.hasNext()
    segment = iterator.next()
    //Statements
iterator.close() 
```
**提示**：你需要手动关闭迭代器（`iterator.close()`）。

### Skip 

最基本的语句是 `_skip_` 语句，它不做任何事情。在某些极少数情况下它可能有用。

### 迭代器

你可以通过为你的类型提供一组函数来使其支持 Wurst 的迭代器。通过提供迭代器，该类型就可以用于 `for` 循环：
-  函数 **hasNext()** 返回 `boolean`（如果还有下一个元素，则返回 `true`）
-  函数 **next()** 返回 `TYPE`（返回下一个元素）

有了这两个函数，你就得到了一个可以用于 `for-from` 循环的迭代器。

如果你想让一个类型能用于 `for-in` 循环，你需要提供：

-  函数 **iterator()** 返回 `Iterator`

对于你的类型，它返回一个用于迭代的对象。
这个对象可以是一个句柄（就像 `group-iterator`），也可以是一个类实例（就像 `List-iterator`）。
你的迭代器还应该提供一个 `close` 函数，用于清理它分配的所有资源。
通常，迭代器只是在 `close` 函数中销毁它自己。

可以查看标准库中的两个示例：

#### Group-Iterator
```wurst
public function group.iterator() returns group
    // 返回单位组的副本：
    bj_groupAddGroupDest = CreateGroup()
    ForGroup(this, function GroupAddGroupEnum)
    return bj_groupAddGroupDest

public function group.hasNext() returns boolean
    return FirstOfGroup(this) != null

public function group.next() returns unit
    let u = FirstOfGroup(this)
    GroupRemoveUnit(this, u)
    return u

public function group.close()
    DestroyGroup(this)
```

如你所见，这个迭代器就是一个单位组，因此 `group` 类型需要提供上述函数。标准库通过扩展函数来实现这一点。

#### LinkedList-Iterator

```wurst
public class LinkedList<T>
    ...

    // 获取此列表的迭代器
    function iterator() returns LLIterator<T>
        return new LLIterator(dummy)

class LLIterator<Q>
    LLEntry<Q> dummy
    LLEntry<Q> current

    construct(LLEntry<Q> dummy)
        this.dummy = dummy
        this.current = dummy

    function hasNext() returns boolean
        return current.next != dummy

    function next() returns Q
        current = current.next
        return current.elem

    function close()
        destroy this
```

`LinkedList` 迭代器有点不同，因为它是一个类。
它仍然提供了所需的功能，因此可以作为迭代器使用。
它还包含一些用于辅助迭代的成员变量。
当从 `LinkedList` 的 `.iterator()` 函数返回 `LLIterator` 时，会返回一个新的实例。

### 运算简写

WurstScript 支持以下赋值运算符简写：
```wurst
i++         // i = i + 1
i--         // i = i - 1
x += y      // x = x + y
x -= y      // x = x - y
x *= y      // x = x * y
x /= y      // x = x / y
```

因为这些简写只是简单地转换为它们的等价形式，所以它们可以与重载运算符一起使用。

# 包

如上所述，用 Wurst 编写的每个代码段都必须位于一个 `_package_` 中。包定义了代码的组织结构和独立的命名空间。

包也可以有全局变量——任何不位于函数、类或模块内部的变量都是全局变量，被称为包级全局变量。


当在 VSCode 中创建一个 `.wurst` 文件时，包声明会自动添加到文件顶部：

```wurst
package SomeVSCodePackage // 文件名，也是包名
...
```

## Imports 导入 / 依赖

包可以导入（或依赖）其他包，从而访问其他包中被定义为公开（`public`）的函数和变量。

```wurst
// 声明
package SomePackage

// 导入
package Blub
import SomePackage
import AnotherPackge // 导入多个包
import public PackageX // 公开导入（见下文）
```

`import` 指令会在项目的 `wurst` 文件夹中搜索包，并从 `wurst.dependencies` 文件中搜索所有依赖的项目。

### import public 公开导入

默认情况下，包不会传递性地导出它所导入的包。例如，以下代码是错误的：
```wurst
package A
public constant x = 5

package B
import A

package C
import B
constant z = x
```

变量 `x` 在包 B 中可用，但不会从 B 传递出去。因此，在包 C 中，我们不能使用变量 `x`。
我们可以通过在 C 中也导入 A 来解决这个问题，但有时你希望避免这种额外的导入。
使用公开导入（`import public`）可以解决这个问题，因为它会传递所有导入的内容。因此，以下代码将正常工作：

```wurst
package A
public constant x = 5

package B
import public A

package C
import B
constant z = x
```

### 特殊的wurst包

默认情况下，每个包都会隐式导入一个名为 `Wurst.wurst` 的包，该包在标准库中定义。
这个包导入了一些最常用的标准库包。

如果你不想要这个标准的隐式导入，可以通过导入 `NoWurst` 包来禁用它。
该指令主要用于解决标准库中的一些循环依赖问题。


## 公开声明
默认情况下，包的所有成员都是私有的。如果想在导入该包的其他包中使用它们，必须添加 `public` 关键字。

## 常量
你可以将一个变量声明为常量（`constant`），以禁止在初始化后对其进行修改。这对生成的代码没有影响，但会在编译时抛出错误。


### 例子
```wurst
package First
int i // （默认为私有）只在此包中可用
public int j // 公开的，导入此包后即可使用

package Second
import First

int k = i // 错误
int m = j // 正确，因为 j 是公开的


package Foo
constant int myImportantNumber = 21364 // 常量必须在声明时初始化

function blub()
    myImportantNumber = 123 // 错误

public constant int myPrivateNumber2 = 123 // 注意：包级变量和常量必须在调用它们的函数之前定义。
```


## Init 初始化块

包的另一个功能是 `init` 块。
每个包可以在其内部的任何位置拥有一个或多个 `init` 块。
包的 `init` 块内的所有操作都会在地图加载时执行。

在 `init` 块开始执行时，你可以假定当前包中的所有全局变量都已初始化。
```wurst
package MyPackage
init
    print("this is the initblock")
```

*提示：* 由于魔兽争霸3的字节码限制，主函数（main function）内的操作过多会导致其无法完全执行。
为了避免这个问题，Wurst 使用 `TriggerEvaluate` 在一个单独的“线程”中执行每个包的初始化，以确保包能够成功初始化。
如果在单个包的初始化过程中计算量过大，仍然可能会出现问题，此时编译器会显示警告。


## 初始化和延迟初始化

Wurst 的初始化规则很简单：

1. 包内的初始化是自上而下完成的。一个包的初始化内容包括所有全局变量的初始化（包括静态类变量）和所有的 `init` 块。
2. 如果包 A 导入了包 B，并且该导入不是 `initlater` 导入，那么包 B 的初始化总是在包 A 之前运行。不允许出现不带 `initlater` 的循环导入。

通常，你应该通过移动受影响的代码来避免包之间的循环依赖。
但是，如果有必要，你可以通过添加 `initlater` 关键字来手动定义所依赖的包可以稍后初始化：


```wurst
package A
import initlater B
import public initlater C
import D
```

这里只有 `D` 包保证在包 `A` 之前被初始化。
包 `B` 和 `C` 允许稍后初始化。

## 配置包

全局变量和函数可以被配置。配置是通过配置包（Configuration Packages）完成的。
每个包最多只能有一个配置包，并且每个配置包只配置一个包。
包与其配置包之间的关系通过命名约定来表示：对于一个名为 `Blub` 的包，其配置包必须命名为 `Blub_config`。

在配置包中，全局变量可以使用 `@config` 注解进行标记。
这样，配置包中的变量就可以覆盖原始包中同名的变量。
在原始包中，变量应该用 `@configurable` 注解进行标记，以表明该变量是可被配置覆盖的。

示例：
```wurst
package Example
@configurable int x = 5

package Example_config
@config int x = 42
```
函数的配置也基本相同：
```wurst
package Example
@configurable public function math(int x, int y) returns int
    return x + y


package Example_config
@config public function math(int x, int y) returns int
    return x*y
```