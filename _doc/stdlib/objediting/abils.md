---
title: 创建技能对象数据
sections:
- 简介
- 普通法术
- 依赖等级的数值
- 持续施法类法术
- 提示信息生成
---

### 简介

在《魔兽争霸III》中，大多数技能与单位不同，是唯一的，因此必须指定父技能才能访问自定义数据字段。
标准库通过在 `AbilityObjEditing` 中为每个独特法术提供类，以及为基于持续施法的自定义法术提供预设 `ChannelAbilityPreset`，很好地封装了这一过程。

### 普通法术

要生成和修改普通法术，请使用 `AbilityObjEditing` 中相应的类。
例如，如果你想生成一个火球术技能，请使用 `AbilityDefinitionFireBolt`；如果是圣盾术，则使用 `AbilityDefinitionPaladinDivineShield`。


```wurst
public constant FIREBOLT_ID = compiletime(ABIL_ID_GEN.next())

@compiletime function gen()
	new AbilityDefinitionFireBolt(FIREBOLT_ID, AbilityIds.firebolt)
		..setLevels(3)
		..setName("My Fireball Spell")
		..presetDamage(lvl -> 100. * lvl)
```

#### 依赖等级的数值

技能的另一个特点是它们有多个等级，这使得某些字段可以根据等级拥有不同的值。
在上面的例子中，我们使用 `lvl -> 100. * lvl` 来根据一个简单的计算自动填充等级数据。
这本质上与编写以下几行代码是相同的：

```wurst
	..setDamage(1, 100.)
	..setDamage(2, 200.)
	..setDamage(3, 300.)
```

但这是一种更简洁的形式，并且当您更改法术的等级数量时，它会自动适应。

### 持续施法类法术

大多数自定义法术都基于“持续施法（Channel）”。这是一个专为此目的而设计的、没有默认效果且高度可定制的技能。
默认情况下，持续施法类法术会表现出一些奇怪的行为，比如技能效果不可见，并且需要像暴风雪或火焰雨那样“引导”法术一段时间。
您可以通过在构造函数中传递 `true` 作为最后一个参数来重置所有这些属性。

看一下这个跳跃法术的例子：

```wurst
public constant JUMP_ID = compiletime(ABIL_ID_GEN.next())

@compiletime function genJump()
	new ChannelAbilityPreset(JUMP_ID, 3, true) // by passing true, we disable channel behaviour
	..presetTargetTypes(Targettype.POINT) // Set the target type for all levels
	..presetTooltipNormal(lvl -> "[|cffFFCC00W|r] |cff7BB5F7Jump - Level " + lvl.toString())
	..presetTooltipNormalExtended(lvl -> "Jumps into the target direction. Allows you to jump down, but not up, cliffs.")
	..presetCooldown(lvl -> 5. - lvl) // [4, 3, 2] seconds cooldown
	..setManaCost(lvl -> 0)
	..presetCastRange(lvl -> 9999.)
	..presetHotkey("W")
	..presetButtonPosNormal(1, 2)
	..presetIcon(Icons.bTNBootsOfSpeed)
```

### 提示信息生成

标准库提供了一个接口，用于监听技能定义上设置的每个数据字段。使用像 [Ability-Tooltip-Generator](https://github.com/Frotty/Ability-Tooltip-Generator) 这样的代码片段，您可以轻松地生成提示信息。


![](/assets/images/stdlib/tooltipgen.png)
{: .img-responsive}

```wurst
	let tgen = new AbilityTooltipGenerator("An ordinary snowball.")
	new ChannelAbilityPreset(SNOWBALL_SPELL_ID, 4, true, tgen)
	..setHeroAbility(false)
	..presetButtonPosNormal(0, 2)
	..presetTargetTypes(Targettype.POINT)
	..tooltipStartListen()
	..presetHotkey("Q")
	..setName("Snowball")
	..presetIcon("BTNFrostBolt")
	..presetCooldown(lvl -> 1.75 - (lvl / 4))
	..presetManaCost(lvl -> 4 - (lvl / 3).toInt())
	..presetCastRange(RANGE)
	..setLevelSkipRequirement(0)
	..tooltipStopListen()
```