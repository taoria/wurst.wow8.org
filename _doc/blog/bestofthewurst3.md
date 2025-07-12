---
title: Wurst 精华 3 - 一月更新
layout: post
excerpt: Wurst 以诸多变化迈入新的一年
icon:
  type: fa
  name: fa-snowflake-o
color: blue
author: Frotty
date: 2018-01-01
banner: /assets/images/blog/bestof3/bestOfTheWurst3Banner.jpg
---
------

![https://wurstlang.org](/assets/images/blog/bestof3/bestOfTheWurst3Banner.jpg)
{: .img-responsive}

欢迎阅读我们 Wurstscript 博客的第三期！新年快乐！

**背景：** Wurst 是一种编译型的高级编程语言，为 wc3 modding 提供强大的工具和安全性，以实现集成开发体验。

本月期刊的主题是“在 wurstscript 中进行对象编辑”。


# 更新

由于 Wurst 工具的使用量增加，12 月份发现并解决了几个关键错误。请确保下载最新版本的安装工具，并相应地更新您的编译器和项目。我们也很高兴地接受了几个针对标准库的 Pull Request，并自豪地推出 [Wurstbin](https://bin.wurstlang.org/)，一个 wurst 代码分享平台。Wurst 及其工具现在也最终落户于 [wurstlang.org](https://wurstlang.org/)。

## 编译器
* 修复了几个严重的动态调度错误
* `buildmap` 命令不再输出带有损坏头文件的地图
* 为编译时实现了更多的 jass 类型和原生函数，如单位和单位组
* 编译时哈希表现在更接近地模仿 wc3 哈希表的行为。
* 类的数组成员现在可以在实例化期间进行初始化
* ObjData .txt 输出现在使用级联运算符来链接调用
* 修复了 vscode 中文本高亮差一位的问题
* 改进了单元测试控制台输出的保真度
* 以 "Tests" 结尾的包现在会从建议中忽略
* 所有工具的日志现在存储在 `~/.wurst/logs`


## 标准库
* 我们合并了几个用户创建的 pull request，即修复 OnUnitEnterLeave [#20](https://github.com/wurstscript/WurstStdlib2/pull/20) 的问题和替换损坏的同步包 [#19](https://github.com/wurstscript/WurstStdlib2/pull/19)。非常感谢 @Sir_Moriarty 和 @Trokkin
* 随着编译器单元测试的改进，标准库包 Wurstunit 也进行了重构，并添加了针对单位组和单位的测试
* 添加了更多单位组和地图的便捷函数
* UnitIndexer 现在更加灵活，允许配置要索引哪些单位以及如何索引它们

## 安装应用
* 如果其谷歌网络测试未响应 200 OK，安装程序不再崩溃
* 已添加适当的日志记录，并保存到 `~/.wurst/logs/setup.log`
* 修复了有关项目更新的回归问题
* `wurst.build` 文件现在在 vscode 中被识别为 yaml

## Wurstbin 介绍
一种优雅而简单的方式来分享您的 wurst 和 jass 代码片段。无需登录。
[点击查看](https://bin.wurstlang.org/)

![](https://i.imgur.com/8bVnUT1.png)
{: .img-responsive}


# 对象编辑 101

WurstScript 附带一个 jass “解释器”，它可以在 wc3 之外执行 Jass 代码。这通常在编译项目时完成——因此我们将其占用的时间段命名为“__compiletime__”（编译时）。这与在游戏中运行地图相反，我们称之为“__runtime__”（运行时）。


![](https://i.imgur.com/bJA1su0.png)
{: .img-responsive}

您可以通过简单地使用 `@compiletime` 注解来标记任何函数在编译时执行。唯一的限制是，您只能使用已为编译时实现的 jass 原生函数，并且只能访问脚本中同样在编译时初始化的资源。

让我们看一个小例子：

## 单位生成示例

```wurst
import UnitObjEditing
@compiletime function createUnit()
    new UnitDefinition('xyz1', 'ewsp')
	..setName("Wurst Generated Unit")
```

在这里，我们基于小精灵创建了一个新的 UnitDefinition，并为其分配了一个名称。这些 objectdata 类随后被转换为相应的二进制文件，在本例中是 `war3map.w3u`，这些文件将在运行时保留。Wurst 完全有能力在管理这些资产的同时，管理在普通对象编辑器中定义的单位。

## 保留变量

您可能希望在编译时生成值，并保留评估结果以在运行时使用。一个非常常见的用例是生成 ID。
让我们用一个自动生成的、存储在全局变量中的 ID 来增强上面的例子：

```wurst
import ObjectIdGenerator
constant MY_UNIT_ID = compiletime(UNIT_ID_GEN.next())

@compiletime function createUnit()
    new UnitDefinition(MY_UNIT_ID, 'ewsp')
	..setName("Wurst Generated Unit")
```

`UNIT_ID_GEN.next()` 调用引用了一个 `ObjectIdGenerator` 对象，该对象仅在标准单位未使用的范围内生成安全的 ID。如您所见，您必须用 `compiletime()` 包装您的表达式，以便它被评估并在运行时保留。

## 技能生成最佳实践

在生成技能时，应避免直接使用 `AbilityDefinition` ——而是使用其子类之一，例如，针对火焰箭的具体类 `AbilityDefinitionFireBolt`。这样您就不需要原始 ID，并且可以使用具体子类的函数来修改技能特定的字段。

```wurst
import AbilityObjEditing
@compiletime function generateFireBolt()
    new AbilityDefinitionFireBolt(MY_FIREBOLT_ID)
        // This setter would not be available on
        // the base class `AbilityDefinition`.
        ..setDamage(1, 200)
```

对于自定义触发的英雄法术，经常使用 `channel`。请查看 `ChannelAbilityPreset` 提供的 API：

```wurst
import ChannelAbilityPreset
@compiletime function generateLeap()
    new ChannelAbilityPreset(MY_LEAP_ID, LEVELS, true)
        ..presetTargetTypes(Targettype.POINT)
        ..presetCooldown(lvl -> 30 - (level * 2))
```

注意带有 lambda 表达式的 `presetCooldown` 调用。除了普通的 setter 函数，标准库还提供了 `preset*` 函数，它们要么是方便的包装器，要么接受闭包参数，仅用一次调用即可填充多个等级。


# Hiveworkshop Wurst 新闻

* @Frotty 和 @Cokemonkey11 现在是 hive workshop 上 Wurst 的认证代码审查员。
* 安装应用现在是工具区的一个认证资源

一如既往，欢迎在 [IRC](https://kiwiirc.com/nextclient/#irc://irc.quakenet.org/#inwc.de-maps) 上与我们聊天，或在此帖中发表评论，为我们这些月度博客文章提供反馈，并提出您希望我们下一期涵盖的内容。