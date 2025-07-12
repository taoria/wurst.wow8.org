---
title: 虚拟施法
sections:
- 简介
- 制作虚拟技能
- 施放瞬发技能
- 施放延迟技能
---

### 简介

虚拟施法单位通常用于自定义技能中，以动态地对目标或点施放技能。
例如，对一个范围效果（AoE）内的所有单位施放妖术，或向多个方向施放穿刺。

### 制作虚拟技能

由于将使用一个常规单位来虚拟施放技能，因此传递的技能必须具有特殊属性。
即无冷却时间、无魔法消耗、高施法距离、无依赖项且不是英雄技能。
实现这一点的简单方法是使用 `.setDummyAbility()`（最近新增的功能，请确保您的 stdlib 是最新的）。
然后设置与技能相关的数值，如持续时间。请看下面这个变形术的例子：

```wurst
constant POLY_DUMMY_ID = compiletime(ABIL_ID_GEN.next())

@compiletime function genPolymorph()
	new AbilityDefinitionPolymorph(POLY_DUMMY_ID)
	..setDummyAbility()
	..setDurationNormal(1, 5.)
```


### 施放瞬发技能

对于非持续施法或穿刺类技能，即那些只造成瞬时伤害或无伤害，并且没有附加于施法者身上的效果的技能，您应该使用 `InstantDummyCaster`。
它将只使用一个单位来施放所有技能。请看下面这个使用上述变形术虚拟技能的例子，它之所以能正常工作，是因为它是瞬发的：

```wurst
import InstantDummyCaster

InstantDummyCaster.castTarget(DUMMY_PLAYER, POLY_DUMMY_ID, 1, OrderIds.polymorph, target)
```

### 施放延迟技能

持续施法技能、穿刺，或任何在施法发生后造成伤害的技能，都应该使用一个会存留一段时间的 `DummyCaster` 对象来施放。
这样，伤害将由正确的玩家造成，并且附加在施法者身上的效果（如连锁闪电）将保持完整。
根据您技能的持续时间调整延迟参数。您可以使用一个对象施放多个技能，只要它们都在最后一个技能的延迟时间到期前发生即可。`BLIZZARD_DUMMY_ID` 指的是一个与上述变形术虚拟技能同样方式创建的暴风雪虚拟技能。
切勿手动销毁 `DummyCaster` 实例，应在使用时即时分配它们。

```wurst
// Casts blizzard 4 times in cross formation around target point
new DummyCaster(casterPos)
	..owner(caster)
	..delay(15)
	..castPoint(BLIZZARD_DUMMY_ID, 1, OrderIds.blizzard, target + vec2(128, 0))
	..castPoint(BLIZZARD_DUMMY_ID, 1, OrderIds.blizzard, target + vec2(-128, 0))
	..castPoint(BLIZZARD_DUMMY_ID, 1, OrderIds.blizzard, target + vec2(0, 128))
	..castPoint(BLIZZARD_DUMMY_ID, 1, OrderIds.blizzard, target + vec2(0, -128))
```

### 应用法球效果

使用 `DummyCaster`，您还可以对目标单位应用法球效果，例如霜冻攻击或毒性攻击。您应该使用 `attackonce` 命令。

```wurst
// Poisons target unit
new DummyCaster().castTarget(AbilityIds.poisonSting, 1, OrderIds.attackonce, target)
```