---
title: 手册
---


# 其他事项

## 堆栈追踪

你可以通过命令行开关 `-stacktraces` 来启用它。启用后，每个错误都将显示一个堆栈追踪，其中包含导致错误的每个函数调用的行号和文件名。错误必须通过魔术函数 `function error(string msg)` 来生成。

# 标准库

Wurst 自带一个包含一些实用标准函数的库。
你可以在这里找到生成的 HotDoc 页面：[Wurstscript 标准库](https://peeeq.de/hudson/job/Wurst/HotDoc_Standard_Library_Documentation/index.html)
然而，学习该库的最佳方式仍然是查看源代码。

# 代码风格规范

本节将详细介绍Wurst编程时应遵循的最佳实践：

## 规范一：可读性优先

代码应具备良好的可读性，使其他开发者能够轻松理解逻辑流程。理想情况下，你的代码应该像普通的英文文本一样易于阅读。
为函数和变量使用合适的名称，使你的代码读起来像自然文本。

- 创建函数，帮助你在更高的抽象层次上表达意图。
- 当函数或变量的类型为布尔值时，像提问一样命名它。这个名称在 `if` 语句中使用时应该听起来很自然。
- 使用类函数和扩展方法，使代码可以从左到右阅读。


示例：

    // 不太易读：
    if GetUnitState(h, UNIT_STATE_LIFE) <= 0.405
        let t = NewTimer()
        t.setData(...)
        t.start(...)
        ...

    // 更好：
    if hero.isDead()
        hero.reviveAt(town, REVIVE_TIME)

    // 计时器的相关逻辑放在另一个小函数里


### 规则 1.1 为你的代码编写文档
### 规则 1.2 保持函数简短
### 规则 1.3 黄金路径




## 规则 2：编写可检查的代码

你的代码应该能被编译器检查。Wurst 编译器为你提供了一些强大的类型检查工具。明智地使用它们，以减少你在程序中可能犯的错误。你可能还想观看 [Yaron Minsky 的精彩演讲](https://vimeo.com/14313378)，他对此总结得很好：“让无效状态无法表示”。当然，Wurst 不像 ML 那样复杂，但你仍然可以做很多事情。

- 尽可能避免使用 `castTo` 表达式
- 使用像 `HashMap` 这样的高级库，而不是像 `Table` 甚至 `hashtable` 原生函数这样的低级库
- 使用元组类型为值赋予意义（参见 `Vector` 示例）
- 将枚举与 `switch` 语句结合使用

## 规则 3：DRY：不要重复自己

当你在项目的多个地方发现相同的代码行时，尝试将通用代码放入函数、类或模块中。

## 规则 4：KISS：保持简单，傻瓜！

总是尝试为你的问题选择最简单的解决方案。避免过早优化。


## 规则 5：使用面向对象编程

### 避免使用 instanceof

使用 *instanceof* 通常是面向对象使用不当的标志。通常，`instanceof` 检查可以通过使用动态分派来替代。让我们看一个例子：

    if shape instanceof Circle
        Circle c = shape castTo Circle
        area = bj_PI * c.radius*c.radius
    else if shape instanceof Rect
        Rect r = shape castTo Rect
        area = r.width * r.height

更好的做法是在 `Shape` 中定义一个 `area` 函数，然后在 `Circle` 和 `Rect` 中分别实现它。这样你就可以直接写：

    area = shape.area()

然后，正确的 `area` 方法将根据 `shape` 的类型自动被选择。


## 在旧地图中使用 Wurst

Wurstpack World Editor 会将你的 Wurst 包编译并链接到你的地图中，即使该地图已经包含 GUI/Jass/vJass——Wurst 将与它们并行运行。

对于更具冒险精神的用户，Wurst 还有一个类似 Jass 的方言，名为 Jurst，并支持将 vJass 自动提取为 Jurst。此功能应被视为测试版。

### Jurst

Jurst 是 Wurst 的一种方言，它具有与 Wurst 相同的功能，但语法类似于 vJass。你可以使用 Jurst 来适配 vJass 代码，但由于支持的功能存在差异，仍然需要一些手动步骤。

总的来说，Jurst 不如 Wurst 严格。以下代码片段都是有效的 Jurst 代码：

    library LooksLikeVjass initializer ini
        private function ini takes nothing returns nothing
            local trigger t = CreateTrigger()
            t = null
        endfunction
    endlibrary

和

    package NearlyTheSameAsWurst
        function act()
            print(GetTriggerUnit().getName() + " died.")
        end

        init
            CreateTrigger()..registerAnyUnitEvent(EVENT_PLAYER_UNIT_DEATH)
                           ..addAction(function act)
        end

    // 注意：此处故意没有 "end"。

如你所见，Jurst 能够访问 Wurst 代码，包括来自 Wurst 标准库的包，例如 `print`。但是，你不应该尝试从 Wurst 包中访问 Jurst（或 Jass/vJass）代码。

特别感兴趣的用户可以阅读 [Jurst 语言定义](https://github.com/wurstscript/WurstScript/blob/master/de.peeeq.wurstscript/parserspec/Jurst.g4) 以获取合法的 Jurst 语法的精确参考。

### 自动将 vJass 提取为 Jurst

处理旧地图的一种有用方法是自动将现有的 vJass 提取为 Jurst。在 Eclipse 中，你可以右键单击地图，并将所有自定义文本触发器导出为 Jurst 文件。这些文件将存储在 `wurst/exported/` 文件夹中。


![提取到文件](/assets/images/extractToFiles.png)


完成此操作后，你需要在触发器编辑器中手动删除这些成员。请注意，提取到文件的功能可能会产生一些带有语法错误的文件，因此在继续操作前请务必备份你的地图。

# 优化器

Wurst 编译器内置了一套脚本优化工具，启用后，会以多种方式优化生成的 Jass 代码。在使用非常增强和复杂的系统时，Jass 优化对于提供可玩的游戏帧率变得非常重要。一方面，优化器会清理代码，减小其体积并移除无用内容，以减少 RAM 使用量。另一方面，它也提供一些优化来提高代码的执行速度和性能。

## 清理

被移除、更改或甚至不被打印的内容

*   注释
*   不必要的空格
*   多余的括号
*   一些无用的 Jass 常量被替换为 "null"

## 名称压缩

较短的名称执行更快且占用更少的空间，因此所有函数和变量的名称都会被压缩到可能的最短名称。

## 内联

内联不是一项简单的任务，但它能为使用许多不同函数的系统带来巨大的性能提升。它还使编码更容易、更具可读性，因为你不必担心将内容拆分到太多函数中会带来的性能损失。此外，blizzard.j 中的函数，如 BJ 和 Swap，也可以被内联。

在当前的实现中，当一个函数只有一个退出点时，它可以被内联。这种情况发生在函数没有 `return` 语句，或者唯一的 `return` 语句位于函数末尾时。

一个函数是否真的被内联取决于编译器中的一些启发式算法。该算法试图在执行速度和地图脚本大小之间取得平衡，因为内联通常会使代码变长但运行更快。一个函数越短，就越有可能被内联。一个函数在许多不同地方被调用，就越不可能被内联。最好不要依赖于关于内联器的更多保证，因为启发式算法会时常改变。

具有常量值的全局变量以及常量局部变量也会被内联。

## 常量折叠与常量传播

只包含常量的表达式在编译时计算。带有常量条件的 `if` 语句会被移除。这两种机制协同工作，以移除不必要和不可达的代码。

# 错误与警告

Wurst 提供一些错误和警告，以帮助在开发过程的早期发现错误。本章将解释其中一些错误和警告，以及一些修复它们的方法。

## 警告

*   对局部变量 x 的赋值从未被读取

    这个警告意味着在所有可能的执行路径中，赋值中使用的值从未被读取。这通常意味着你忘记使用它了，所以这可能是一个需要修复的错误。有时它只是一些不必要的代码，如下例所示：

        int x = 0
        if someCondition
            x = 2
        else
            x = 3
        print(x.toString())

    在这种情况下，修复这个警告很简单，只需移除未使用的值：

        int x
        if someCondition
            x = 2
        else
            x = 3
        print(x.toString())


*   变量 x 从未被读取。

    通常这个警告也意味着你有一些不必要的代码，或者你忘记了使用某些东西。在极少数情况下，你需要传递一个参数但不想使用它。在这些情况下，你可以通过在变量名前加上下划线来**抑制该警告**：

        function foo(int _x)
            return 5

*   对 ... 的赋值可能没有效果

    这个警告通常出现在类似以下的赋值中：

        construct(int emeny)
            this.enemy = enemy

    注意，参数中有一个拼写错误，导致赋值错误。

*   导入的 ... 从未被直接使用。

    这通常意味着你导入了一个包但从未使用它，所以你可以移除该导入。存在一些边缘情况，比如隐式泛型转换，目前无法正确检测。

有些警告只是为了确保 Wurst 中的一些通用编码标准：

*   函数名应以小写字母开头。
*   变量名应以小写字母开头。
*   类型名应以大写字母开头。