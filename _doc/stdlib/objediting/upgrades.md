---
title: 创建升级对象数据
sections:
- 简介
- UpgradeDefinition
---

### 简介

在《魔兽争霸III》中，升级是一种可研究的技术，用于改进或解锁某些效果。
每个升级最多可以有4种效果，这些效果从一个由 `UpgradeEffectType` 枚举包装的固定池中选择。

### UpgradeDefinition

`UpgradeDefinition` 将基于魔法哨兵创建一个没有任何效果的纯净升级。
使用 `addEffectXXX` 函数来添加额外效果，每个升级最多4个。
每个函数都有详细的文档，您应该查阅这些文档以了解其功能。

示例：

```wurst
let MY_UPGRADE_ID = compiletime(UPGD_ID_GEN.next())

@compiletime function genUpg()
	new UpgradeDefinition(MY_UPGRADE_ID)
	..setLevels(10)
	..presetName(lvl -> "Damage Upgrade level -" + lvl.toString())
	..presetTooltip(lvl -> "Research Damage Upgrade " + lvl.toString())
	..setGoldCostBase(25)
	..setGoldCostIncrement(10)
	..addEffectAttackDamageBonus(5, 10)
```