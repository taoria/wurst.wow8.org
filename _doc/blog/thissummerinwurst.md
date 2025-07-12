---
title: Wurst 今夏纪事
layout: post
excerpt: Wurst 在今年夏天的所有变更与更新摘要。
icon:
  type: fa
  name: fa-sun-o
color: orange
author: Cokemonkey11
date: 2017-11-15
banner: /assets/images/blog/bestof1/ThisSummerInWurstBanner.png
---
------

![](/assets/images/blog/bestof1/ThisSummerInWurstBanner.png)
{: .img-responsive}

大家好！这是关于 Wurst 和 WurstScript 社区的系列新闻帖的第一期。距离我们上次为 Hive Workshop 新闻总结 Wurst 相关内容已经过去了好几年，所以请注意，这只是一个非常简略的摘要，而我们未来的新闻帖将更密集地讨论时事。

关于 Wurst 的背景：WurstScript 是一种可编译为 JASS 的编程语言，它与一套工具一同开发，旨在提供连贯的开发体验。它甚至可以与用 vJass 编写的地图协同工作。一个典型的工作流程如下：

*   在世界编辑器（原版编辑器、WEX 或任何其他编辑器）中设计地形
*   定义物体，可以使用物体编辑器，也可以使用 Wurst 的“物体编辑”子系统
*   编写脚本，获得即时反馈，并在轻量级的 [vscode](https://code.visualstudio.com/) 编辑器中编译和构建
*   将你的代码签入版本控制系统

与 vJass 不同，WurstScript 是一种具有严格类型系统和强大优化器的高级编程语言。其结果通常是能以更快的速度编写更短的代码，而性能上没有任何牺牲。

### 那么，最近有什么变化呢？

WurstScript 社区一直保持着稳定的活跃度，不仅在 WurstScript 语言本身，而且在整个地图编辑工具链上都有频繁而定期的变化。

WurstScript 已经找到了自己的定位，即成为一个旨在最大化开发体验的生产力和一致性，同时不牺牲质量的工具链。

WurstScript 的维护者通常首先是程序员，其次才是地图制作者。遗憾的是，时间限制意味着我们没有花太多时间向更广泛的模组社区宣传我们的进展。我们渴望改变这一点，将更频繁地发布精心策划的摘要，介绍我们圈子里的动态。

在此背景下，我们希望这将是众多“Wurst 精选”回顾帖中的第一篇，我们将在其中重点介绍重大变化——当然频率会比三年一次高得多。变化多得数不清，所以在这篇文章中，我们将讨论一些今年夏天我们感到兴奋的变化。


# 用于编译器更新和地图项目设置的 GUI 工具


![](/assets/images/blog/bestof1/SetupToolUI.png)
{: .img-responsive}

今年夏天最重要的进展可能是一个新工具，用于维护你的 Wurst 安装、使用模板引导新地图项目以及导入现有项目。

该工具维护一个 Wurst 编译器的全局实例，自动更新它，并能以对版本控制仓库和 VSCode 插件友好的方式生成和更新项目。

这里的关键点是，我们对 WurstScript 项目结构进行了一些重构，使 Wurst 能与 VSCode 更好、更一致地协同工作。随着依赖管理和模板项目的加入，我们预见随着项目模板的进步，生产力将取得巨大飞跃。


# 新的 runmap/run-arguments 设计，以及只读地形地图文件

伴随着 [JMPQ](https://github.com/inwc3/JMPQ3) 库的巨大改进，Wurst 现在以只读归档文件的形式访问你的地图项目。编译器不再像传统方式那样覆盖它，而是将构建好的地图文件输出到一个单独的“_build”目录。这带来了巨大的好处，包括：

*   地图文件可以更安全地进行版本控制，因为 Wurst 编译器以只读方式访问它们。
*   在极端情况下，结合原生物体编辑，你可以端到端地维护你的地图，仅将签入的地图文件用于地形制作。
*   更好地与现有的 JASS/vJass 地图集成，因为 Wurst 可以重用现有的地图脚本而无需修改源文件。
*   默认情况下，Wurst 地图的构建和编译现在在 VSCode 中独立完成——因此无需使用自定义编辑器（除了地形制作），甚至根本不需要安装魔兽争霸3。

## `wurst.build` 中的地图元数据


![](/assets/images/blog/bestof1/coolGraph.png)
{: .img-responsive}

Wurst 地图项目的整体结构已经改变，现在包含两个新的元数据文件：`wurst.build` 和 `wurst_run.args`——前者提供构建产物，后者用于向 Wurst 编译器传递参数。

使用 `wurst.build` 文件的一个优点是，可以在不同构建之间保持一致性——依赖项被隔离，维护库代码也变得更加模块化和方便。

另一个优点是，不再需要世界编辑器来处理某些元数据，如地图名称、描述、作者等——这些元数据从构建文件中获取并注入到最终的地图中。

设置工具也通过导入 `wurst.build` 文件与此元数据交互，从而更新项目的依赖项，以保持标准库和其他使用的包为最新状态。


# 新的文档和网站，位于新地址（2017年12月30日更新）

WurstScript 的文档和教程现在位于 [wurstlang.org](https://wurstlang.org)——我们相信这个新的登陆页面为使用 Wurst 提供了更一致、更有吸引力的体验。我们鼓励社区编写教程，而[面向 vJass 用户的 Wurst 介绍](https://wurstlang.org/tutorials/wurst_for_vjass_users.html)就是这样一篇教程。

也请在这里查看上面提到的 Wurst 设置工具！


# ……以及更多

作为过去几年的快速回顾，还有更多内容：

*   VSCode 插件和工作流，取代了已弃用的 Eclipse 插件和工作流。
*   新的 Github 组织——重构工作以拆分仓库；现在使用 Gradle 进行依赖管理。
*   Wurst 标准库 v2 现已激活并成为事实上的标准。
*   提升了 Jurst（vJass 友好的 Wurst 方言）中的 JASS 合规性。
*   编译器性能和内存使用得到改善。

我们希望您会觉得这份摘要内容丰富。我们计划将来更频繁地撰写此类精选内容，请务必给我们一些反馈。

像往常一样，我们在 [#inwc.de-maps](https://kiwiirc.com/client/irc.quakenet.org/#inwc.de-maps) IRC 频道讨论所有与 Wurst 相关的事情。