---
title: 向量
sections:
- 简介
- 基本用法
- 向量代数
- 距离
- 极坐标投影
---


![](/assets/images/stdlib/vectors.png)
{: .img-responsive}

### 简介

Wurst 标准库中的向量指的是欧几里得向量，通常表示为一个连接点 A 和点 B 的箭头。
它们最常用于描述二维或三维空间中的位置，例如单位的位置 `unit.getPos() returns vec2` 或地图上的目标位置 `item.setPos(vec2 pos)`。`vec2` 和 `vec3` 是元组（tuples），这意味着它们没有对象 ID，因此不能用单个整数表示。
应使用向量来代替 `location` 或原始数据类型。标准库提供了开箱即用的大部分向量代数运算。

### 基本用法

```wurst
// 在 (0, 0) 创建步兵
let u = createUnit(DUMMY_PLAYER, UnitIds.footman, ZERO2, angle(0))
let pos = u.getPos()
// 打印单位的位置
print(pos.toString())

// 将位置向右移动 128 个单位
u.setXY(pos + vec2(128, 0))
```

### 向量代数

加法、减法和标量乘法通过运算符重载（+、-、*）实现。

```wurst
let a = vec2(1, 1)
let b = vec2(2, 2)
let sum = a + b
let difference  = a - b
let scalar = a * 0.5
```

### 距离

使用 `distanceTo` 和 `distanceToSq` 分别计算两个向量之间的距离或距离的平方。
距离的平方常用于范围检查，与范围的平方进行比较，以实现更快的计算（无需开方）。

```wurst
let distSq = caster.getPos().distanceToSq(target.getPos())
if distSq < 256 * 256
	// 在范围内，造成伤害
	caster.damageTarget(target, 32.)
```

此外，你还可以计算向量到线段的距离，以及判断它是否包含在三角形或多边形内。

### 极坐标投影

极坐标投影使用三角函数，通过一个距离和投影角度，在二维和三维空间中投影一个向量。
我们来快速看一下数学原理。


![](/assets/images/stdlib/trigonometry.png)
{: .img-responsive}

给定一个以弧度为单位的角度，`Sin` 和 `Cos` 函数分别返回单位圆上该角度对应点在 y 轴和 x 轴上的距离。
我们知道蓝线的长度为 1，因为它等于单位圆的半径。
因此，我们只需将 x 和 y 值相乘，即可将其缩放到我们期望的距离，得到 `vec2(Cos(this.radians) * len, Sin(this.radians) * len)`。

请记住，在《魔兽争霸3》中，零度的 xy 角指向东方，零度的 z 角指向上方。

```wurst
// 将施法者向目标方向闪烁一定距离
let caster = GetSpellAbilityUnit()
let target = getSpellTargetPos()
let blinkPos = caster.getPos().polarOffset(caster.angleTo(target), BLINK_RANGE)
flashEffect(Abilities.blink, blinkPos)
caster.setPos(blinkPos)
```