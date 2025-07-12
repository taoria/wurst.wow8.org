---
title: Wurst 精选 6 - 四月增强
layout: post
excerpt: 本期我们将介绍新的可变参数函数，并探讨向量元组
icon:
  type: fa
  name: fa-servicestack
color: purple
author: Frotty
date: 2018-05-17
banner: /assets/images/blog/bestof6/wurstbanner6.png
---
------


![](/assets/images/blog/bestof6/wurstbanner6.png)
{: .img-responsive}

Wurst 迈向 1.29 及更高版本
---

随着新补丁在四月初发布，我们已经在工具链中实施了必要的更改以支持它，同时仍然保持向后兼容性。

更新内容
---

*   将**最大类实例数**增加到 32768，以匹配 1.29+ 版本中新的数组大小
*   现在默认提供**新的 common.j 和 blizzard.j**
*   实现了**可变参数函数** - 这类函数可以接受某种类型的可变数量的参数。
*   改进了 VSCode 的**语法高亮**，以匹配语言文档，能够识别标点符号和运算符
*   弃用警告现在也对变量显示
*   修复了两个与扩展 Unicode 字符相关的罕见异常
*   为编译时实现了额外的原生函数，如计时器、触发器、矩形等，使得它们可以在单元测试中使用。
*   修改后的标准单位会再次输出到 `WurstExportedObjects` 中，以便更轻松地转移到生成的对象中
*   Wurst 的 Jass 解析中 `or` 运算符的优先级现已正确

__标准库__

*   合并了**超过 10 个优秀的拉取请求**，涉及对象编辑、伤害检测和新的 Blz 原生函数
*   为新的原生函数添加了扩展函数包装器，例如 **effect.setColor** 和 **item.setName**
*   受新的 24 名玩家功能影响的包（例如 **Colors**）已相应更新
*   修复并单元测试了几个关于类型转换和错误处理的问题


可变参数函数
---

Wurst 现在支持一种有限的可变参数函数。可变参数只能被迭代，不能直接访问，也无法获取其数量。

声明：
```wurst
function sum(vararg int ints)
    var result = 0
    for i in ints
        result += i
    print(result.toString())
```
用法：
```wurst
sum(1,2,3,4)
sum(14,22)
sum(23,52,5234,5,5)
```

标准库中的一些函数现在通过可变参数实现，因此提供了可变参数功能，例如用于对象编辑和编组的 ID 列表：

```wurst
@compiletime function genBuilder()
    new UnitObjDefinition('buil', 'hfoo')
    ..setNormalAbilities(asList(ID_FIREBALL, ID_FIRE_NOVA))
    ..setStructuresBuilt(asList(ID_GUARD_TOWER, ID_CANNON_TOWER))

init
    CreateGroup().addUnit(u1, u2, u3)
```

此外，现在可以使用 `asList` 来内联创建元素的 LinkedLists

```wurst
asList("This", "will", "be", "a", "list")
asList(75., 125., 175.).add(250., 375.)
asList(players[0], players[1], players[2], players[3])
```


专业技巧
---

想最高效地使用 Wurst 吗？在“专业技巧”这个栏目下，我们将定期介绍一些方法或产品来改善您的工作流程：

*   __使用向量元组__
    与其使用会泄漏且不应使用的 location，或使用会使代码量和出错可能性翻倍的 x、y 坐标，不如使用 Wurst 提供的 `vec2` 和 `vec3` 向量元组。与类不同，它们没有标识，因此可以零开销地进行转换。这意味着您可以获得直接使用坐标的速度，同时享受到类似类的语法和功能的便利性。

一些常见用法：

```wurst
// Event response
getSpellTargetPos() // instead of GetSpellTargetX/Y
getOrderPos() // instead of GetOrderPointX/Y
getOrderTargetPos()  // instead of GetWidgetX(GetOrderTarget()) etc.

// As class member, e.g. position, velocity
class A
    vec2 position

// Angle between points & Polar projection
function onEvent()
    let casterPos = GetTriggerUnit().getPos()
    let angl = casterPos.angleTo(getSpellTargetPos())
    flashEffect(Objects.impaleTargetDust, casterPos.polarOffset(angl, 256.))
```

您会注意到，许多扩展函数也期望向量元组作为参数，这使得它们的使用更加无缝。

*   __VSCode 扩展：[vscode-icons](https://marketplace.visualstudio.com/items?itemName=robertohuertasm.vscode-icons)__
    为文件夹树视图添加有意义的图标，提高视觉识别度和保真度。Wurst 代码文件目前尚未被识别，但大多数其他文件可以，这有助于区分它们。


![](/assets/images/blog/bestof6/vscodeicons.png)
{: .img-responsive}


此致
Frotty