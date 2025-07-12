---
title: 高级概念
sections:
- 枚举
- 元组类型
- 拓展函数
- 不定参函数
- Lambdas和闭包
- 函数重载
- 操作符重载
- 注解
- 编译时执行
- 自动化单元测试
---

## 枚举

在 Wurst 中，__枚举 (Enums)__ 是一组命名整数常量的简写包装器。
枚举与类无关，并直接转换为它们所代表的整数。
其主要目的是在通常会使用整数的地方添加安全、舒适的 API。

用法类似于通过枚举名称访问静态类成员：

```wurst
enum MyUnitState
	FLYING
	GROUND
	WATER

let currentState = MyUnitState.GROUND
```

枚举可以用作类成员

```wurst
class C
	State currentState

	construct(State state)
		currentState = state
```

要检查枚举的当前值，你可以使用 switch 语句。
请注意，必须检查所有枚举成员（或使用 default）。

```wurst
let currentState = MyUnitState.GROUND
switch currentState
	case FLYING
		print("flying")
	case GROUND
		print("ground")
	case WATER
		print("water")
```
请注意，在 switch 语句和变量赋值中，限定符 `MyUnitState` 可以省略。

要获取枚举成员所代表的整数，只需将其转换为 int 类型：

```wurst
print((MyUnitState.GROUND castTo int).toString()) // 将打印 "1"
```

合并的整数值从 0 开始，每个后续的枚举成员递增。因此，对于 `MyUnitState`，`FLYING` 将是 0，`GROUND` 是 1，`WATER` 是 2。


## 元组类型

通过*元组*类型，你可以将多个变量组合成一个包。这可以用于从函数返回多个值、创建自定义类型，当然也可以提高可读性。

请注意，元组不像类。它们有一些重要的区别：
- 你不需要销毁元组值。
- 当你将元组赋给另一个变量或将其传递给函数时，你会创建该元组的副本。对此副本的更改不会影响原始元组。
- 元组类型不能绑定到类型参数，因此如果 vec 是元组类型，你就不能拥有 `List{vec}`。
- 由于元组类型不是在堆上创建的，与使用单个变量相比，没有性能开销。

```wurst
// Example 1:

// define a tuple
tuple vec(real x, real y, real z)

init
	// create a new tuple value
	let v = vec(1, 2, 3)
	// change parts of the tuple
	v.x = 4
	// create a copy of v and call it u
	let u = v
	u.y = 5
	if v.x == 4 and v.y == 2 and u.y == 5
		testSuccess()


// Example 2:

tuple pair(real x, real y)
init
	var p = pair(1, 2)
	// swap the values of p.x and p.y
	p = pair(p.y, p.x)
	if p.x == 2 and p.y == 1
		testSuccess()
```

因为元组本身没有任何函数，你可以为现有的元组类型添加扩展函数，以实现类似类的功能。
请记住，你不能在元组的扩展函数中修改其值——因此，如果你想更改某些内容，每次都必须返回一个新的元组。
可以查看标准库中的 Vector 包以获取一些元组使用示例。(Math/Vectors.wurst)



## 拓展函数

扩展函数使你能够向现有类型“添加”函数，而无需创建新的派生类型、重新编译或以其他方式修改原始类型。
扩展函数是一种特殊的静态函数，但它们的调用方式就像它们是扩展类型的实例函数一样。

### 声明
```wurst
public function TYPE.EXTFUNCTIONNAME(PARAMETERS) returns ...
	BODY
	// The keyword "this" inside the body refers to the instance of the extended type
```
### 示例

```wurst
// Declaration
public function unit.getX() returns real
	return GetUnitX(this)

// Works with any type
public function real.half() returns real
	return this/2

// Parameters
public function int.add( int value )
	return this + value

// Usage
let u = CreateUnit(...)
...
print(u.getX().half())

// Also classes, e.g. setter and getter for private vars
public function BlubClass.getPrivateMember() returns real
	return this.privateMember

// And tuples as mentioned above
public function vec2.lengthSquared returns real
	return this.x*this.x+this.y*this.y
```

## 不定参函数

可变参数函数可以传递可变数量的同类型参数。它们最常用于防止样板代码并提供更好的 API。
在函数内部，可以通过 `for .. in` 循环访问可变参数。

示例：
```wurst
function asNumberList(vararg int numbers) returns LinkedList<int>
	let list = new LinkedList<int>()
	for number in numbers
		list.add(number)
	return list

init
	asNumberList(1, 2, 3)
	asNumberList(44, 14, 651, 23)
	asNumberList(10)
```

在此示例中，所有对 `asNumberList` 的调用都是有效的，其好处显而易见。我们可以向函数传递任意数量的整数（最多 31 个参数），但我们只需要实现一次。

### 限制

当前的实现会创建一个具有正确参数数量的专用函数。
由于 Jass 最多允许 31 个参数，因此函数调用总共不能使用超过 31 个参数。


## Lambdas和闭包

Lambda 表达式（也称为匿名函数）是一种轻量级的方式，用于提供函数式接口或抽象类的实现（为简化文本，以下解释均指接口，但抽象类可以以相同的方式使用）。

*函数式接口*是只有一个方法的接口。
这是一个例子：
```wurst
// the functional interface:
interface Predicate<T>
	function isTrueFor(T t) returns bool

// a simple implementation
class IsEven implements Predicate<int>
	function isTrueFor(int x) returns bool
		return x mod 2 == 0

// and then we can use it like so:
let x = 3
Predicate<int> pred = new IsEven()
if pred.isTrueFor(x)
	print("x is even")
else
	print("x is odd")
destroy pred
```

使用 lambda 表达式时，无需定义一个实现该函数式接口的新类。相反，该函数式接口的唯一函数可以在其使用的地方实现，如下所示：
```wurst
let x = 3
// Predicate is defined here:
Predicate<int> pred = (int x) -> x mod 2 == 0
if pred.isTrueFor(x)
	print("x is even")
else
	print("x is odd")
destroy pred
```
重要的部分是：
```wurst
(int x) -> x mod 2 == 0
```
这是一个 lambda 表达式。它由两部分和一个箭头符号 *->* 组成。箭头的左侧是形式参数列表，就像你从函数定义中了解的那样。右侧是一个表达式，即实现部分。实现通常只包含单个表达式，因为 lambda 表达式通常很小并且在一行中使用。但如果一个表达式不够，可以使用 begin-end 表达式。

请记住，因为闭包就像普通对象一样，你也必须像销毁普通对象一样销毁它们。你还可以对它们执行所有可以对其他对象执行的操作，比如将它们放入列表或表中。

### 类型推断

如果可以从上下文中推断出 Lambda 参数的类型，则无需提供它们。此外，当只有一个无类型参数或没有参数时，括号是可选的。

因此，以下定义是等效的：

```wurst
Predicate<int> pred = (int x) -> x mod 2 == 0
Predicate<int> pred = (x) -> x mod 2 == 0
Predicate<int> pred = x -> x mod 2 == 0
```

### begin-end 表达式

有时一个表达式对于闭包来说是不够的。在这种情况下，可以使用 begin-end 表达式。它允许在表达式内部包含语句。begin 关键字后必须跟一个换行符和增加的缩进。可以在其中包含多行语句：
```wurst
doAfter(10.0, () -> begin
	u.kill()
	createNiceExplosion()
	doMoreStuff()
end)
```
在 begin-end 表达式内部也可以有 return 语句，但只有最后一条语句可以是 return。

### Lambda 块

通常，带有 begin-end 块的 lambda 表达式作为一行中的最后一个参数。Wurst 为这种情况提供了一种特殊的语法，它更符合 Wurst 中使用的基于缩进的通用语法。

lambda 表达式可以在赋值、局部变量定义、返回语句或函数调用之后使用。lambda 表达式的箭头 `->` 后面必须跟一个换行符和一个缩进的语句块。

例如，上面的 begin-end 块可以替换如下：

```wurst
doAfter(10.0) ->
	u.kill()
	createNiceExplosion()
	doMoreStuff()
```

以下示例使用带参数 `u` 的 lambda 遍历玩家拥有的所有单位：

```wurst
forUnitsOfPlayer(lastRinger) u ->
	if u.getTypeId() == TOWER_ID
		let pos = u.getPos()
		let facing = u.getFacingAngle()
		addEffect(UI.goldCredit, pos).destr()
		u.remove()
		createUnit(players[8], DUMMY_ID, pos, facing)
```

### 变量捕获


lambda 表达式真正酷的特性是它们会创建一个*闭包*。
这意味着它们可以封闭其作用域之外的局部变量并捕获它们。
这是一个非常简单的例子：
```wurst
let min = 10
let max = 50
// remove all elements not between min and max:
myList.removeWhen((int x) ->  x < min or x > max)
```
在这个例子中，lambda 表达式捕获了局部变量 min 和 max。

重要的是要知道，变量是按值捕获的。当创建闭包时，值被复制到闭包中，闭包只在该副本上工作。变量仍然可以在环境或闭包中更改，但这不会对变量的另一个副本产生任何影响。

当变量在闭包创建后被更改时，可以观察到这一点：
```wurst
var s = "Hello!"
let f = () ->
	print(s)
	s = s + "!"

s = "Bye!"
f.run()  // will print "Hello!"
f.run()  // will print "Hello!!"
print(s) // will print "Bye!"
```

### 幕后原理

编译器会为代码中的每个 lambda 表达式创建一个新类。
这个类实现了由 lambda 表达式使用上下文给出的接口。
生成的类为所有捕获的局部变量设有字段。
每当 lambda 表达式被求值时，就会创建该类的一个新对象并设置其字段。

所以上面的“Hello!”示例大致等同于以下代码：
```wurst
// (the interface was not shown in the above code, but it is the same):
interface CallbackFunc
	function run()

// compiler creates this closure class implementing the interface:
class Closure implements CallbackFunc
	// a field for each captured variable:
	string s

	function run()
		// body of the lambda expression == body of the function
		print(s)
		s = s + "!"

var s = "Hello!"
let f = new Closure()
// captured fields are set
f.s = s
s = "Bye!"
f.run()  // will print "Hello!"
f.run()  // will print "Hello!!"
print(s) // will print "Bye!"
```
### 函数类型

lambda 表达式有一种特殊的类型，它捕获参数的类型和返回类型。这种类型称为*函数类型*。以下是一些示例及其类型：
```wurst
() -> 1
	// type: () -> integer

(real r) -> 2 * r
	// type: (real) -> real

(int x, string s) -> s + I2S(x)
	// type: (int, string) -> string
```

虽然函数类型是类型系统的一部分，但 Wurst 没有办法写下函数类型。
没有类型为 `(int,string) -> string` 的变量。
因此，lambda 表达式只能在已知具体接口或类类型的地方使用。
这可以是一个赋值语句，其中变量的类型是给定的。
```wurst
Predicate<int> pred = (int x) -> x mod 2 == 0
```
但是，如果变量的类型只是被推断出来，则无法使用 lambda 表达式：
```wurst
// will not compile, error "Could not get super class for closure"
let pred = (int x) -> x mod 2 == 0
```
### 作为 code 类型的 Lambda 表达式

Lambda 表达式也可以在需要 `code` 类型表达式的地方使用。
这样做的前提是，lambda 表达式没有任何参数，也不捕获任何变量。例如，以下代码是*无效*的，因为它捕获了局部变量 `x`。

```wurst
let t = getTimer()
let x = 3
t.start(3.0, () -> doSomething(x)) // error: Cannot capture local variable 'x'
```

这可以通过手动将数据附加到计时器来解决：
```wurst
let t = getTimer()
let x = 3
t.setData(x)
t.start(3.0, () -> doSomething(GetExpiredTimer().getData()))
```
如果 lambda 表达式用作 `code`，则不会创建新对象，因此也没有需要销毁的对象。lambda 表达式将被直接翻译成一个普通的 Jass 函数，因此以这种方式使用 lambda 表达式没有性能开销。

## 函数重载

函数重载允许你拥有多个同名函数。
编译器将根据参数的静态类型来决定调用哪个函数。

Wurst 使用一种非常简单的重载形式。如果在作用域中只有一个函数对于给定的参数是可行的，那么将使用该函数。
如果存在多个可行的函数，编译器将报错。

请注意，这与许多其他语言（如 Java）不同，在这些语言中，会选择具有最具体可行类型的函数，而不是报错。
```wurst
function unit.setPosition(vec2 pos)
	...

function unit.setPosition(vec3 pos)
	...

function real.add(real r)
	...

function real.add(real r1, real r2)
	...
```
这是可行的，因为参数类型不同或参数数量不同，因此可以在编译时确定正确的函数。
```wurst
function real.add(real r1)
	...

function real.add(real r1) returns real
```
这是不可行的，因为只有返回类型不同，无法确定正确的函数。

```wurst
class A
class B extends A

function foo(A c)
	...

function foo(B c)
	...

// somewhere else:
	foo(new B)
```

这也不可行，因为 B 是 A 的子类型。如果你用 B 类型的值调用函数 foo，两个函数都将是可行的。其他语言会选择“最具体的类型”，但 Wurst 不允许这样做。如果 A 和 B 是不可比较的类型，则允许重载。


## 操作符重载

操作符重载允许你为自定义参数更改内部操作符 +、-、* 和 / 的行为。
一个来自标准库的快速示例 (Vectors.wurst)：
```wurst
// Defining the "+" operator for the tupletype vec3
public function vec3.op_plus( vec3 v ) returns vec3
	return vec3(this.x + v.x, this.y + v.y, this.z + v.z)

// Usage example
vec3 a = vec3(1., 1., 1.)
vec3 b = vec3(1., 1., 1.)
// Without Operator Overloading (the add function was replaced by it)
vec3 c = a.add(b)
// With operator Overloading
vec3 c = a + b
```
你可以通过扩展函数为现有类型重载操作符，或通过类函数为特定类类型重载。
为了定义一个重载函数，它必须按以下方式命名：
```wurst
+  "op_plus"
-  "op_minus"
*  "op_mult"
/  "op_divReal"
```

## 注解

Wurst 中的几乎任何定义都可以用一个或多个可选的命名注解进行标注。
注解是仅在编译时存在的元数据，可用于编译时函数、测试和 `callFunctionsWithAnnotation`。
在大多数情况下，除非你自己使用，否则注解通常会被忽略。
从 build#1124 开始，注解必须使用 `@annotation` 定义为顶级的无函数体函数才有效。

```wurst
@annotation public function my_annotation() // Definition

@my_annotation function someOtherFunc() // Usage
```

Wurst 的保留注解在 `Annotations` 包中定义。

```wurst
@annotation public function compiletime()
@annotation public function deprecated()
@annotation public function deprecated(string _message)
@annotation public function compiletimenative()
@annotation public function configurable()
@annotation public function inline()
@annotation public function config()
@annotation public function extern()
```

## 编译时执行

Wurst 包含一个解释器，可以在编译时执行代码，这对于测试和对象编辑非常有用。

### 编译时函数

编译时函数是在编译你的脚本/地图时执行的函数。
它们主要提供了通过代码创建对象编辑器对象的能力。

编译时函数只是一个用 @compiletime 注解的普通 Wurst 函数。
```wurst
@compiletime function foo()
```
编译时函数没有参数，也没有返回值。

编译时函数默认运行。你可以使用命令行参数 `-runcompiletimefunctions` 和 `-injectobjects` 来更改此行为。
当你使用编译时函数生成对象时，Wurst 会在你的地图旁边生成对象文件，你可以使用对象编辑器的常规导入功能将它们导入到你的地图中。与 ObjectMerger 相比，这样做的好处是你可以直接在对象编辑器中看到你的新对象。
你还可以启用一个选项，将对象直接注入到地图文件中，不过这些更改不会直接在对象编辑器中显示。

你可以在运行时和编译时使用相同的代码。
特殊常量 `compiletime` 可用于区分两者。
当函数在编译时调用时，该常量为 `true`，否则为 `false`。
以下示例展示了这可能如何有用：
```wurst
init
	doInit()

@compiletime
function doInit()
	for i = 1 to 100
		if compiletime
			// create item object
		else
			// place item on map
```

### 编译时表达式

与编译时函数类似，Wurst 也有编译时表达式。
顾名思义，这些是在编译时求值的表达式。
执行表达式的结果会取代原始表达式，被放入地图脚本中。

编译时表达式的语法是对标准库中 `MagicFunctions` 包内定义的 `compiletime` 函数的简单调用。
该函数接受一个参数，即要求值的表达式。

例如，以下代码定义了一个全局变量 `blub`，并用编译时表达式 `fac(5)` 对其进行初始化：

```wurst
let blub = compiletime(fac(5))

function fac(int x) returns int
    if x <= 1
        return 1
    return x * fac(x - 1)
```

阶乘函数在编译时被求值并返回 `120`。
然后，数字 `120` 会在生成的地图脚本中替换掉编译时表达式。

就像编译时函数一样，编译时表达式也可以与对象编辑原生函数一起使用（见下文）。

编译时表达式有一个限制，即如果不带 `-runcompiletimefunctions` 标志，就无法编译地图。

### 执行顺序

编译时表达式和函数在包内从上到下执行。
如果导入关系是单向的，则导入的包会在导入它的包之前执行。
否则，执行顺序未指定，并取决于实现细节。

### 编译时可用的函数

并非所有可以在游戏中使用的函数都可以在编译时使用。
只有少数函数在 Wurst 编译器中实现，并模拟了 `common.j` 中的相应函数。

当前实现的函数可以在编译器代码的 [NativeFunctionsIO](https://github.com/wurstscript/WurstScript/blob/master/de.peeeq.wurstscript/src/main/java/de/peeeq/wurstio/jassinterpreter/NativeFunctionsIO.java) 类中找到。


### 对象编辑原生函数

标准库提供了一些在编译时函数中编辑对象的函数。
你可以在标准库的 objediting 文件夹中找到相应的原生函数和更高级别的库。

ObjEditingNatives 包包含用于创建和操作对象的原生函数。如果你熟悉 Wc3 的对象格式，并了解类似 [Lua 对象生成](https://www.hiveworkshop.com/forums/jass-ai-scripts-tutorials-280/lua-object-generation-191740/) 或 JNGP 的 ObjectMerger 等工具，那么使用它们应该没有问题。如果你在启用编译时函数的情况下运行 Wurst，它将为地图中的所有对象生成对象创建代码。此代码保存在类似于 "WurstExportedObjects_w3a.wurst.txt" 的文件中，可以在你的地图文件旁边找到。如果你想使用原生函数，可以将此代码作为起点。

Wurst 还提供了更高级别的抽象。例如，AbilityObjEditing 包为 Wc3 的不同基础技能提供了许多类，并带有可读的方法名。这样你就不必查找 ID。

以下示例基于“雷霆一击”创建一个新法术。为说明目的，创建的法术 ID 为“A005”。
在正确的代码中，你应该生成你的 ID，这样就不必直接处理它们。请参阅[这篇博客文章](https://wurstlang.org/blog/bestofthewurst3.html#objectediting%20)
在下一行中，法术的名称被更改为“测试法术”。
特定等级的属性使用等级闭包进行更改，该闭包会计算所有等级的值。

```wurst
package Objects
import AbilityObjEditing

@compiletime function myThunderBolt()
	// create new spell based on thunder bolt from mountain king
	new AbilityDefinitionMountainKingThunderBolt("A005")
	// change the name
	..setName("Wurst Bolt")
	..setTooltipLearn("The Wurstinator throws a Salami at the target.")
	// 400 damage, increase by 100 every level
	..presetDamage(lvl -> 400. + lvl * 100)
	// 10 seconds cooldown
	..presetCooldown(lvl -> 10.)
	// 0 mana, because no magic is needed to master Wurst
	..presetManaCost(lvl -> 0)
	// ... and so on
```

> *注意* 所有对象类型都存在相应的包。

## 自动化单元测试

你可以将 `@Test` 注解添加到任何函数。打开任务运行器 `F1` 并输入 `run test` 以查看可用命令。你可以运行光标下的测试、包中的所有测试或所有测试。

要执行断言，你需要导入 `Wurstunit` 包。

示例：

```wurst
package Test
import Wurstunit

@Test public function a()
	12 .assertEquals(3 * 4)

@Test public function b()
	12 .assertEquals(5 + 8)
```

如果你运行此代码，将得到以下输出：

```
Running <PlayerData:114 - a>..
	OK!
Running <PlayerData:117 - b>..
	FAILED assertion:
	Test failed: Expected <13>, Actual <12>
... when calling b() in PlayerData.wurst:117
... when calling int_assertEquals(12, 13) in PlayerData.wurst:118

Tests succeeded: 1/2
>> 1 Tests have failed!
```

你可以在标准库中搜索“@Test”以获取更多示例。