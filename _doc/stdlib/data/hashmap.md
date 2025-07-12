---
title: 通用哈希映射
sections:
- 简介
- 基本用法
- 其他示例
- 可迭代映射
---

### 简介

哈希表，也称为哈希映射，是一种可以将键映射到值的数据结构。如果您不需要两个键，则应使用它来代替 `hashtable` 类型。
与 Jass 的 `hashtable` 暴露大量函数来加载、保存和移除值不同，`class HashMap<K, V>` 是通用的，其中 `K` 是键的类型，`V` 是您想要保存的值的类型。

### 基本用法

由于其通用性，无论使用何种类型，对哈希映射的调用都是相同的。

```wurst
class A

class B

@Test function testMap()
	let map = new HashMap<A, B>
	let a = new A()
	let b = new B()
	// 为键添加一个值。现有值将被覆盖
	map.put(a, b)
	// 检查映射是否包含与键关联的值
	map.has(a).assertTrue()
	// 获取一个值
	let a2 = map.get(a)
	// 移除条目
	map.remove(a)
	map.has(a).assertFalse()
```

### 其他示例

使用默认导入的 `TypeCasting` 包，我们也可以将 Jass 类型用作 K 和 V。
例如，通过使用 `unit` 作为键类型，您的类作为值类型，将类附加到单位上。

```wurst
class SomeDataClass

let dataMap = new HashMap<unit, SomeDataClass>
```

### 可迭代映射

由于普通的 `HashMap` 不知道其条目，因此您无法遍历它们。
对于这种情况，有 `IterableMap`，它扩展了 `HashMap` 并将其与一个存储键的 `HashList` 结合起来。
通过这些键，您就可以访问您的值。

```wurst
let iterMap = new IterableMap<int, int>
iterMap.put(0, 3)
iterMap.put(1, 2)
iterMap.put(2, 1)
iterMap.put(3, 0)

for key in iterMap
	let val = iterMap.get(key)
	Log.info("key: " +  key.toString() + " val: " + val.toString())
```