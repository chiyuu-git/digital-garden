---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2024-07-08-Mon, 3:04:12 pm","date-modified":"2024-07-09-Tue, 5:47:59 pm","permalink":"/programming/language/go-basic/","dgPassFrontmatter":true}
---


[飞书 - 登录](https://bytedance.larkoffice.com/minutes/obcnceycxwwex2rydwjd6b3h)

[剪C+dreamina服务端自助文档](https://bytedance.larkoffice.com/docx/InG1dVk1KohG4MxT3jGcfSRjnbZ)

Dreamia IDL https://code.byted.org/videocut-aigc/idl

Dreamina API https://code.byted.org/videocut-aigc/mweb-api // 小的打包逻辑

Dreamina RPC https://code.byted.org/videocut-aigc/content_generate // 视频生成，异步任务

Feed RPC [https://code.byted.org/faceu-server/artist-feed](https://code.byted.org/faceu-server/artist-feed) // 灵感库，EffectItem

Overpass https://overpass.bytedance.net/idl_info?s=videocut.mweb.api

IDL 仓库 -> 给上游 接入使用，只有定义没有实现

RPC 服务代码开发 -> 服务部署

环境

1. IDL 定义
2. 通过 overpass 生成 go 文件 / make generate hertztool 框架生成本地 go 文件

API -> DM RPC -> xxxx

API -> Feed RPC -> xxxx

请求流程

TNC -> 给前端下发域名 Dreamina -> DreaminaA， DreamiaB，DreaminC

TLB -> DreaminaA -> PSM（P.S.M） -> Pod（服务实例）

videocut.mweb.api -> Deprec -> Handler -> return rsp -> http response body

存储：

[Abase, Redis](https://cloud.bytedance.net/cache?x-resource-account=public) KV 存储、分布式锁、缓存

RDS 数据库 mysql

Hive 离线数据存储

Tos 文件存储 toskey/xxxxx 图片 视频 文件

平台：

[TCC 平台](https://cloud.bytedance.net/tcc)，服务端配置存储

[TCE 平台](https://cloud.bytedance.net/tce)，服务端实例部署

[Argos](https://cloud.bytedance.net/argos) 监控服务状态 QPS，时延，集群的负载， 日志查询 logid

视频云/IMAGEX 下发文件链接（域名，存储 Tos，模板）

Bytecycle CI、CD 平台 **PPE****环境（只支持本机房调度）**，上线，同步国内与海外

nepturn 流量调度，S1(A,B,C) -> S2(B,C)

Overpass 生成 RPC 调用的平台，thrift IDL 调整，提交分支 -> Overpass 生成一个仓库分支 -> go get

Faas 平台，消息订阅，异步任务等 ， 研发环境 http 服务

# GO 学习资料

[Get Started - The Go Programming Language](https://golang.google.cn/learn/)

[A Tour of Go](https://golang.google.cn/tour/welcome/1)

[GitHub - quii/learn-go-with-tests: Learn Go with test-driven development](https://github.com/quii/learn-go-with-tests)

# 导出

[Go 语言之旅](https://tour.go-zh.org/basics/3)

导出包名需要是大写的

# 函数

## 参数类型

当连续两个或多个函数的已命名形参类型相同时，除最后一个类型以外，其它都可以省略。

在本例中， x int, y int 被简写为 x, y int

```go
package main

import "fmt"

func add(x, y int) int {
	return x + y
}

func main() {
	fmt.Println(add(42, 13))
}

```

## 返回值

函数可以返回任意数量的返回值。

`swap` 函数返回了两个字符串。

```go
package main

import "fmt"

func swap(x, y string) (string, string) {
	return y, x
}

func main() {
	a, b := swap("hello", "world")
	fmt.Println(a, b)
}

```

### 带名字的返回值

Go 的返回值可被命名，它们会被视作定义在函数顶部的变量。

返回值的命名应当能反应其含义，它可以作为文档使用。

没有参数的 `return` 语句会直接返回已命名的返回值，也就是「裸」返回值。

裸返回语句应当仅用在下面这样的短函数中。在长的函数中它们会影响代码的可读性。

```go
package main

import "fmt"

func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}

func main() {
	fmt.Println(split(17))
}
```

# 声明变量

`var` 语句用于声明一系列变量。和函数的参数列表一样，类型在最后。

如例中所示，`var` 语句可以出现在包或函数的层级。

```go
package main

import "fmt"

var c, python, java bool

func main() {
	var i int
	fmt.Println(i, c, python, java)
}
```

## 变量的初始化

变量声明可以包含初始值，每个变量对应一个。

如果提供了初始值，则类型可以省略；变量会从初始值中推断出类型。

```go
package main

import "fmt"

var i, j int = 1, 2

func main() {
	var c, python, java = true, false, "no!"
	fmt.Println(i, j, c, python, java)
}
```

类型可省略, 附带类型推断的

## 短变量声明

在函数中，短赋值语句 `:=` 可在隐式确定类型的 `var` 声明中使用

函数外的每个语句都 **必须** 以关键字开始（`var`、`func` 等），因此 `:=` 结构不能在函数外使用

```go
package main

import "fmt"

func main() {
	var i, j int = 1, 2
	k := 3
	c, python, java := true, false, "no!"

	fmt.Println(i, j, k, c, python, java)
}
```

相当于

```go
package main

import "fmt"

func main() {
	var i, j int = 1, 2
	var k = 3
	var c, python, java = true, false, "no!"

	fmt.Println(i, j, k, c, python, java)
}
```

就只是省略了几个 var 关键字, 附带类型推断

```go
package main

import "fmt"

func main() {
	v := 42 // 修改这里看看！
	fmt.Printf("v is of type %T\n", v)
}
```

## 零值

没有明确初始化的变量声明会被赋予对应类型的 **零值**。

零值是：

- 数值类型为 `0`，
- 布尔类型为 `false`，
- 字符串为 `""`（空字符串）。

## 常量

常量的声明与变量类似，只不过使用 `const` 关键字。

常量可以是字符、字符串、布尔值或数值。

常量不能用 `:=` 语法声明。

## 数值常量

数值常量是高精度的 **值**。

一个未指定类型的常量由上下文来决定其类型。

再试着一下输出 `needInt(Big)` 吧。

（`int` 类型可以存储最大 64 位的整数，根据平台不同有时会更小。）

```go
package main

import "fmt"

const (
	// 将 1 左移 100 位来创建一个非常大的数字
	// 即这个数的二进制是 1 后面跟着 100 个 0
	Big = 1 << 100
	// 再往右移 99 位，即 Small = 1 << 1，或者说 Small = 2
	Small = Big >> 99
)

func needInt(x int) int { return x*10 + 1 }
func needFloat(x float64) float64 {
	return x * 0.1
}

func main() {
	fmt.Println(needInt(Small))
	fmt.Println(needFloat(Small))
	fmt.Println(needFloat(Big))
}
```

# 类型

## 基本类型

Go 的基本类型有

```
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // uint8 的别名

rune // int32 的别名
     // 表示一个 Unicode 码位

float32 float64

complex64 complex128
```

本例展示了几种类型的变量。 和导入语句一样，变量声明也可以「分组」成一个代码块。

`int`、`uint` 和 `uintptr` 类型在 32- 位系统上通常为 32- 位宽，在 64- 位系统上则为 64- 位宽。当你需要一个整数值时应使用 `int` 类型， 除非你有特殊的理由使用固定大小或无符号的整数类型。

```go
package main

import (
	"fmt"
	"math/cmplx"
)

var (
	ToBe   bool       = false
	MaxInt uint64     = 1<<64 - 1
	z      complex128 = cmplx.Sqrt(-5 + 12i)
)

func main() {
	fmt.Printf("类型：%T 值：%v\n", ToBe, ToBe)
	fmt.Printf("类型：%T 值：%v\n", MaxInt, MaxInt)
	fmt.Printf("类型：%T 值：%v\n", z, z)
}
```

## 类型转换

表达式 `T(v)` 将值 `v` 转换为类型 `T`。

一些数值类型的转换：

```go
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)
```

或者，更加简短的形式：

```go
i := 42
f := float64(i)
u := uint(f)
```

与 C 不同的是，Go 在不同类型的项之间赋值时需要显式转换。试着移除例子中的 `float64` 或 `uint` 的类型转换，看看会发生什么。

```go
package main

import (
	"fmt"
	"math"
)

func main() {
	x, y := 3, 4
	f := math.Sqrt(float64(x*x + y*y))
	z := uint(f)
	fmt.Println(x, y, z)
}
```

## 函数类型

![go-basic](programming/language/go-basic.md#参数类型)

# 流程控制语句

Go 只有一种循环结构：`for` 循环。

基本的 `for` 循环由三部分组成，它们用分号隔开：

- 初始化语句：在第一次迭代前执行
- 条件表达式：在每次迭代前求值
- 后置语句：在每次迭代的结尾执行

初始化语句通常为一句短变量声明，该变量声明仅在 `for` 语句的作用域中可见。

一旦条件表达式求值为 `false`，循环迭代就会终止。

**注意**：和 C、Java、JavaScript 之类的语言不同，Go 的 `for` 语句后面的三个构成部分外没有小括号， 大括号 `{ }` 则是必须的。

```GO
package main

import "fmt"

func main() {
	sum := 0
	for i := 0; i < 10; i++ {
		sum += i
	}
	fmt.Println(sum)
}
```

初始化语句和后置语句是可选的。

```go
func main() {
	sum := 1
	for ; sum < 1000; {
		sum += sum
	}
	fmt.Println(sum)
}
```

## For 是 Go 中的「while」

此时你可以去掉分号，因为 C 的 `while` 在 Go 中叫做 `for`。

```go
package main

import "fmt"

func main() {
	sum := 1
	for sum < 1000 {
		sum += sum
	}
	fmt.Println(sum)
}
```

## If 语句

Go 的 `if` 语句与 `for` 循环类似，表达式外无需小括号 `( )`，而大括号 `{ }` 则是必须的。

```go
package main

import (
	"fmt"
	"math"
)

func sqrt(x float64) string {
	if x < 0 {
		return sqrt(-x) + "i"
	}
	return fmt.Sprint(math.Sqrt(x))
}

func main() {
	fmt.Println(sqrt(2), sqrt(-4))
}
```

### If 和简短语句

和 `for` 一样，`if` 语句可以在条件表达式前执行一个简短语句。

该语句声明的变量作用域仅在 `if` 之内。

（在最后的 `return` 语句处使用 `v` 看看。）

```go
package main

import (
	"fmt"
	"math"
)

func pow(x, n, lim float64) float64 {
	if v := math.Pow(x, n); v < lim {
		return v
	}
	return lim
	// return v; undefined of v
}

func main() {
	fmt.Println(
		pow(3, 2, 10),
		pow(3, 3, 20),
	)
}
```

作用域, if 前面的那一小句有一个局部的作用域

### If 和 Else

在 `if` 的简短语句中声明的变量同样可以在对应的任何 `else` 块中使用。

（在 `main` 的 `fmt.Println` 调用开始前，两次对 `pow` 的调用均已执行并返回其各自的结果。）

```go
package main

import (
	"fmt"
	"math"
)

func pow(x, n, lim float64) float64 {
	if v := math.Pow(x, n); v < lim {
		return v
	} else {
		fmt.Printf("%g >= %g\n", v, lim)
	}
	// can't use v here, though
	return lim
}

func main() {
	fmt.Println(
		pow(3, 2, 10),
		pow(3, 3, 20),
	)
}
```

## Switch 分支

`switch` 语句是编写一连串 `if - else` 语句的简便方法。它运行第一个 `case` 值 值等于条件表达式的子句。

Go 的 `switch` 语句类似于 C、C++、Java、JavaScript 和 PHP 中的，不过 Go 只会运行选定的 `case`，而非之后所有的 `case`。 在效果上，Go 的做法相当于这些语言中为每个 `case` 后面自动添加了所需的 `break` 语句。在 Go 中，除非以 `fallthrough` 语句结束，否则分支会自动终止。 Go 的另一点重要的不同在于 `switch` 的 `case` 无需为常量，且取值不限于整数。

```go
package main

import (
	"fmt"
	"runtime"
)

func main() {
	fmt.Print("Go 运行的系统环境：")
	switch os := runtime.GOOS; os {
	case "darwin":
		fmt.Println("macOS.")
	case "linux":
		fmt.Println("Linux.")
	default:
		// freebsd, openbsd,
		// plan9, windows...
		fmt.Printf("%s.\n", os)
	}
}
```

### 无条件 Switch

无条件的 `switch` 同 `switch true` 一样。

这种形式能将一长串 `if-then-else` 写得更加清晰。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	t := time.Now()
	switch {
	case t.Hour() < 12:
		fmt.Println("早上好！")
	case t.Hour() < 17:
		fmt.Println("下午好！")
	default:
		fmt.Println("晚上好！")
	}
}
```

## Defer

defer 语句会将函数推迟到外层函数返回之后执行。

推迟调用的函数其参数会立即求值，但直到外层函数返回前该函数都不会被调用。

```go
package main

import "fmt"

func main() {
	defer fmt.Println("world")

	fmt.Println("hello")
}
```

### Defer 栈

推迟调用的函数调用会被压入一个栈中。 当外层函数返回时，被推迟的调用会按照后进先出的顺序调用。

更多关于 defer 语句的信息，请阅读 [此博文](http://blog.go-zh.org/defer-panic-and-recover)。

```go
package main

import "fmt"

func main() {
	fmt.Println("counting")

	for i := 0; i < 10; i++ {
		defer fmt.Println(i)
	}

	fmt.Println("done")
}
```

# 指针

Go 拥有指针。指针保存了值的内存地址。

类型 `*T` 是指向 `T` 类型值的指针，其零值为 `nil`。

```go
var p *int
```

`&` 操作符会生成一个指向其操作数的指针。

```go
i := 42

p = &i
```

`*` 操作符表示指针指向的底层值。

```go
fmt.Println(*p) // 通过指针 p 读取 i
*p = 21         // 通过指针 p 设置 i
```

这也就是通常所说的「解引用」或「间接引用」。

与 C 不同，Go 没有指针运算。

> [!NOTE] 与 JS 不同, 原始类型也可以通过引用修改

```go
package main

import "fmt"

func main() {
	i, j := 42, 2701

	p := &i         // 指向 i
	fmt.Println(*p) // 通过指针读取 i 的值
	*p = 21         // 通过指针设置 i 的值
	fmt.Println(i)  // 查看 i 的值

	p = &j         // 指向 j
	*p = *p / 37   // 通过指针对 j 进行除法运算
	fmt.Println(j) // 查看 j 的值
}
```

# 结构体

一个 结构体（`struct`）就是一组 字段（field）。

```go
type Vertex struct {
	X int
	Y int
}

func main() {
	fmt.Println(Vertex{1, 2})
}
```

感觉就是 js 里的对象

## 结构体指针

结构体字段可通过结构体指针来访问。

如果我们有一个指向结构体的指针 `p` 那么可以通过 `(*p).X` 来访问其字段 `X`。 不过这么写太啰嗦了，所以语言也允许我们使用隐式解引用，直接写 `p.X` 就可以。

```go
type Vertex struct {
	X int
	Y int
}

func main() {
	v := Vertex{1, 2}
	p := &v
	p.X = 1e9
	fmt.Println(v)
}

```

## 结构体字面量

使用 `Name:` 语法可以仅列出部分字段（字段名的顺序无关）。

特殊的前缀 `&` 返回一个指向结构体的指针。

```go
type Vertex struct {
	X, Y int
}

var (
	v1 = Vertex{1, 2}  // 创建一个 Vertex 类型的结构体
	v2 = Vertex{X: 1}  // Y:0 被隐式地赋予零值
	v3 = Vertex{}      // X:0 Y:0
	p  = &Vertex{1, 2} // 创建一个 *Vertex 类型的结构体（指针）
)

func main() {
	fmt.Println(v1, p, v2, v3)
}
```
