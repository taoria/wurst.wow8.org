---
title: 虚拟单位回收器
sections:
- 简介
- 回收虚拟单位
---

### 简介

在《魔兽争霸III》中，单位是重量级对象——`CreateUnit` 和 `SetUnitPosition` 是 Jass 中性能开销最大的原生函数之一。
一次性创建大量单位可能会给配置较差的玩家造成卡顿，而持续分配单位会使内存（RAM）占用增长得更快。
因此，如果我们想要分配一个虚拟单位来施放技能或附加特效，我们不希望进行这种高开销的操作，以及它可能引发的所有额外事件。

### 回收虚拟单位

因此，我们使用一个回收器，它会使用尽可能少的单位并且不会移除它们。取而代之的是，它们会被隐藏并存储起来，直到再次需要时使用。
像 `DummyCaster` 和 `Fx` 这样的包在内部使用了 `DummyReycler`。
由于单位转向总是有延迟，我们会根据朝向角度来存储虚拟单位，并检索朝向与所需角度最接近的虚拟单位。

```wurst
init
	let dummyUnit = DummyRecycler.get(ZERO2, 0 .fromDeg())
	dummyUnit.damageTarget(target, 100)
	DummyReycler.recycle(dummyUnit)
```

不要移除或杀死虚拟单位，应始终使用 `DummyRecycler.recycle`。请注意，虚拟单位默认拥有蝗虫（locust）技能。这并非设计为一个通用的单位回收器。