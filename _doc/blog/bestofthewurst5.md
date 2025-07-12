---
title: Wurst 精选 5 - 三月下旬更新
layout: post
excerpt: 本月我们将介绍一些创建 Wurst 法术时的技巧
icon:
  type: fa
  name: fa-servicestack
color: blue
author: Frotty
date: 2018-03-23
banner: /assets/images/blog/bestof5/bestof5banner.png
---
------

> **更新于** 2018年11月29日


![](/assets/images/blog/bestof5/bestof5banner.png)
{: .img-responsive}

# 1.29 和 PTR

Wurst 团队对新的 PTR 变更将为 wc3 modding 社区带来什么感到兴奋和好奇。我们期待着在稳定的战网服务器上发布，并将尽快为 Wurst 工具链添加支持。

目前，我们不打算设立一个单独的分支来支持 PTR 安装。然而，如果经典团队继续广泛使用测试服务器，我们将来可能会考虑这个选项。

我们正在积极思考 PTR 的变更对我们意味着什么，以及它将如何影响 2018 年的时间线。例如，既然 24 人地图即将到来，我们将优先支持从 VSCode 编辑地图玩家元数据。



![](/assets/images/blog/bestof5/24players.jpg)
{: .img-responsive}
上图：在 Wurst 中实现的 24 种独特的玩家颜色！

更新
===

我们本月的博客文章发布得有点晚，因为没有重大的更新要介绍，而且我们一直专注于高质量的工作以及关注 Wurst 的稳定性。话虽如此，在三月份，我们改进了 lambda 语法，以获得更大的灵活性和更简洁的代码。此外，我们再次收到了来自社区的一些贡献。

* 针对 lambda 表达式和闭包的类型推断以及新的代码块处理
* 我们为标准库解决了一些已开启的问题 ([#44](https://github.com/wurstscript/WurstStdlib2/issues/44), [#45](https://github.com/wurstscript/WurstStdlib2/issues/45), [#48](https://github.com/wurstscript/WurstStdlib2/issues/48)) 并合并了 7 个拉取请求 ([#47](https://github.com/wurstscript/WurstStdlib2/pull/47), [#49](https://github.com/wurstscript/WurstStdlib2/pull/49), [#50](https://github.com/wurstscript/WurstStdlib2/pull/50), [#51](https://github.com/wurstscript/WurstStdlib2/pull/51), [#51](https://github.com/wurstscript/WurstStdlib2/pull/52), [#54](https://github.com/wurstscript/WurstStdlib2/pull/54), [#55](https://github.com/wurstscript/WurstStdlib2/pull/55), [#56](https://github.com/wurstscript/WurstStdlib2/pull/56))。
* Wurst Setup 获得了一些稳定性更新，我们正在努力将其与 Wurst 工具套件进一步集成。

*注意*：我们仍在为我们的[展示页面](https://wurstlang.org/showcase.html)寻找 Wurst 资源！

Lambda 的新玩法
---
现在编写 lambda 表达式时，类型是可选的：
```wurst
interface UnitFunction
    function run(unit u)

function group.foreach(UnitFunction closure)
    ...

// Before:
g.foreach((unit u) -> print(u.getName()))
// Now also possible:
g.foreach(u -> print(u.getName()))
// Works for multiple params too:
list.foldl((x, y) -> x + y)
```
此外，如果 lambda 是当前行的最后一个参数，你现在可以去掉语句 lambda 的 `begin` 和 `end` 关键字。lambda 会被移动到函数调用之后，像这样：
```wurst
// Before:
doAfter(10.0, () -> begin
	u.kill()
	createNiceExplosion()
	doMoreStuff()
end)

// Now also possible:
doAfter(10.0) ->
	u.kill()
	createNiceExplosion()
	doMoreStuff()
```


使用新语法，缩进定义了代码块。这允许人们定义自定义的控制流，看起来非常像语言内置的结构。

`begin` 和 `end` 语法当然会保留，以实现向后兼容，并用于 lambda 不是最后一个参数的情况。

Wurst 法术探秘
---

在 wc3 中，法术是一个非常流行的脚本编写选择，大多数地图至少有一些脚本化的技能。

在这里，我们将展示一个简洁的例子，说明如何结合物体编辑、事件监听和闭包的使用，轻松创建一个不错的法术。

在这个例子中，我们正在制作一个名为“Conflagration”（大火）的定时爆炸法术。爆炸会对击中的敌人施加一个燃烧的减益效果，这会使后续的“Conflagration”命中造成额外伤害。我们的目标是使该法术易于配置，并最终将其上传到 GitHub，以便可以轻松地导入到 Wurst 项目中。

配置包
---

要制作一个易于配置的法术，有时先考虑配置包会很有帮助。根据概念，我们知道我们希望允许用户以何种方式自定义技能，例如：

- 基础伤害，额外伤害
- 法术半径、效果、持续时间
- 增益效果持续时间、效果

为了将其转化为 Wurst 代码，我们用列表中的变量创建一个新的包。为了让用户可以配置它们，我们必须用 `@configurable` 对它们进行注解。

```wurst
package ConflagrationConstants
import public Assets

@configurable public let BASE_DAMAGE = 50.
@configurable public let BONUS_DAMAGE = 50.

@configurable public let SPELL_EFFECT_PATH = Units.infernalBirth
@configurable public let BONUS_EFFECT_PATH = Abilities.fireBallMissile
@configurable public let SPELL_EFFECT_DURATION = .75
@configurable public let SPELL_RADIUS = 256.

@configurable public let SPELL_ICON = Icons.bTNBreathOfFire
@configurable public let SPELL_NAME = "Conflagration"
@configurable public let SPELL_TT_NORMAL = "Cast Conflagration"
@configurable public let SPELL_TT_EXTENDED = "Conjures a firey explosion at the " +
    "target, damaging nearby enemies and applying a buff, which will cause them to " +
    "take more damage on succeeding hits of this spell."

@configurable public let BUFF_DURATION = 7.5
@configurable public let BUFF_EFFECT_PATH = Abilities.incinerateBuff

@configurable public let BUFF_NAME = "Conflagrated"
@configurable public let BUFF_TT = "This unit has weakness to fire"
```

显然，以上代码描绘了一个伤害和持续时间都相当低的快速、单位型法术的蓝图。


法术物体
---

接下来，我们将为我们的法术生成所需的物体。我们需要一个技能物体给使用该法术的单位，以及一个增益效果物体，用于在视觉上和状态上显示“Conflagration”的存在。

因此，我们创建一个新的包
```wurst
package ConflagrationObjects
import public ConflagrationConstants
import public BuffObjEditing
import ChannelAbilityPreset

public let SPELL_ID = compiletime(ABIL_ID_GEN.next())
public let BUFF_OBJ = compiletime(createDummyBuffObject(BUFF_NAME, BUFF_TT,	Icons.bTNFireBolt, Abilities.incinerateBuff, "chest"))

@compiletime function genObj()
    new ChannelAbilityPreset(SPELL_ID, 4, true)
    ..setName(SPELL_NAME)
    ..presetTooltipNormal(lvl -> SPELL_TT_NORMAL)
    ..presetTooltipNormalExtended(lvl -> SPELL_TT_EXTENDED)
    ..presetIcon(SPELL_ICON)
    ..presetButtonPosNormal(0, 0)
    ..presetManaCost(lvl -> 0)
    ..presetCooldown(lvl -> 4.)
    ..presetHotkey("Q")
    ..presetTargetTypes(Targettype.POINT)
    ..presetAreaofEffect(lvl -> SPELL_RADIUS)
    ..presetOption(Option.TARGETIMAGE, true)
```

法术效果
---

最后，我们创建实际的效果。在地图初始化时，我们注册一个法术事件监听器，当任何单位施放我们生成的法术时，它将被触发。

```wurst
package Conflagration
import ConflagrationObjects
import ClosureTimers
import ClosureForGroups
import HashMap
import ClosureEvents

let buffId = BUFF_OBJ.abilId
let buffMap = new HashMap<unit, CallbackSingle>()
init
	EventListener.onPointCast(SPELL_ID) (caster, tpos) ->
		flashEffect(SPELL_EFFECT_PATH, tpos)
		doAfter(SPELL_EFFECT_DURATION) ->
			forUnitsInRange(tpos, SPELL_RADIUS) u ->
				if u.hasAbility(buffId)
					caster.damageTarget(u, BONUS_DAMAGE)
					flashEffect(BONUS_EFFECT_PATH, tpos)

				caster.damageTarget(u, BASE_DAMAGE)
				u.addAbility(buffId)
				if buffMap.has(u)
					destroy buffMap.get(u)
				let cb = doAfter(BUFF_DURATION) ->
					if buffMap.has(u)
						buffMap.remove(u)
						u.removeAbility(buffId)
				buffMap.put(u, cb)
```

我们使用来自 `ClosureEvents.wurst` 的 `EventListener.onPointCast` 来监听 `SPELL_ID` 的施法事件。当一个具有正确 ID 的 `SPELL_EFFECT` 事件发生时，下面缩进的代码块将被执行。lambda 参数 `(caster, tpos)` 将被适当地填充为施法单位和目标点。


![](/assets/images/blog/bestof5/spellAction.gif)
{: .img-responsive}
法术实际效果

为我们的项目创建仓库
===

最后，让我们从我们的 Wurst 项目创建一个 git 依赖。我们将使用 [GitHub](https://github.com/) 及其对应的[桌面应用](https://desktop.github.com/)，这需要一个免费账户。如果你还没有，请创建一个账户并设置好该应用。

一旦你设置好了应用，我们就可以通过选择 `File -> Add Local Repository` 在我们的项目文件夹内创建一个新的 git 仓库。选择你项目的根目录，然后选择 `create a repository`：


![](/assets/images/blog/bestof5/addRepo.png)
{: .img-responsive}

保持所有设置不变并创建仓库：


![](/assets/images/blog/bestof5/createRepo.png)
{: .img-responsive}

太棒了，我们的仓库已经创建好了。我们所有现有的文件都已添加到一个初始提交中。不应被版本控制的文件会被设置工具生成的配置自动忽略。现在我们可以发布我们的仓库了：


![](/assets/images/blog/bestof5/publishRepo.png)
{: .img-responsive}

如果一切顺利，你的仓库现在已经发布了。你可以在这里查看我们在这篇博客文章中创建的仓库：https://github.com/Frotty/ConflagrationSpell

**注意** 如果你想了解更多关于 git 工作流程的信息，可以在线查看一些教程，比如[这个](https://try.github.io/levels/1/challenges/1)。

使用仓库作为 Wurst 依赖
---

打开设置工具并导入某个其他项目的 wurst.build 文件。在 `Advanced` 下，你现在可以输入我们上面创建的仓库链接。点击 `Update Project` 来更新依赖项。


![](/assets/images/blog/bestof5/setup1.png)
{: .img-responsive}


![](/assets/images/blog/bestof5/setup2.png)
{: .img-responsive}

重新加载 VSCode，你应该就能够按需导入该法术了。

---

就是这样！感谢阅读又一篇 Wurst 精选博客。

此致
Frotty & Cokemonkey11