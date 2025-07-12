---
title: 关于1.29支持
layout: post
excerpt: 关于1.29和Wurst的简短提醒
icon:
  type: fa
  name: fa-rss
color: green
author: Frotty
date: 2018-04-13
banner: /assets/images/empty.png
---
------
## 补丁兼容性

截至昨天，1.29兼容性更改已合并，并通过设置工具作为更新提供。
这还包含了新的本地函数和类，实例限制增加到32768。

社区成员@crojewsk已经为标准库创建了一个拉取请求，包含新本地函数的扩展函数包装器，该请求很快将被合并。

所有内容更新后，runmap应该能正常工作。

## 24名玩家

我们的wurst.build集成仍然不支持配置玩家、势力和加载屏幕选项。
这些功能将很快实现，然后可以直接访问此补丁中的所有新功能。

有关新补丁的更多信息，请[点击这里](https://www.hiveworkshop.com/threads/warcraft-iii-patch-1-29.304803/)。

## 故障排除

> __当尝试使用新本地函数时，我收到wurst错误！__

- 尝试删除`_build`文件夹中的`common.j`和`Blizzard.j`文件。然后使用runmap重新生成它们。

> __Runmap启动，但游戏窗口是黑色且有一个黑色错误对话框__

- 当游戏根目录的地图目录和用户数据地图目录中存在同名地图时，会发生此情况。尝试从游戏根目录删除`/Maps/Test/WurstRunMap.w3x`。