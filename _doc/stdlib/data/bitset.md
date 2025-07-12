---
title: 位集(bitset)
sections:
- 导入
- 使用
---

### 导入

位集（bitset）是将一组较小的整数表示为一个较大的整数。让我们回顾一下整数是如何用二进制表示的：


![](/assets/images/stdlib/bitset.png)
{: .img-responsive}

上图说明了最终值是如何通过 2 的幂计算出来的。我们可以通过加上或减去特定的 2 的幂来单独操作每一位。因此，指数可以表示为一个由 32 位组成的整数中的一个位，其中 31 位是可用的，因为有一位用于表示符号（- 或 +）。这意味着我们仅用一个整数就可以保存一组 1 到 31 的值。

### 使用

位集通常不用于保存和检索实际值，而是用来保存一组布尔值。在《魔兽争霸 III》中，它们可以在二进制数据中找到，例如用于通道选项。这些通道选项在标准库中相应地表示为一个位集，并与一个枚举（enum）结合使用：

```wurst
public enum Option
	VISIBLE
	TARGETIMAGE
	PHYSICALSPELL
	UNIVERSALSPELL
	UNIQUECAST

public class ChannelAbilityPreset extends AbilityDefinitionIllidanChannel
	private bitset optionSet = bitset(0)

	function presetOption(Option opt, boolean flag)
		let pow = opt castTo int
		if optionSet.contains(pow)
			if not flag
				optionSet = optionSet.remove(pow)
		else if flag
			optionSet = optionSet.add(pow)

		presetOptions(lvl -> optionSet.val)

	function hasOption(Option option) returns boolean
		return optionSet.contains(option castTo int)
```