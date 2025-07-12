---
title: 错误处理
sections:
- 简介
- 重复的错误
- 自定义错误
---

### 简介
Wurst 能够将栈轨迹（即函数调用的顺序记录）注入到你的代码中。这意味着当错误（Error）发生时，你可以看到其发生前后代码的运行轨迹。这些错误通常在 Wurst 内部出现问题时发生，例如内存耗尽或空指针解引用。
栈轨迹由一个运行时参数 `-stacktraces` 控制，该选项默认开启。


![](/assets/images/blog/bestof9/stacktrace.png)
{: .img-responsive}

### 重复的错误
`ErrorHandling` 中唯一的额外逻辑是防止重复的错误信息刷屏。错误和其栈轨迹只会被显示一次，后续重复的错误将在 `MUTE_ERROR_DURATION` 秒（默认为 60 秒）内被忽略。

### 自定义错误
抛出错误并非编译器的专属功能。实际上，编译器生成的错误最终会调用标准库中的 `error` 函数。
你也可以在自己的代码中调用 `error` 函数并附上适当的信息，从而抛出自定义错误。

```wurst
public function myAPI(unit target)
	if target == null
		error("Target may not be null")
```