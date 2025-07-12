---

title: 代码热更新(1.31)
excerpt: 学习如何通过JHCR在运行的地图上更新代码
date: 2019-03-29
icon:
  type: fa
  name: fa-share-square
color: green
author: peq
layout: tutorialdoc
---

# JHCR(Jass Hot Code Reload)

> **目前只支持1.31和1.32。**

JHCR原理并不是Memory Hack，而是把jass翻译成字节码
通过地图脚本并注入运行时间并转换地图脚本，使运行时间能够与它配合使用
原作者博客解释了运行原理：[jhcr，一个高级概述](https://lep.duckdns.org/blog/2020-09-25-jhcr-a-high-level-overview.html)

Wurst 完全支持在地图运行时通过 JHCR 重载代码
这使你能够快速迭代，因为每次代码更改后都无需从头开始测试地图。

## 安装方法

如果你尚未安装 Wurst 和 Visual Studio Code，应首先查看[安装指南](/start)。

然后，设置 JHCR：

1. [下载 JHCR](https://www.hiveworkshop.com/threads/jass-hot-code-reload.313811/) 并将其解压到你机器上的一个文件夹中。
2. 打开 Visual Studio Code 设置，并将 `wurst.jhcrExe` 条目更改为你在步骤 1 中解压的 jhcr 可执行文件。例如 `C:\apps\jhcr.exe`。（如果将 jhcr.exe 添加到系统路径中，则此步骤不是必需的）
3. （不确定是否必要）通过更改注册表来启用 `Allow Local Files`。
4. （可选）更改 Visual Studio Code 设置 `wurst.customMapDataPath`，使其指向 Warcraft 存储自定义地图数据的路径。
    这通常是类似 `C:\Users\YourName\Documents\Warcraft III\CustomMapData` 或 `C:\Users\YourName\OneDrive\Dokumente\Warcraft III\CustomMapData` 的路径。此路径将传递给 Jass Hot Code Reload (JHCR) 的 `--preload-path` 选项。
    如果你不指定此路径，Wurst 将尝试自动查找正确的文件夹。

## 用法

要使用代码热重载，只需使用 `wurst.hotstartmap` 命令运行你的地图：

 - 按 `F1`
 - 搜索 `wurst: Run a Wurst map with Jass Hot Code Reload (JHCR)` 并按 `Enter`
 - 选择要运行的地图并按 `Enter`

Warcraft 启动后，你可以编辑代码。当你想将更改加载到正在运行的 WC3 实例中时，只需运行 `wurst.hotreload` 命令：

 - 按 `F1`
 - 搜索 `wurst: Reload code for a Wurst map started with Jass Hot Code Reload (JHCR)` 并按 `Enter`
 - 等待编译完成
 - 切换回游戏并按 `ESC` 键触发重载。

## 更先进的用法

如果你想经常使用这些命令，可以在 Visual Studio Code 中为它们分配键盘快捷键。