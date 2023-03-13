# Go基础

![](https://img.sj33.cn/uploads/202011/7-20111FZTI04.jpg)

【简介】

由Unix和C语言创始人之一，肯汤普森发明

为的就是优化C语言的基础之上发明的Go

为合理利用多核多CPU、简洁高效、还能处理大并发

被称为"21世纪的C语言"

Go语言吉祥物：gordon 

![go语言吉祥物 的图像结果](https://tse4-mm.cn.bing.net/th/id/OIP-C.7lz8QNYHKfo96_xmpIIq9AAAAA?w=119&h=180&c=7&r=0&o=5&pid=1.7)

| [Go官网](http://golang.org) | [Go博客](https://go.dev/blog/) | [Go标准库](https://pkg.go.dev/std) |



Go语言特点：

* 语法简洁
* 开发效率高
* 执行性能好
* 天生支持并发



Go语言的包的概念：Go的每一个文件都属于一个包，不能单独存在

Go语言有垃圾回收机制，内存自动回收

GO语言的函数可以返回多个值

Go语言语句后面不需要带分号，编译器自动给你带上分号

------



# 开发环境搭建

Windows环境：

1. 安装配置SDK（Software Development Kit：软件开发工具包）
2. 配置系统环境变量
   * GOROOT：Go的安装目录
   * GOPATH：工作目录，也就是写的代码放在哪儿
   * Path：里面配置Go安装目录中的bin的目录



Go语言开发的目录结构：

* 开发目录：GoDocument
  * bin
  * pkg
  * src（放创建的Go项目）
    * project01
      * main
        * 源码：hello.go
      * package
    * project02
    * .....



所有 Go 源文件都应该放置在工作区里的 **src** 目录下。请在刚添加的 **GoDocument** 目录下面创建目录 **src**。

所有 Go 项目都应该依次在 src 里面设置自己的子目录。我们在 src 里面创建一个目录 **hello** 来放置整个 hello world 项目。

创建上述目录之后，其目录结构如下：

```
GoDocument
  src
    hello
```

在我们刚刚创建的 hello 目录下，在 **helloworld.go** 文件里保存下面的程序。

```go
package main

import "fmt"

func main() {  
    fmt.Println("Hello World")
}
```

创建该程序之后，其目录结构如下：

```go
GoDocument
  src
    hello
      helloworld.go
```



运行 Go 程序有多种方式，我们下面依次介绍。

1.使用 **go run** 命令 - 在命令提示符旁，输入 `go run workspacepath/src/hello/helloworld.go`。

上述命令中的 **workspacepath** 应该替换为“GoDocument”的绝对路径

在控制台上会看见 `Hello World` 的输出。

2.使用 **go install** 命令 - 运行 `go install hello`，接着可以用 `workspacepath/bin/hello` 来运行该程序。

上述命令中的 **workspacepath** 应该替换为你自己的工作区路径

当你输入 **go install hello** 时，go 工具会在工作区中搜索 hello 包（hello 称之为包，我们后面会更加详细地讨论包）。接下来它会在工作区的 bin 目录下，创建一个名为 `hello`（Windows 下名为 `hello.exe`）的二进制文件。运行 **go install hello** 后，其目录结构如下所示：

```go
GoDocument
  bin
    hello
  src
    hello
      helloworld.go
```

------



# 第一章 入门

## 1.1 Hello World

使用[gopl.io](http://www.gopl.io/)提供的Go的项目：【gopl.io】

gopl.io/ch1/helloworld

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello, 世界")
}
```

Go是一门静态编译型语言，Go语言的工具链将源代码及其依赖转换成计算机的机器指令

Go语言提供的工具都通过一个单独的命令 go 调用， go 命令有一系列子命令。 最简单的一个子命令就是run。这个命令编译一个或多个以.go结尾的源文件，链接库文件，并 运行最终生成的可执行文件。

```shell
go run helloworld.go
```

Go语言的代码通过包（package）组织，包类似于其它语言里的库 （libraries）或者模块（modules）。<u>一个包由位于单个目录下的一个或多个.go源代码文件组 成, 目录定义包的作用</u>。每个源文件都以一条 package 声明语句开始，这个例子里就 是 package main , 表示该文件属于哪个包，紧跟着一系列导入（import）的包，之后是存储在 这个文件里的程序语句。

[Go标准库](https://pkg.go.dev/std)提供了100多个包，以支持常见功能，如输入、输出、排序以及文本处理。比如 fmt 包，就含有格式化输出、接收输入的函数。 Println 是其中一个基础函数，可以打印以空格间隔的一个或多个值，并在最后添加一个换行符，从而输出一整行

main 包比较特殊。它定义了一个独立可执行的程序，而不是一个库。在 main 里的 main 函数也很特殊，它是整个程序执行时的入口（译注：C系语言差不多都这样）。 main 函数所做的事情就是程序做的。当然了， main 函数一般调用其它包里的函数完成很多工作, 比如, fmt.Println 



注意：

* 必须恰当导入需要的包，缺少了必要的包或者导入了不需要的包，程序都无法编译通过
* Go语言不需要在语句或者声明的末尾添加分号
* Go语言在代码格式上采取了很强硬的态度。 gofmt 工具把代码格式化为标准格式

------



## 1.2 命令行参数

通常情况下，输入来自于程序外部：文件、网络连接、其它程序的输出、敲键盘的用户、命令行参数或其它类似输入源

os 包以跨平台的方式，提供了一些与操作系统交互的函数和变量。**程序的命令行参数可从os包的Args变量获取**；os包外部使用os.Args访问该变量

os.Args变量是一个字符串（string）的切片（slice），切片是Go语言的基础概念。用 s[i] 访问单个元素，用 s[m:n] 获取子序列。省略切片表达式的m或n，会默认传入0或 len(s)，因此前面的切片os.Args[1:len(os.Args)]可以简写成os.Args[1:]

os.Args的第一个元素，os.Args[0], 是命令本身的名字；其它的元素则是程序启动时传给它的参数。

下面是Unix里echo命令的一份实现，echo把它的命令行参数打印成一行：

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	var s, sep string
	for i := 1; i < len(os.Args); i++ {
		s += sep + os.Args[i]
		sep = " "
	}
	fmt.Println(s)
}
```

var声明定义了两个string类型的变量s和sep。变量会在声明时直接初始化。如果变量没有显式初始化，则被隐式地赋予其类型的零值（zero value），数值类型是0，字符串类型是空字符串""。

Go语言提供了常规的数值和逻辑运算符。而对string类型， + 运算符连接字符串

符号 := 是短变量声明（short variable declaration）的一部分, 这是定义一个或多个变量并根据它们的初始值为这些变量赋予适当类型的语句。

Go不像C系的其它语言那样，i++/++i是表达式，它们是语句。所以 j = i++ 非法，而且++和--都只能放在变量名后面，因此 --i 也非法

Go语言只有for循环这一种循环语句

```go
for initialization; condition; post {
	// zero or more statements
}

```

for循环的这三个部分每个都可以省略；达到传统的while循环和无限循环

```go
// a traditional "while" loop
for condition {
	// ...
}

// a traditional infinite loop
for {
	// ...
}
```

终止循环：如一条 break 或 return 语句

for 循环的另一种形式, 在某种数据类型的区间（range）上遍历，如字符串或切片

 echo 的第二版本展示了这种形式：

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	s, sep := "", ""
	for i, arg := range os.Args[0:] {
		s += sep + arg
		sep = " "
		fmt.Print("index:", i, " value: ")
	}
	fmt.Println(s)
}
```

每次循环迭代， range 产生一对值：索引以及在该索引处的元素值。

Go语言不允许使用无用的局部变量（local variables），因为这会导致编译错误。

 空标识符 （blank identifier），即 _ （也就是下划线）。 空标识符可用于任何语法需要变量名但程序逻辑不需要的时候

```go
s := "" //短变量声明，最简洁，但只能用在函数内部，而不能用于包变量
var s string //依赖于字符串的默认初始化零值机制，被初始化为""
var s = "" //用得很少，除非同时声明多个变量
var s string = "" //显式地标明变量的类型，当变量类型与初值类型相同时，类型冗余，但如果两者类型不同，变量类型就必须了。
```

strings 包的 Join 函数与 + 拼接字符串效果一致

------



## 1.3 查找重复的行

对文件做拷贝、打印、搜索、排序、统计或类似事情的程序都有一个差不多的程序结构：一 个处理输入的循环，在每个元素上执行计算处理，在处理的同时或最后产生输出

接下来的dup 程序的三个版本；灵感来自于Unix的 uniq 命令，其寻找相邻的重复行

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	counts := make(map[string]int)
	input := bufio.NewScanner(os.Stdin)
	for input.Scan() {
		counts[input.Text()]++
	}
	// NOTE: ignoring potential errors from input.Err()
	for line, n := range counts {
		if n > 1 {
			fmt.Printf("%d\t%s\n", n, line)
		}
	}
}
```

if 语句条件两边不加括号

map存储了键/值（key/value）的集合，对集合元素，提供常数时间的存、取或测试操作。键可以是任意类型，只要其值能用 == 运算符比较，最常见的例子是字符串；值则可以是任意类型。

内置函数 make 创建空 map

从功能和实现上说， Go 的 map 类似于 Java 语言中的 HashMap

bufio 包，它使处理输入和输出方便又高效。 Scanner 类型是该包最有用的特性之 一，它读取输入并将其拆成行或单词；通常是处理行形式的输入最简单的方法

类似于C或其它语言里的 printf 函数， fmt.Printf 函数对一些表达式产生格式化输出。该函 数的首个参数是个格式字符串，指定后续参数被如何格式化。各个参数的格式取决于“转换字 符”（conversion character），形式为百分号后跟一个字母。

格式化输出的verb：

```go
%d 十进制整数
%x, %o, %b 十六进制，八进制，二进制整数。
%f, %g, %e 浮点数： 3.141593 3.141592653589793 3.141593e+00
%t 布尔：true或false
%c 字符（rune） (Unicode码点)
%s 字符串
%q 带双引号的字符串"abc"或带单引号的字符'c'
%v 变量的自然形式（natural format）
%T 变量的类型
%% 字面上的百分号标志（无操作数）
```

dup 程序的下个版本读取标准输入或是使用 os.Open 打开各个具名文件，并操作它们

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	counts := make(map[string]int)
	files := os.Args[1:]
	if len(files) == 0 {
		countLines(os.Stdin, counts)
	} else {
		for _, arg := range files {
			f, err := os.Open(arg)
			if err != nil {
				fmt.Fprintf(os.Stderr, "dup2: %v\n", err)
				continue
			}
			countLines(f, counts)
			f.Close()
		}
	}
	for line, n := range counts {
		if n > 1 {
			fmt.Printf("%d\t%s\n", n, line)
		}
	}
}

func countLines(f *os.File, counts map[string]int) {
	input := bufio.NewScanner(f)
	for input.Scan() {
		counts[input.Text()]++
	}
	// NOTE: ignoring potential errors from input.Err()
}
```

os.Open 函数返回两个值。第一个值是被打开的文件( *os.File ），其后被 Scanner 读取。 os.Open 返回的第二个值是内置 error 类型的值

为了使示例代码保持合理的大小，一些示例有意简化了错误处理，显而易见的是，应该检查 os.Open 返回的错误值

函数在其声明前被调用。函数和包级别的变量（package-level entities）可以任意顺序声明，并不影响其被调用。

map 是一个由 make 函数创建的数据结构的引用。 map 作为为参数传递给某函数时，该函数 接收这个引用的一份拷贝（copy，或译为副本），被调用函数对 map 底层数据结构的任何修 改，调用者函数都可以通过持有的 map 引用看到。在我们的例子中， countLines 函数 向 counts 插入的值，也会被 main 函数看到。（类似于C++里的引用传递，实际上指针是另一个指针了，但内部存的值指向同一块内存）

一口气把全部输入数据读到内存中，一次分 割为多行，然后处理它们。下面这个版本， dup3 ，就是这么操作的：

```go
package main

import (
	"fmt"
	"io/ioutil"
	"os"
	"strings"
)

func main() {
	counts := make(map[string]int)
	for _, filename := range os.Args[1:] {
		data, err := ioutil.ReadFile(filename)
		if err != nil {
			fmt.Fprintf(os.Stderr, "dup3: %v\n", err)
			continue
		}
		for _, line := range strings.Split(string(data), "\n") {
			counts[line]++
		}
	}
	for line, n := range counts {
		if n > 1 {
			fmt.Printf("%d\t%s\n", n, line)
		}
	}
}
```

ReadFile 函数（来自于 io/ioutil 包），其读取指定文件的全部内容， strings.Split 函数把字符串分割成子串的切片。

ReadFile 函数返回一个字节切片（byte slice），必须把它转换为 string ，才能 用 strings.Split 分割

------



## 1.4 GIF动画

下面的程序会演示Go语言标准库里的image这个package的用法，我们会用这个包来生成一系 列的bit-mapped图，然后将这些图片编码为一个GIF动画。我们生成的图形名字叫利萨如图形 (Lissajous figures)，这种效果是在1960年代的老电影里出现的一种视觉特效。它们是协振子 在两个纬度上振动所产生的曲线，比如两个sin正弦波分别在x轴和y轴输入会产生的曲线

```go
package main

import (
	"image"
	"image/color"
	"image/gif"
	"io"
	"math"
	"math/rand"
	"os"
)

//!-main
// Packages not needed by version in book.
import (
	"log"
	"net/http"
	"time"
)

//!+main

var palette = []color.Color{color.White, color.Black}

const (
	whiteIndex = 0 // first color in palette
	blackIndex = 1 // next color in palette
)

func main() {
	//!-main
	// The sequence of images is deterministic unless we seed
	// the pseudo-random number generator using the current time.
	// Thanks to Randall McPherson for pointing out the omission.
	rand.Seed(time.Now().UTC().UnixNano())

	if len(os.Args) > 1 && os.Args[1] == "web" {
		//!+http
		handler := func(w http.ResponseWriter, r *http.Request) {
			lissajous(w)
		}
		http.HandleFunc("/", handler)
		//!-http
		log.Fatal(http.ListenAndServe("localhost:8000", nil))
		return
	}
	//!+main
	lissajous(os.Stdout)
}

func lissajous(out io.Writer) {
	const (
		cycles  = 5     // number of complete x oscillator revolutions
		res     = 0.001 // angular resolution
		size    = 100   // image canvas covers [-size..+size]
		nframes = 64    // number of animation frames
		delay   = 8     // delay between frames in 10ms units
	)
	freq := rand.Float64() * 3.0 // relative frequency of y oscillator
	anim := gif.GIF{LoopCount: nframes}
	phase := 0.0 // phase difference
	for i := 0; i < nframes; i++ {
		rect := image.Rect(0, 0, 2*size+1, 2*size+1)
		img := image.NewPaletted(rect, palette)
		for t := 0.0; t < cycles*2*math.Pi; t += res {
			x := math.Sin(t)
			y := math.Sin(t*freq + phase)
			img.SetColorIndex(size+int(x*size+0.5), size+int(y*size+0.5),
				blackIndex)
		}
		phase += 0.1
		anim.Delay = append(anim.Delay, delay)
		anim.Image = append(anim.Image, img)
	}
	gif.EncodeAll(out, &anim) // NOTE: ignoring encoding errors
}
```

------



## 1.5 获取URL

Go语言在net这 个强大package的帮助下提供了一系列的package来做这件事情，使用这些包可以更简单地用 网络收发信息，还可以建立更底层的网络连接，编写服务器程序。在这些情景下，Go语言原 生的并发特性显得尤其好用

为了最简单地展示基于HTTP获取信息的方式，下面给出一个示例程序fetch，这个程序将获取 对应的url，并将其源文本打印出来；这个例子的灵感来源于curl工具（译注：unix下的一个用 来发http请求的工具，具体可以man curl）。当然，curl提供的功能更为复杂丰富，这里只编 写最简单的样例。这个样例之后还会多次被用到

```go
package main

import (
	"fmt"
	"io/ioutil"
	"net/http"
	"os"
)

func main() {
	for _, url := range os.Args[1:] {
		resp, err := http.Get(url)
		if err != nil {
			fmt.Fprintf(os.Stderr, "fetch: %v\n", err)
			os.Exit(1)
		}
		b, err := ioutil.ReadAll(resp.Body)
		resp.Body.Close()
		if err != nil {
			fmt.Fprintf(os.Stderr, "fetch: reading %s: %v\n", url, err)
			os.Exit(1)
		}
		fmt.Printf("%s", b)
	}
}
```

这个程序从两个package中导入了函数，net/http和io/ioutil包，http.Get函数是创建HTTP请求 的函数，如果获取过程没有出错，那么会在resp这个结构体中得到访问的请求结果。resp的 Body字段包括一个可读的服务器响应流。ioutil.ReadAll函数从response中读取到全部内容；将其结果保存在变量b中。resp.Body.Close关闭resp的Body流，防止资源泄露，Printf函数会 将结果b写出到标准输出流中。
