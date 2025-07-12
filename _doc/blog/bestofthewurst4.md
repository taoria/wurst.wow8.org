---
title: Wurst 精华 4 - 二月更新
layout: post
excerpt: 本月我们将展示我们的路线图并解释单元测试
icon:
  type: fa
  name: fa-servicestack
color: blue
author: Frotty
date: 2018-02-01
banner: /assets/images/blog/bestof4/februaryBanner.png
---
------


![](/assets/images/blog/bestof4/februaryBanner.png)
{: .img-responsive}

在本期博客的第四篇文章中，我们将回顾 WurstScript 在 2018 年的开端以及我们的路线图。我们再次激动地提到，我们优秀社区中的用户正在积极参与并为 Wurst 做出贡献。

本月代码片段的主题是**在 Wurst 中进行单元测试**。

WurstScript，简称 *Wurst*，旨在成为一个完全集成的 WC3 模组开发解决方案，专注于更高级别的编程和 IDE 体验，同时不牺牲生成地图脚本的效率。


更新
===

* `runmap` 命令现在如果存在 `war3.exe`，则会使用它，这可以缩短加载时间。游戏可执行文件的检测也得到了改进。
* 移除了处理 MPQ 文件时创建过时临时文件的操作，这可以防止在某些系统上出现权限问题。
* 为 `force` 和 `gamecache` 添加了编译时模拟（mock），使其可以在对象编辑和单元测试中使用。
* 感谢 @lep，`StringHash` 的编译时实现现在返回与运行时相同的值。
* 我们合并了许多来自标准库的优秀 Pull Request ([#23](https://github.com/wurstscript/WurstStdlib2/pull/23), [#25](https://github.com/wurstscript/WurstStdlib2/pull/25), [#26](https://github.com/wurstscript/WurstStdlib2/pull/26), [#27](https://github.com/wurstscript/WurstStdlib2/pull/27), [#28](https://github.com/wurstscript/WurstStdlib2/pull/28), [#30](https://github.com/wurstscript/WurstStdlib2/pull/30), [#31](https://github.com/wurstscript/WurstStdlib2/pull/31), [#33](https://github.com/wurstscript/WurstStdlib2/pull/33), [#34](https://github.com/wurstscript/WurstStdlib2/pull/34), [#35](https://github.com/wurstscript/WurstStdlib2/pull/35), [#36](https://github.com/wurstscript/WurstStdlib2/pull/36), [#37](https://github.com/wurstscript/WurstStdlib2/pull/37), [#38](https://github.com/wurstscript/WurstStdlib2/pull/38), [#41](https://github.com/wurstscript/WurstStdlib2/pull/41), [#42](https://github.com/wurstscript/WurstStdlib2/pull/42))
    * 非常感谢 @HappyTauren @Trokkin @Jampi0n @Karlek @Krankx 和 @Chaosy
    * 特别感谢 @Sir Moriarty 为我们提供了全新且改进的 FileIO 和 Sync 包。这是我们迄今为止合并的最大的 PR！
* 我们对持续集成流程进行了一些改进，因此标准库的变更以及对其的 Pull Request 变得更加无缝和强大。
* Wurst 的底层 MPQ 库 [JMPQ](https://github.com/inwc3/JMPQ3) 已更新，支持 pkware 解压缩和无文件数据提取。
* [Wc3libs](https://github.com/inwc3/wc3libs) 也已更新，提供了 WTS 支持和 Jass 的 `StringHash` 实现。

*注意*：[作品展示](https://wurstlang.org/showcase.html) 页面现已正式上线 - 你应该提交你的 Wurst 地图，以争取在聚光灯下展示的机会！


2018 年路线图
===

我们今年的主要目标是：

* 增强 `wurst.build` 和设置工具，以实现更完整、更无缝的地图编译。具体来说：
    * 支持配置 `scenario` 和 `force` 选项
    * 提供一个无缝的 API，用于在地图构建前后运行外部工具
    * 使设置工具能完全通过命令行操作
* 达到 100+ 个 GitHub 仓库，成为 Github Linguist 支持的语言。我们需要你在 Github 上发布你的 Wurst 仓库！
* 为编译时实现大部分 Jass 原生函数/类型。主要用于测试与 WC3 相关的代码。
* 优化、扩展和对标准库进行单元测试。


Warcraft 3 中的单元测试
===

![](/assets/images/blog/bestof4/unitTestsBanner.png)
{: .img-responsive}

前言：[单元测试](https://en.wikipedia.org/wiki/Unit_testing)中的“单元”并非指 WC3 中的单位——而是指被测试的单个源代码片段。

测试允许你在编译时，在 Warcraft 3 游戏环境之外验证代码的行为。Wurst 会解释你的代码，而不是让游戏解释你生成的 Jass 代码，因此可以在代码错误在游戏中发生之前就断言其正确性并发现错误。

单元测试的主要好处是，它可以轻松地覆盖大多数可以想象到的行为，甚至是那些在游戏中很少发生的行为，而无需在游戏中浪费时间去执行这些行为。

单元测试的一个注意事项是，编译时的 Jass 解释依赖于执行测试的 Wurst 编译器的完整性和正确性，而非 Warcraft 引擎。Wurst 并未实现所有的原生函数，而且由于我们不了解 WC3 的内部工作原理，它无法完全相同地模拟所有东西。

总的来说，单元测试有很多好处，例如：
* 证明你的代码确实做了它应该做的事情。
* 通过尝试破坏代码来帮助你改进和理解它。
* 对你的代码库进行大的改动，并验证一切仍然正常工作。
* 从根本上减少错误的数量。

最容易测试的单元是那些不包含 Warcraft 特定元素的单元。以下是一些来自标准库的，针对我们的数据结构、算术和字符串操作的精简单元测试示例。

```wurst
@Test function testAdd()
	new HashList<int>..add(5).get(0).assertEquals(5)

@Test function linearVecTest()
	let v = linear(vec2(3,4), vec2(6,2), 0.5)
	v.x.assertEquals(4.5)
	v.y.assertEquals(3)

@Test function testJoin()
    new LinkedList<string>()..add("this")..add("is")..add("a")..add("string")
    .joinBy(" ").assertEquals("this is a string")
```

编写单元测试
---

要将任何函数变成一个测试，只需用 `@Test` 对其进行注解。所有带有此注解的函数都将被 Wurst 识别。这并不会以任何方式限制该函数，理论上它仍然可以在你的代码中使用，而不仅仅是作为测试，但我们不推荐这样做。

一个单元测试由两部分组成——执行待测试的代码和一个断言，断言会将实际结果与期望值进行比较。你可以在 `Wurstunit` 包中找到断言函数。

最简单的例子是算术测试，因为我们可以很容易地知道期望值。例如：

```wurst
@Test function example()
    let actual = (10 .squared()) * 6
    let expected = 600
    actual.assertEquals(expected)
```

如你所见，我们计算出我们想要测试的内容，然后断言该值是我们期望的值。

运行单元测试
---

在 VScode 中，使用 F1 打开任务提示符，输入 "run test" 并选择其中一个选项。

![](/assets/images/blog/bestof4/runTestVS.png)
{: .img-responsive}

你可以在 __Output__ 选项卡中看到结果。


![](/assets/images/blog/bestof4/testOutputVS.png)
{: .img-responsive}

就是这样！你现在是一位测试大师了。我们希望你喜欢阅读这篇文章，并期待下个月的博客文章，届时我们将从实践角度探讨如何在 Wurst 中构建视觉上华丽的法术。