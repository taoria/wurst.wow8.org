---
title: 哈希列表
sections:
- 简介
- 基本用法
- 哈希集合
---

### 简介

哈希列表与链表非常相似，但顾名思义，它由哈希表支持。在大多数情况下，你应该[改用](https://wurstlang.org/stdlib/linked_list) `LinkedList`，因为它在大多数操作中速度更快。与链表相比，哈希列表的主要优点是元素的 O(1) 访问和包含检查。这一点只在处理较大的列表时才真正重要。哈希列表还可以拥有更多的最大元素数量，因为它们的条目不消耗对象。

### 基本用法

与大多数其他数据结构一样，`HashList<T>` 是泛型的，其中 `T` 是你想要保存的元素的类型。

```wurst
@Test function testList()
	let list = new HashList<int>
	list.add(1, 2, 3, 4, 5)
	// 包含检查
	list.hash(5).assertTrue()
	// 获取
	let entry = list.get(2)
```
更多列表示例，请参阅[链表](https://wurstlang.org/stdlib/linked_list)。

### 哈希集合

哈希集合是一种哈希列表，其中每个元素只能包含一次。

```wurst
@Test function testList()
	let hset = new HashSet<int>
	hset.add(1, 2, 2, 4, 4)
	// 大小检查
	hset.size().assertEquals(3)
	// 获取
	hset.get(2).assertEquals(4)
```