---
title: 链表
sections:
- 简介
- 基本用法
- 其他示例
- 工厂函数
- 高阶 Lambda
- 比较器
---

### 简介

链表是一种线性数据结构，其中每个元素都是一个独立的对象。这些对象通过保存对其邻居的引用来链接在一起。
标准库中的 `LinkedList` 是一个双向链表，并实现了众多函数，使其可以作为栈或队列使用，从而用非常相似的代码替代其他包。

### 基本用法

与大多数其他数据结构一样，`LinkedList<T>` 是泛型的，其中 `T` 是您想要保存的元素的类型。

```wurst
@Test function testMap()
	let list = new LinkedList<int>
	list.add(1, 2, 3, 4, 5)
	// 随机打乱条目
	list.shuffle()
	// 获取并移除最后一个元素
	list.pop()
	list.size().assertEquals(4)
```

### 其他示例

使用默认导入的 `TypeCasting` 包，我们也可以为 T 使用 int 以外的 jass 类型。
例如，通过使用 `unit` 作为类型来创建一个单位列表。

```wurst
let unitList = new LinkedList<unit>
```

### 工厂函数

要直接从一堆值创建列表，一个简洁的单行方法是使用 `asList`。
它接受可变数量的参数，并返回一个包含这些参数的 `LinkedList`。

```wurst
let numbers = asList(1, 2, 3, 4, 5)
```

### 高阶 Lambda

如果性能不重要，并且您想要一个类似于 Java 和 Kotlin 的优秀高阶列表 API，请使用带有闭包参数的函数，例如：`forEach`、`filter`、`map`、`foldl`。
其结果和用法与其他语言非常相似。
要查看此单元测试的实际效果，请将其复制到您项目中的一个包里，并使用 `F1 -> run unit tests in file` 来运行它。
输出将显示在 Output->wurstLangServer 日志选项卡中。

```wurst
@Test function coolStuff()
	let myList = asList(1, 2, 3, 4, 5, 6, 7, 8, 9)
	// 保留能被 3 整除的值，然后求和
	let sum3 = myList.filter(elem -> elem % 3 == 0).foldl(0, (i, q) -> q + i)
	// 将所有值映射到一个新列表
	let mapped = myList.map(elem -> elem * (5 - elem))
	// 打印列表。引入了负值
	print(mapped.toString())
	// 打印排序后的列表
	print(mapped.sort().toString())
```

### 比较器

链表带有一个原地归并排序，允许您使用 `Comparator<T>` 高效地对任何类型的列表进行排序。
系统提供了 Int 和 Real 类型的比较器。要对任意类型进行排序，请实现一个自定义比较器。
比较器是一个闭包，它接受两个相同类型的参数 `(p1, p2)` 并返回一个整数，表示比较结果。
如果结果为负，`p1` 将被认为小于 `p2`。如果结果为正，`p1` 将被认为大于 `p2`。
零表示两个值相等，它们在结果列表中的位置是不确定的。
在下面的示例中，我们根据单位到目标的距离对一个单位列表进行排序。然后将最近的单位从列表中弹出。

```wurst
let ll = new LinkedList<unit>()
let target = vec2(0, 0)
ll.sortWith((u1, u2) -> (u1.getPos().distanceTo(target) - u2.getPos().distanceTo(target)).toInt())
// 获取最近的单位
let closest = ll.pop()
```

### 链表模块

如果您遇到总是需要迭代一个类的所有实例的情况，那么创建 LinkedList 对象并将实例存储在其中会带来不必要的开销。

```wurst
// 外部
let list = new LinkedList<A>
class A
  construct()
    list.add(this)

...
for a in list

```

相反，类本身可以充当一个链表，这样每个实例都知道它的下一个和上一个邻居。

```wurst
// 更好，但仍在分配迭代器
class A
  use LinkedListModule

...
for a in A

```

### 高效迭代链表

如果您有一个像下面这样遍历列表的 for-each 循环：

```wurst
LinkedList<X> list
for elem in list
```

那么每次执行该循环时，wurst 都会创建并销毁一个迭代器对象。

如果您知道您的循环不是嵌套的，您可以使用静态迭代器来代替，从而节省开销。

```wurst
# 更好，但有限制
LinkedList<X> list
for elem from list.staticItr()
```

**注意**，现在它必须是一个 for .. *from* 循环。