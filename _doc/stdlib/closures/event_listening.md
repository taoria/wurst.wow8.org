---
title: 事件监听
sections:
- 导入
- 普通事件
- 特殊事件
- 聊天输入事件
- 施法事件
---

### 简介

`ClosureEvents.wurst` 是一个围绕魔兽争霸 III 事件的通用事件监听器包装器，它使用闭包作为监听器。
使用闭包允许你传递数据并将监听器保存在变量中。

> 确保对于闭包事件，始终使用事件的 EVENT_UNIT_**PLAYER** 变体，即使它只针对单个单位。相反，应使用带有单位参数的适当版本的 .add()。

### 通用事件

在大多数情况下，你会希望使用通用的 `EventListener.add(..)` API。以下是一些示例：

```wurst
EventListener.add(EVENT_PLAYER_UNIT_DEATH) ->
	Log.info("Any unit died: " + GetTriggerUnit().getName())

EventListener.add(specificUnit, EVENT_PLAYER_UNIT_DEATH) ->
	Log.info("specificUnit died: " + GetTriggerUnit().getName())
```

### 特殊事件

以下事件也可以使用，但它们不属于 `EVENT_PLAYER_UNIT_*` 类型：

- EVENT_PLAYER_LEAVE
- EVENT_UNIT_DAMAGED
- EVENT_PLAYER_CHAT_FILTER
- EVENT_PLAYER_ARROW_*
- EVENT_PLAYER_MOUSE_*

### 聊天输入事件

尽管存在 `EVENT_PLAYER_CHAT = ConvertPlayerEvent(16)`，但它并非 `TriggerRegisterPlayerChatEvent` 原生函数所使用的事件，也无法访问输入的消息。因此，如果你想访问 `GetEventPlayerChatString`，你需要使用 `EVENT_PLAYER_CHAT_FILTER = ConvertPlayerEvent(96)`。

```wurst
EventListener.add(EVENT_PLAYER_CHAT_FILTER) ->
	let input = GetEventPlayerChatString()
	let plyer = GetTriggerPlayer()
	...
```

### 施法事件

对于施法事件，存在以 `EventListener.on` 开头的便捷包装器。例如：

```wurst
EventListener.onCast(MY_SPELL_ID) caster ->
	print(caster.getName() + " casted my spell.")

EventListener.onTargetCast(myUnit, MY_SPELL_ID) (caster, target) ->
	caster.damageTarget(target, 50.)

EventListener.onPointCast(myUnit, MY_SPELL_ID) (caster, target) ->
	flashEffect(Abilities.frostNovaTarget, target)
```