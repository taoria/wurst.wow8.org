---
title: 组, 枚举, 迭代
sections:
- 导入
- 所有你需要的
- API总览
- 更高级的东西
---

### 导入

Wurst 标准库为单位（units）提供了一个简单方便的封装。

从概念上讲，`ForUnitsInXXX` 系列函数是在 `group` 句柄类型之上创建的一个抽象。

### 所有你需要的
你只需要了解这个接口。

```wurst
interface ForGroupCallback
	function callback(unit u)
```

`ClosureForGroups` 包会提供给你一些使用 `ForGroupCallback` 的函数。

### API总览

单位：

```wurst
group.forEachFrom() u ->
	// 遍历组中的每个单位 u，之后会清空该组。
```

```wurst
forUnitsOfPlayer(player p) u ->
	// 迭代玩家 p 的所有单位。
	// 注意：此函数封装了 `GroupEnumUnitsOfPlayer`，因此会包含蝗虫单位。
```

```wurst
forUnitsAll() u ->
	// 通过迭代每个玩家的单位来遍历地图上的所有单位。
```

```wurst
forUnitsInRect(rect r) u ->
	// 迭代矩形区域 r 内的单位。
```

```wurst
forUnitsInRange(vec2 pos, real radius) u ->
	// 迭代圆形范围内的单位。
```


### 更高级的东西

标准库还提供了一些更强大的工具：

```wurst
forNearestUnit(vec2 pos, real range, filterfunc filter) u ->
	// 迭代给定范围内离位置 pos 最近且符合筛选条件的单位。
```

对于可破坏物，也有一些类似的封装：

```wurst
forDestructablesInRect(r) d ->
forNearestDestructable(pos, range) ->
```