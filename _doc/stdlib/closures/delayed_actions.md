---
title: 闭包计时器
sections:
- 导入
- 延时动作
- 周期动作
---

### 导入

`ClosureTimers.wurst` 包使用 Wurst 的闭包为 Warcraft 3 的计时器机制提供了封装。通过闭包，您可以跨作用域传递数据或将回调存储在变量中。

> 尽管闭包计时器在大多数情况下都表现出色，但在对性能要求严格的情况下，`TimedLoop` 模块可以提供更好的性能。

### 延时动作

计时器最常见的用例是在一定时间后执行某个操作，而不阻塞其他代码的执行。您可以使用 `doAfter()` 函数来实现此目的。

示例如下：

```wurst
// 5秒后销毁特效
let eff = addEffect(Abilities.vengeanceMissile, ZERO2)
doAfter(5.) ->
	eff.destr()

// 一段时间后造成伤害
let attacker = GetEventDamageSource()
let target = GetTriggerUnit()
doAfter(2.) ->
	attacker.damageTarget(target, 20)
```
与 vJass 的计时器附件相比，您会立即注意到闭包的数据绑定是自动的。

闭包在创建时会捕获其内部的数据，在本例中是 `eff`、`attacker` 和 `target`。之后您可以在回调中引用它们。

在底层，闭包计时器也使用 `TimerUtils` 来实现数据绑定。

### 周期动作

另一个典型的用例是周期性地执行某个动作。为此，请使用 `doPeriodically()` 或 `doPeriodicallyCounted`。

```wurst
doPeriodically(ANIMATION_PERIOD) cb ->
	if missiles.size() == 0
		destroy cb
	else
		for missile in missiles
			missile.update()

// 倒计时
doPeriodicallyCounted(1, 3) cb ->
	print(cb.getCount().toString() + "..")
```
如您所见，在此变体中，回调会接收闭包对象作为参数。这使您可以销毁或修改它。您还可以将回调存储在变量中，并在例如关联单位死亡时销毁它。

```wurst
class MyClass
	CallbackPeriodic cb

	construct()
		cb = doPeriodically(0.5, () -> checkAura())

	function checkAura()
		...

	ondestroy
		destroy cb
```