---
title: Wurst 精选 2 - 12 月更新
layout: post
excerpt: 我们为假期准备的第一篇及时博客文章。
icon:
  type: fa
  name: fa-snowflake-o
color: blue
author: Cokemonkey11, Frotty
date: 2017-12-03
banner: /assets/images/blog/bestof2/bestOfTheWurst2Banner.jpg
---
------

![https://wurstlang.org](/assets/images/blog/bestof2/bestOfTheWurst2Banner.jpg)
{: .img-responsive}

这是关于 Wurst 和 WurstScript 社区的常规新闻系列文章的第二期。与[上一期](https://wurstlang.org/blog/thissummerinwurst.html)不同，本期内容简短，重点关注本月的进展。我们打算在未来的所有文章中都采用这种格式。

__什么是 Wurst？__ WurstScript 是一种可编译为 JASS（用于编写《魔兽争霸 3》自定义游戏脚本的语言）的编程语言，它与一套工具一同开发，提供了一致且现代的模组开发体验。它甚至可以与用 vJass 编写的地图协同工作。WurstScript 的设计旨在让难题变得更简单、更优雅，让简单问题变得极其简单。

本期的主题是__“实践中的 WurstScript”__，我们希望展示普通地图如何使用 WurstScript，而不是专注于更高级的语言特性。


# 工具链更新

* [安装工具](https://wurstlang.org/start.html)已移至一个独立的仓库。它现在会检查自身的新版本，并静默检查编译器和项目依赖的更新。要获得这些__关键__新功能，请通过上面的链接下载更新您的工具。
* [单元测试](https://wurstlang.org/manual.html#automated-unit-tests)现在拥有对编译时资源的地图范围读取权限。这使得作者可以对一些以前无法进行单元测试的行为进行测试。
* 为字符串数据操作添加了“哈希表模拟”，使其可以在编译时和单元测试中工作。
* 错误修复：
    - 修复了 vscode 自动补全在某些边缘情况下行为异常的问题。
    - 修复了一个导致 `buildmap` 命令在某些情况下生成无效地图文件的错误。
    - 在 vscode 中切换文件不再导致无效缓冲区（“ambigious reference error”）。
    - 标准库修复和新功能。
    - 修复了一些罕见的 ascii-int 解析问题，提高了 JASS 的兼容性。

特别感谢 @DD_LegionTN 和 @HappyTauren 的测试和贡献。


# Hiveworkshop 集成

本月，我们中的一些人一直在思考并采取行动，以改善 Wurst 开发与 Hive Workshop 之间的统一性。

这方面的一项成就是增加了 Wurst 标签，现在所有与代码相关的提交都已启用该标签。这包括：

* 技能提交。
* 触发器与脚本帖子。
* JASS（代码）提交。


![](/assets/images/blog/bestof2/hiveTag.jpg)
{: .img-responsive}

我们也很高兴地看到，在这一消息公布之前，就已经有了[多个](https://www.hiveworkshop.com/threads/snippet-initpackage.300738/)[提交](https://www.hiveworkshop.com/threads/quest.300223/)！

随着越来越多的社区成员参与进来，您可以期待在不久的将来看到更多与 Wurst 相关的活动。

我们很期待看到您创作的那些有趣但不太适合标准库的作品——更期待看到您在技能提交中能创造出什么样的魔法。如需代码审查反馈，请随时标记 @Frotty 或 @Cokemonkey11。

---

应读者要求，我们从现在开始将在文章中包含代码片段示例。为了契合本月的主题，这里有一个实用而简单的例子，展示了 Wurst 如何将一个简单的问题变得微不足道：

```wurst
package SimpleSpell
/// Augments the Warden blink ability to deal damage to units in the target
/// area.  Units with low enough health are instead slain.

import Assets
import ClosureForGroups
import EventHelper
import RegisterEvents

constant ID_BLINK = 'A000'

constant BASE_DAMAGE    =  100.
constant DAMAGE_RADIUS  =  150.
constant SLAY_THRESHOLD =  200.
constant BLINK_RANGE    = 1000.

constant EFFECT_POISON = Abilities.poisonStingTarget
constant EFFECT_SLAY   = Objects.nightElfBloodArcher

function onEffect()
	let caster = GetTriggerUnit()

	forUnitsInRange(getSpellTargetPos(), DAMAGE_RADIUS, (unit u) -> begin
		if caster.getOwner().isEnemyOf(u.getOwner()) and u.isAlive()
			if u.getHP() <= SLAY_THRESHOLD
				u.addEffect(EFFECT_SLAY, "origin").destr()
				caster.damageTarget(u, SLAY_THRESHOLD * 2.)
			else
				u.addEffect(EFFECT_POISON, "origin").destr()
				caster.damageTarget(u, BASE_DAMAGE)
	end)


init
	registerSpellEffectEvent(ID_BLINK, function onEffect)
```

然后，当运行 Wurst 编译器时，上述代码会生成可读性很好的 Jass（见下文）。这只是 Wurst 提供零成本抽象的一个例子，因为内联器最终会将例如 getSpellTargetPos 内联为 2 个局部定义——我们将在未来的文章中更多地讨论这个话题。

```wurst
function getSpellTargetPos takes nothing returns real
    set getSpellTargetPos_return_x = GetSpellTargetX()
    set getSpellTargetPos_return_y = GetSpellTargetY()
    return getSpellTargetPos_return_x
endfunction

function forUnitsInRange takes real pos_x, real pos_y, real radius, integer c returns nothing
    call pushCallback(c)
    call GroupEnumUnitsInRange(Group_ENUM_GROUP, pos_x, pos_y, radius, ClosureForGroups_filter)
    call popCallback()
endfunction

function onEffect takes nothing returns nothing
    local unit caster_2 = GetTriggerUnit()
    local real temp_x = getSpellTargetPos()
    local real temp_y = getSpellTargetPos_return_y
    local integer clVar = alloc_Closure_2()
    set caster[clVar] = caster_2
    call forUnitsInRange(temp_x, temp_y, 150., clVar)
    set caster_2 = null
endfunction
```

下个月，我们将回归一个更硬核的例子，演示 `@compiletime function`s + 表达式及其用途。

我们期待您的评论！像往常一样，我们在 [#inwc.de-maps](https://kiwiirc.com/client/irc.quakenet.org/#inwc.de-maps) IRC 频道讨论所有与 Wurst 相关的事情。