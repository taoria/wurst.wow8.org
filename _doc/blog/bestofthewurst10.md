---
title: Wurst 精选 10
layout: post
excerpt: 春季大更新 & 420
icon:
  type: fa
  name: fa-servicestack
color: blue
author: Frotty & Coke
date: 2019-04-20
banner: /assets/images/blog/bestof10/wurstbanner10.png
---
------

Wurst 精选 10
===

![](/assets/images/blog/bestof10/wurstbanner10.png)
{: .img-responsive}

在三月和四月初，我们推出了先前宣布的 `wurst.build` 配置文件改进，允许进一步基于代码的地图元数据自定义，并为我们设想的未来 wurst 独立地图生成管道奠定了更多基础。

现在，您可以通过我们新的 Grill 包管理器从命令行充分利用该工具套件。此外，标准库迎来了 `DamageEvent`：一个新的伤害检测和修改 API，它支持并使用了新的 1.29 原生函数。

工具
---
*   对 `wurst.build` 配置工作流进行了升级。您现在可以修改场景数据，如玩家、队伍和加载屏幕，所有这些都无需打开世界编辑器。更多信息请参阅[教程](https://wurstlang.org/tutorials/wurstbuild.html)。
*   WurstSetup 进行了界面更新，包括自动更新、更好的异步任务以及为高级用户提供的完整 CLI 功能。
*   `compiletime` 表达式内的对象和其他分配现在会在运行时保留以供使用。
*   更新了 `switch` 语句，支持合并 case、重复 case 检测和控制流改进。
*   实验性支持 @lep 的 [“热代码重载”](https://wurstlang.org/tutorials/jhcr.html)，该功能允许在地图运行时修改代码。

标准库
---

我们合并了十几个拉取请求，并修复了大量错误，这要感谢众多贡献者，我们对此感到非常兴奋。主要亮点包括：

*   添加了用于伤害检测和修改的新包 `DamageEvent`，并弃用了旧的 `DamageType` 包。
*   用于 `ClosureTimer` 执行的计时器现在可以自定义，从而可以创建计时器对话框。
*   包 `LastOrder` 已被清理和修复。
*   包 `Printing` 现在可以完全配置。
*   包 `ClosureEvents` 现在仅在使用了鼠标事件时才会监听它们。
*   包 `OrderStringFactory` 不再生成默认的单位命令字符串，以便在事件中可以区分它们。
*   将 `damageTarget` 和 `isAliveTrick` 从 unit 推广到 widget。
*   为 `flashEffect` 添加了额外的重载。
*   添加了简写 `.setDummyAbility()`，使任何技能都可以由虚拟施法者施放。
*   现在可以通过虚拟施法者 API 访问 `DummyCaster` 的虚拟单位。
*   修复了 UnitIndexer 中的嵌套索引/取消索引问题。
*   修复了计数回调在某些情况下无法正常停止的问题。
*   修复了 `image.hideForPlayer`，并改进了图像文档。
*   修复了 `TimedLoop` 实例在停止循环后执行不正确的问题。

网站
---
*   添加了更多并改进了现有的标准库[文档](https://wurstlang.org/stdlib)页面。
*   我们最近还推出了一个新的[关于闭包的教程](https://wurstlang.org/tutorials/getclose.html) - 一定要去看看。
*   教程、标准库和博客文章的网页界面面包屑导航现在包含所有父级。
*   我们的域名现已在 GitHub 上得到验证。


焦点
===


![](/assets/images/blog/bestof10/grillpm.jpg)
{: .img-responsive}

## 简介

如果您已经使用 Wurst 一段时间，您很可能已经使用过 Wurst Setup 工具来安装 WurstScript、生成项目和更新依赖项。我们首先从图形化解决方案开始，以便每个人都能轻松安装和使用 wurst。现在 Setup 工具已经成熟，我们正在为更高级的终端用户改进命令行界面。

请注意，所有这些改进都是在现有设置的基础上添加的，如果您不想使用它们，也完全可以！这里唯一的破坏性变更是新的命令行参数语法，它现在是统一的，并且与其他包管理器更加一致。不带任何参数运行 grill 将像以前一样打开 GUI (Wurst Setup) 前端。传递任何参数都将假定为 CLI 用法。

## Grill 包装器

由于所有新增功能都保留在 setup 工具中，我们自豪地推出 `grill` 包装器，以便在任何系统上轻松使用 setup 工具的 CLI。

### 如何获取？

只需使用 setup 工具的 UI 来安装或更新您的 wurstscript 安装。所有新功能都已包含在内。

为了在系统范围内的 shell 中正确使用 `grill` 命令，您需要将 Wurst 安装文件夹 (~/.wurst) 添加到您的环境 PATH 变量中。具体操作方法因平台而异 - 请参阅此[教程](https://www.java.com/en/download/help/path.xml)。在 Windows 10 上，最终结果应如下所示：

![](https://i.imgur.com/8bFJJVT.jpg)
{: .img-responsive}

现在您可以使用 grill 命令了。表情符号的支持取决于您使用的特定终端。


![](https://i.imgur.com/FNSBYgD.jpg)
{: .img-responsive}

### 无头执行

拥有功能齐全的命令行界面的主要好处是，您可以在没有显示器（无头）的系统上运行它。这使我们能够轻松地自动化 wurst 地图文件的构建过程 - 例如在持续集成中，如下所述。

向 `grill` 传递任何命令行参数可确保不会有用户界面提示用户。

### 特殊的 wurstscript 包

使用 `grill install wurstscript` 来安装或更新现有的 WurstScript 工具。安装将与 setup 工具相同，位于您的用户主文件夹 `~/.wurst` 中。

### 生成新项目

`grill generate <project_name>` 将创建一个以您选择的名称命名的新目录，并在其中生成一个新的 wurst 项目。生成的内容包括默认标准库依赖项的最新副本以及基本的 vscode 项目配置元数据。

### 导入现有项目

与安装 WurstScript 或生成项目不同，以下命令应在您项目的根文件夹中运行。

如果您有一个现有项目，或者想为他人的项目做贡献，您只需在克隆/复制后安装一次项目即可。这将获取项目所需的所有依赖项的本地副本。

```
$ git clone <git_repo_url>
$ cd <repo_name>
$ grill install
```

### 包管理

`grill` 不是一个功能完备的包管理器，因为它不解析传递性依赖。它主要是为您拉取一个 git 仓库列表，并为 wurst 编译配置它们。很快，我们将添加对分支或提交 ID 依赖的支持。以下命令必须在项目的根文件夹内执行。

安装新的 git 依赖：
`grill install <git_repo_url>`

移除 git 依赖：
`grill remove <git_repo_url>`

更新现有依赖：
`grill install`

### 持续集成

简而言之，CI 是在每次版本更改（提交）时自动化构建和测试项目的过程。流行的 git 主机（如 bitbucket、gitlab 和 github）使我们能够将 CI 用于我们的 wurst 项目。可以说，最简单的方法是使用 docker-wurst 镜像。

#### Wurst Docker 镜像

Docker 提供了将应用程序和系统容器化为镜像的机制，这些镜像的运行方式类似于虚拟机。该虚拟机预装并预配置了所有工具和依赖项，因此您可以立即从命令行使用 `grill`。

```
$ docker run --rm -it frotty/wurstscript /bin/sh -c "grill help"
```

[Travis CI 示例](https://github.com/wurstscript/WurstStdlib2/blob/master/.travis.yml)

[Circle CI 示例](https://github.com/island-troll-tribes/island-troll-tribes/blob/master/.circleci/config.yml)

就是这样。我们希望您喜欢 BotW 系列的第十篇文章。