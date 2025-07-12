---
title: Wurst 精选 7 - 夏日小憩
layout: post
excerpt: 经过短暂的夏日休整，我们本月携众多错误修复和改进回归
icon:
  type: fa
  name: fa-servicestack
color: orange
author: Frotty
date: 2018-07-30
banner: /assets/images/blog/bestof7/wurstbanner7.png
---
------


![](/assets/images/blog/bestof7/wurstbanner7.png)
{: .img-responsive}

经过短暂的夏日休整，我们本月携众多错误修复和改进回归，旨在提升 WurstScript 的易用性和整体用户体验。

请确保您的 [wurst 安装工具](https://wurstlang.org/start.html#install-wurst) 是最新版本，然后更新您的 wurst 安装，以获得对 wc3 补丁版本更好的处理、一些新的代码检查警告以及新的优化器功能。

工具更新
---

* VSCode 的 **runmap** 命令现在能更可靠地检测补丁级别——修复了启动游戏时出现的几种“黑屏”情况。
* 新增了关于*不当使用 `@compiletime`* 的警告和错误，这有助于避免一些用户层面的编程问题。
* 一些新的 wurst 优化器功能（见下文）。
* Wurst 中的有效函数名，如 'set' 和 'debug'，不再生成无效的 Jass 输出。
* 我们再次更新了 [JMPQ](https://github.com/inwc3/JMPQ3)，以提高 wurst 与非原版世界编辑器（例如 YDWE 和 w3x2ini）构建的地图的性能和兼容性（感谢 @WaterKnight）。
* 特别感谢用户 @dulingzhi 和 @DengSir 对 VSCode 编辑器体验的贡献：改进了语法高亮（[PR #36](https://github.com/wurstscript/wurst4vscode/pull/36)），并且语言服务器现在能更好地自动启动（[PR #37](https://github.com/wurstscript/wurst4vscode/pull/37)）。


标准库亮点
---

许多新功能和合并的 __pull requests__！标准库表现出色——以下是一些显著的变化：

* 升级对象编辑包已由 [#77](https://github.com/wurstscript/WurstStdlib2/pull/77) 改进和完善。
* 缺失的扩展函数和元组包装器已由 [#79](https://github.com/wurstscript/WurstStdlib2/pull/79)、[#80](https://github.com/wurstscript/WurstStdlib2/pull/80)、[#81](https://github.com/wurstscript/WurstStdlib2/pull/81)、[#82](https://github.com/wurstscript/WurstStdlib2/pull/82)、[#84](https://github.com/wurstscript/WurstStdlib2/pull/84)、[#85](https://github.com/wurstscript/WurstStdlib2/pull/85)、[#86](https://github.com/wurstscript/WurstStdlib2/pull/86)、[#87](https://github.com/wurstscript/WurstStdlib2/pull/87)、[#88](https://github.com/wurstscript/WurstStdlib2/pull/88)、[#89](https://github.com/wurstscript/WurstStdlib2/pull/89)、[#90](https://github.com/wurstscript/WurstStdlib2/pull/90)、[#91](https://github.com/wurstscript/WurstStdlib2/pull/91) 添加。
* `TerrainUtils` 包已由 [#67](https://github.com/wurstscript/WurstStdlib2/pull/67) 重构，以实现更好的集成和高级功能。
* `LinkedList#sort` 已修复，不再具有指数级性能。
* `ClosureEvents` 已添加到标准库中。这是一个用于轻松封装事件监听的新包。
代码示例：
```wurst
EventListener.onPointCast(hero, MY_SPELL) (caster, target) ->
    forUnitsInRange(target, 256) victim ->
        caster.damageTarget(victim)
```
非常感谢所有出色的贡献者！


焦点：代码优化
---

您知道 wurst 编译器支持多种自动优化，能让您的地图脚本保持小巧和快速吗？（在 [Wurst for vJass Users](https://wurstlang.org/tutorials/wurst_for_vjass_users.html#on-performance) 中有更详细的讨论）。

本月我们新增了*众多*新的优化器改进，使 wurst 的这一功能比以往任何时候都更加强大。以下是摘要：

* wurst 包初始化现在对所有 `init` 只使用一个触发器。
* 没有副作用的重复初始分支语句现在被合并到分支之外。
* 连续的 `set` 和 `exitwhen` 语句在适用时现在会合并为一个。
* 相同变量或函数的求和现在会被重写（`a + a => a*2`），这提高了其他优化的效率。

新优化的妙处在于它们会产生类似雪崩效应。这意味着，由于输出代码中的这些“微小”变化，其他已有的优化现在可以再次成功应用，而之前它们可能毫无作用。

您可以随时在 `_build/output.j.txt` 文件中查看输出代码。这里有一个小例子：

```wurst
let g = CreateGroup()
for u from g
    u.addHP(100)
```
会变成：

```jass
local group from = CreateGroup()
local unit iterUnit
loop
    exitwhen FirstOfGroup(from) == null
    set iterUnit = FirstOfGroup(from)
    call GroupRemoveUnit(from, iterUnit)
    call SetUnitState(iterUnit, UNIT_STATE_LIFE, GetUnitState(iterUnit, UNIT_STATE_LIFE) + 100.)
endloop
set from = null
set iterUnit = null
```

感谢您的阅读，我们希望您喜欢这些帖子——和往常一样，请告诉我们您的想法。

附言：如果您还没有为 Wurst 仓库点亮星星，请帮忙点一下，助我们达到 100 星！

祝好！

-Frotty