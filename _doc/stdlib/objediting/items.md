---
title: 创建物品对象数据
sections:
- 简介
- 示例
---

### 简介

使用 `ItemObjEditing` 包中的 `ItemDefinition` 来生成物品对象。

### 示例

```wurst
public constant BALL_ITEM_ID = compiletime(ITEM_ID_GEN.next())

@compiletime function gen()
	new ItemDefinition(BALL_ITEM_ID, 'rst1')
	..setGoldCost(0)..setLumberCost(0)
	..setAbilities("")..setName("Ball")..setDescription("Throwable.")
	..setTooltipExtended("It is round")
	..setDroppedWhenCarrierDies(false)
	..setTooltipBasic("Do eet")..setModelUsed(Abilities.ancientProtectorMissile)
	..setInterfaceIcon(Icons.bTNGolemStormBolt)
```