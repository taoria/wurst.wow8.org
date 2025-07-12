---
title: 角度
sections:
- 简介
- 基本用法
---


### 简介

wurst 中的 `angle` 元组用于描述角度值，以防止弧度和角度之间的混淆。
使用 `angle` 而不是原始实数可以提供零开销的安全性和流畅的 API。

### 基本用法

```wurst
// Create from degrees or radians
let angl1 = 180 .fromDeg()
let angl2 = (PI/2.).fromRad()
// Trigonometry on angles
let cosVal = angl1.cos()
// Create unit vector from angle
let uVec = angl1.direction()
```

angle 元组也用作标准库函数的参数和返回值：

```wurst
// Get unit facing as angle
let facing = GetTriggerUnit().getFacingAngle()
// Use facing angle to offset vector
let point = GetTriggerUnit().getPos().polarOffset(facing, 100)
```