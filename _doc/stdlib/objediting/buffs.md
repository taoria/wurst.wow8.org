---
title: 创建增益效果对象数据
sections:
- 简介
- 常规增益效果编辑
- 虚拟龙卷风增益效果
---

### 简介

在《魔兽争霸 III》中，增益效果（buff）是单位身上的负面或正面状态效果。
然而，它们通常只是单位所拥有的某个技能的视觉表现部分。

### 常规增益效果编辑

要生成一个常规的增益效果，只需使用 `BuffDefinition` 并传入你想用作来源的增益效果的 ID。

```wurst
public constant MY_BUFF_ID = compiletime(BUFF_ID_GEN.next())

@compiletime function genBuff()
	new BuffDefinition(MY_BUFF_ID, 'Basl')
		..setName(1, "My Buff")
		..setTooltipNormal(1, "Hello there")
		..setTooltipNormalExtended(1, "This unit is buffed")
		..setIcon(Icons.bTNAcorn)
```

### 虚拟龙卷风增益效果

在处理增益效果时，我们通常希望为自定义技能生成它们，并且不产生任何副作用。
要实现这一点，我们使用 `createDummyBuffObject` 函数。
这些函数会生成一个技能和一个增益效果，你可以通过将该技能添加给单位来应用这个增益效果。
它们会返回一个包含这两个 ID 的元组。

```wurst
let MY_BUFF = compiletime(createDummyBuffObject("My Buff", "This unit is buffed", Icons.btnAcorn))

function add()
	GetTriggerUnit().addAbility(MY_BUFF.abilId)

function remove()
	GetTriggerUnit().removeAbility(MY_BUFF.abilId)
```