---
title: 创建魔兽争霸对话框
sections:
- 简介
- DialogBox
---


![https://wurstlang.org](/assets/images/stdlib/dialogbox.jpg)
{: .img-responsive}

### 简介

在魔兽争霸 III 中，对话框（dialog）指的是向玩家显示的模态对话框，其中包含消息文本和各种可供点击的按钮。
对话框有几个缺点，例如会禁用用户输入和聊天，因此在多人游戏模式中应谨慎使用。

### DialogBox

如果你想创建一个对话框，可以使用 `DialogBox` 类。你可以重复使用同一个 `DialogBox`，并在点击事件中使用 `GetTriggerPlayer()` 来获取玩家：

```wurst
init
	let dBox = new DialogBox("你是个新手吗？")
	dBox.addButton("是") ->
		GetTriggerPlayer()..addGold(500)..display(GetTriggerPlayer(), false)

	dBox.addButton("否", () -> dBox.display(GetTriggerPlayer(), false))

	doAfter(0.1) ->
		// Don't call display during init
		for i = 0 to 11
			dBox.display(players[i], true)
```


使用全局变量并在施法时显示的示例：

```wurst
constant GOLD_BOX = new DialogBox("需要额外的初始金币吗？")

init
	// add buttons
	dBox.addButton("是") ->
		...

	EventListener.onCast(SPELL_ID) (caster) ->
		GOLD_BOX.display(caster, true)
```

或者，你也可以即时创建对话框，并将玩家传递给回调函数：

```wurst
function showBox(player p)
	let tempBox = new DialogBox(SOME_DYNAMIC_STRING)
	tempBox.addButton("好的") ->
		Log.info(p.getName() " 已接受。")
		destroy tempBox
	tempBox.display(p, false)
```