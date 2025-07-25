---
title: 安装Java
sections:
  - 下载JRE
  - 验证/更新Java
---

> WurstScript以及其相关工具需要Java8来正常运行。
> 编者注：装java的教程有很多，这部分不会可以百度。

### 下载 JRE

[*&nbsp;*{: .fa .fa-download} 下载 Java](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html){: .btn .btn-orange} 
![](/assets/images/setup/java_powered.png)
 

下载适合你系统的Java，并跟随下方的安装步骤来安装。

一旦完成后，就可进入下一步。

- 注意你应当确保安装的Java至少为 `1.8.0_XXX` 版本，而 `XXX` 部分至少应当大于 **101** ，以此获得最佳的 WurstScript 使用环境。

### 验证/更新Java

WurstScript的许多功能依赖于电脑上配置的java环境变量。


如果你的 **JAVA_HOME** 环境变量还没有设置，或者设置错了路径。

参考 [*&nbsp;*{: .fa .fa-external-link-square} 这些教程](https://www.cnblogs.com/silence9527/p/7358239.html) 来正确设置你的 **JAVA_HOME** 环境变量。

按 `Win+R` ，输入 `cmd` ，按下 `回车` 后，开启命令行窗口。
随后输入 `java -version` 来验证你当前安装的JAVA版本。

如果你的Java正确安装了，就应该能够看到如下的消息：


![](/assets/images/setup/JavaVerify.png)
{: .img-responsive}

如果以上命令出现了错误，你可以尝试输入 `java` 或者 `javac` 命令来测试环境变量是否成功配置。

当你正确安装完Java后，接着安装VSCode。