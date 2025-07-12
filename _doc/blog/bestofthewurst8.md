---
title: Wurst 精华 8 - 八月更新
layout: post
excerpt: 本月焦点是 VSCode 集成
icon:
  type: fa
  name: fa-servicestack
color: blue
author: Frotty
date: 2018-09-15
banner: /assets/images/blog/bestof8/wurstbanner8.png
---
------


![](/assets/images/blog/bestof8/wurstbanner8.png)
{: .img-responsive}

本月我们解决了一些旧问题，并添加了一些激动人心的新功能。本次的焦点是关于新的 *vscode 功能*。

我们还要感谢社区在过去几个月中的热心支持和贡献，帮助 Wurst 变得比以往任何时候都更好！

工具更新
---
*   已支持 1.30 补丁，并推荐其作为 Wurst 开发的版本。
*   现在使用独立的语法解析器来解析原生 Jass，这提高了对输入地图的兼容性。
*   添加了 `@inline` 和 `@noinline` 注解，以便对内联行为进行更精细的控制。
*   内联器也得到了改进，现在可以逐个调用地处理内联。
*   为 vscode 扩展添加了面包屑导航、大纲视图和符号重命名功能。
*   现在 vscode 中会显示 wurst 颜色元组和 wc3 颜色代码中使用的颜色。
*   在地图初始化期间使用的 `blizzard.j` 中的函数不再被内联，这可以防止地图脚本膨胀，尤其是在超过 12 名玩家的情况下。
*   为未使用的私有类变量和函数添加了警告。
*   修复了一个未检测到死代码的情况，该情况导致在手动置空句柄时出现重复语句。
*   错误窗口的制表符宽度已减小，因此代码不再过度缩进。
*   分析并解决了一些 CPU 和内存问题（感谢 ej-technologies 的开源 JProfiler 许可证）。

标准库亮点
---

*   FileIO 和 Network 库已更新，以支持 24 名玩家和新的原生函数 ([PR #94](https://github.com/wurstscript/WurstStdlib2/pull/94), [PR #106](https://github.com/wurstscript/WurstStdlib2/pull/106), [PR #107](https://github.com/wurstscript/WurstStdlib2/pull/107))。
*   `DamageType` ([PR #95](https://github.com/wurstscript/WurstStdlib2/pull/95), [PR #97](https://github.com/wurstscript/WurstStdlib2/pull/97)) 和 `OnUnitEnterLeave` ([PR #96](https://github.com/wurstscript/WurstStdlib2/pull/96)) 针对边缘情况进行了更多修复。
*   资源列表已通过 ([PR #98](https://github.com/wurstscript/WurstStdlib2/pull/98), [PR #103](https://github.com/wurstscript/WurstStdlib2/pull/103), [PR #114](https://github.com/wurstscript/WurstStdlib2/pull/114)) 得到改进。
*   `trigger` API 已通过 ([PR #102](https://github.com/wurstscript/WurstStdlib2/pull/102), [PR #104](https://github.com/wurstscript/WurstStdlib2/pull/104)) 进行了修复。
*   添加了 `Matrices` 和 `Quaternion` 数学包，这些包也可用于操作特效 ([PR #111](https://github.com/wurstscript/WurstStdlib2/pull/111), [PR #112](https://github.com/wurstscript/WurstStdlib2/pull/112))。
*   从标准库中移除了另一个 `initlater` 导入，从而提高了代码质量。
*   我们还有几个关于伤害修改和 FileIO 的 PR 正在审核中。

焦点：VSCode 集成
---

过去几个月中最显著的改进之一是让 Wurst 在 vscode 中的开发体验功能更丰富、集成度更高。我们实现了语言协议的更多功能，并将在此处展示。此外，Wurst 文件图标现在已成为 [vscode-icons 扩展](https://marketplace.visualstudio.com/items?itemName=robertohuertasm.vscode-icons) 的官方一部分。


![](https://i.imgur.com/AUrSWqt.png)
{: .img-responsive}

### 面包屑导航


![](https://i.imgur.com/6Mj0tIC.png)
{: .img-responsive}


![](https://i.imgur.com/fQbsyQi.png)
{: .img-responsive}

面包屑导航显示从光标位置开始的模型层次结构。
你可以通过 `View->Toggle Breadcrumbs` 来开启它。

*Focus Breadcrumbs* 命令（快捷键 `Ctrl` +`Shift`+`.`）可以让你快速导航代码。
你还可以使用 *Goto symbol in workspace* 命令（`Ctrl` + `t`）或 *Goto symbol in file*（`Ctrl`+`Shift`+`o`）来搜索符号。

### 大纲视图


![](https://i.imgur.com/M0S88xJ.png)
{: .img-responsive}

大纲视图为你提供了文件中所有元素的可搜索概览。在资源管理器标题上右键单击即可开启。


![](https://i.imgur.com/an0TDoP.png)
{: .img-responsive}

### 符号重命名

VSCode 能够识别 Wurst 的符号，从而实现一致的重命名。


![](https://i.imgur.com/93MPgE3.gif)
{: .img-responsive}

### 颜色预览


![](https://i.imgur.com/mpQ0jmd.png)
{: .img-responsive}


![](https://i.imgur.com/uCIl2T3.gif)
{: .img-responsive}

Wurst 颜色元组和 wc3 颜色字符串现在可以预览，并且你可以使用 vscode 的颜色选择器来调整它们。

### 导入快速修复


![](https://i.imgur.com/wHPvvrn.gif)
{: .img-responsive}

当使用尚未导入到给定包中的类型时，Wurst 会建议导入一个可以满足该类型的包。如果应用此修复，它将自动添加导入语句。

### 主题化

虽然这不是 Wurst 的功能，但值得注意的是，你在这里看到的所有与样式相关的内容都可以通过某种方式进行自定义。有默认主题和用户创建的主题，看一看：


![](https://i.imgur.com/xYnqc7L.png)
{: .img-responsive}
![](https://i.imgur.com/T1Icm60.png)
{: .img-responsive}
![](https://i.imgur.com/HVT0A5m.png)
{: .img-responsive}
![](https://i.imgur.com/I0wWnCT.png)
{: .img-responsive}
![](https://i.imgur.com/kpUzuSc.png)
{: .img-responsive}

以上截图使用的主题名为“Atom One Dark”

祝好！

-Frotty