# Go语言基础入门

## 1.Go语言开发环境搭建

### 1-1 Windows系统中安装Go

**1. Go 语言环境安装**

Go 语言支持以下系统：

- Linux
- FreeBSD
- Mac OS X（也称为 Darwin）
- Window

安装包下载地址为：[Go官网](https://golang.google.cn/dl/)

打开浏览器，输入Go语言官方网址，点击**Micrisoft Windows** 安装包镜像进行下载。

![](.\imgs\1.png)

**2. 下载完成后，按照Go语言安装包**

下载完成后，进入下载文件所在目录，选择安装包，点击“install”按钮，然后按照提示点击“Next”按钮。系统推荐安装到默认路径（C:\Go\）,也可以自己选择安装目录。

![](.\imgs\2.png)

**3. 安装完成，打开终端，检查是否成功**

```go
// 在终端输入 Go version 命令，查看安装的Go版本。
```

![](.\imgs\3.png)

不同于其他语言，go中没有项目的说法，只有包，其中有两个重要的路径，**GOROOT**和**GOPATH**。

Go开发相关的环境变量如下：

- GoROOT：GoROOT就是Go的安装目录。（类似于java的JDK）

- GoPATH：GoPATH是我们的工作空间,保存Go项目代码和第三方依赖包。

GoROOT 和 GoPATH 都是环境变量，其中 **GoROOT 是我们安装Go开发包的路径**，而从Go 1.8版本开始，**Go开发包在安装完成后会为 GoPATH 设置一个默认目录**，并且在Go1.14及之后的版本中启用了 Go Module 模式之后，不一定非要将代码写到 GoPATH 目录下，所以也就不需要我们再自己配置 GoPATH 了，使用默认的即可。

```go
// 在终端输入 Go env  命令，查看环境变量。
```

![](.\imgs\4.png)

### 1-2 Go语言配置环境变量

第一步：返回电脑桌面，鼠标右键打开 **属性，** 打开系统属性-高级

第二步：点击环境变量

第三步：点击系统变量点击新建

第四步：更改系统变量Path的值：

GOROOT

浏览目录，点击打开项目的安装目录

第五步：检查环境配置是否配置成功

打开cmd编辑器，输入 go env 查看当前环境

### 1-3 Go付费编译器：Goland下载

Go采用的是UTF-8编码的文本文件存放源代码，**理论上使用任何一款文本编辑器都可以做Go语言开发**，这里我们使用 Goland 加插件做为Go语言的开发工具。

Goland是做Go语言开发的专用编辑器，免费试用一个月

后续的知识点讲解，我们也使用Goland编辑器进行讲解

Goland官方下载地址：[jetbrains官网](https://www.jetbrains.com/Go)

根据自己的电脑平台选择对应的安装包。

双击exe安装文件，自定义安装，安装成功即可

Goland 使用技巧：

鼠标右键，展示出来悬浮框，如图所示

![](.\imgs\5.png)

**（1）新建项目**

​       点击**Project（新建项目）**，可以新建一个项目，如我们新建一个Go-basics 文件

![](.\imgs\6.png)

**（2）新建文件夹**

​     鼠标右键，点击**新建文件夹**，可以新建一个文件夹，如我们新建一个< subtask0202 >文件夹

![](.\imgs\7.png)

**（3）新建Go文件**

​      在subtask0202基础下，选择Go file ，新建subtask0202.Go文件

![](.\imgs\8.png)

**（4）在集成终端中打开**

​      打开一个新终端，后续学习Go命令使用(点击下方Terminal即可显示终端)

![](.\imgs\9.png)

------



## 2.Go语言的main()函数

### main()函数

**1. main()函数只能声明在main包中，不能声明在其他包中。**

**结构释义：**

```go
package main                                  	// 包的声明
import "fmt"                                    // 引入包
func main() {                                   // 函数
}
```

**解析：** main函数作为程序的运行唯一入口，如果main函数没有存在于main包中则程序就无法**指明程序的内存空间位置**，在程序运行编译时就会运行出错。

**举例：**当有一个main函数存在于test包中，能否顺利编译打印出“幸福没有那么容易”。

```go
package test               		// 包的声明
import "fmt"                    // 引入包
func main() {                   // 函数
   fmt.Println("幸福没有那么容易") // 语句&表达式输出
}
```

**终端提示编译报错，无法输出想要的结果：**

![](.\imgs\10.png)

**将例子中的test包名替换成包名main后**

**终端输出：**

![](.\imgs\11.png)

**结构解析：**

```go
package main  // 包的声明
// package [包名]
```

**包声明**：package main 定义了包名。package main 代表当前的 main.go 文件属于哪个包，其中 package 是 Go 语言声明包的关键字，main 是要声明的包名。

**结论：main函数作为程序的唯一入口，只能声明在main包中。**

**2. 一个main包中必需仅有一个main函数，不能出现两个或者多个。**

**解析：** 一个main包中应该只允许存在一个main()函数，**golang函数不支持重载**，**一个包不能有两个函数名一样的函数**。main()函数作为函数编译的执行主入口，如果同时出现两个函数执行入口，系统就会无法选择正确的执行入口从而报错。

**举例：**当一个main包中同时存在两个相同main函数，那是否能输出两个相同的函数值，假设均输出“幸福没有那么容易”。

```go
func main() {                      // 函数
   fmt.Println("幸福没有那么容易")    // 语句&表达式输出
}
func main() {                      // 函数
   fmt.Println("幸福没有那么容易")    // 语句&表达式输出
}
```

**终端显示：**

![](.\imgs\12.png)

**显示重复声明编译报错。**

**当去掉一个main函数以后，函数正常输出。**

**终端输出：**

![](.\imgs\13.png)

**结构解析：**

```go
func main() {}  // 函数  
```

**函数**：func main() 是程序开始执行的函数。main 函数是每一个可执行程序所必须包含的，一般来说都是在启动后第一个执行的函数（如果有 init() 函数则会先执行该函数）。

```go
fmt.Println("幸福没有那么容易")  // 语句&表达式输出
```

**语句&表达式输出**：fmt.Println(...) 可以将字符串输出到控制台，并在最后自动增加换行字符 \n。函数内语句依照main函数内依次执行。

**结论：一个main包中仅有一个main函数。**

**3. 一个go程序如果没有main()函数，程序就不能够正常的运行。**

**解析：**main()函数作为Go语言应用程序的入口函数，只有当一个Go程序中存在一个函数入口时，函数内的语句表达式才能被编译执行从而输出结果。

**举例：**当一个简单的go程序没有main()函数时，它能输出打印结果么？

```go
func text() {                              // 函数  
   fmt.Println("Hello World!")             // 语句&表达式输出
}
```

输出结果：

![](.\imgs\14.png)

**程序不能正常打印，且会在运行时报错。**

**当text()函数替换成main()函数后：**

```go
func main () {                         // 函数  
   fmt.Println("Hello World!")         // 语句&表达式输出
}
```

终端输出：

![](.\imgs\15.png)

**函数**：func main() 是程序开始执行的函数。main 函数是每一个可执行程序所必须包含的，一般来说都是在启动后第一个执行的函数（如果有 init() 函数则会先执行该函数）。

**总结：一个go程序有了main() 函数，程序才能正常运行。**

------



## 3.Go基础语法与使用

### 3-1 关键字分析

**Go语言中的基础类型关键字大致有25个：**

```go
/*
break     default      func    interface    select
case      defer        go      map          struct
chan      else         goto    package      switch
const     fallthrough  if      range        type
continue  for          import  return       var
*/
```

**关键字：**作为go语言中重要的组成部分，在各种数据声明，数据执行中都有很多使用的地方。

**关键字释义：**

-   var和const：变量和常量的声明
-   package 和 import：导入
-   func：用于定义函数和方法
-   return：用于从函数返回
-   defer someCode：在函数退出之前执行
-   go: 用于并行
-   select：用于选择不同类型的通讯
-   interface：用于定义接口
-   struct：用于定义抽象数据类型
-   break、case、continue、for、fallthrough、else、if、switch、goto、default：流程控制
-   chan用于channel通讯
-   type用于声明自定义类型
-   map用于声明自定义类型
-   range用于读取slice，map， channel数据

**实例（1）**：在go语言编辑器中分别声明一个变量和一个常量。

```go
var Name string              // 变量的声明
const name = 3.13            // 常量的声明
```

**解析：**无论是变量还是常量的声明，在声明过程中都需要关键字来定义，在关键字的定义下go语言编辑器才能识别相应的方法操作，从而实现对应的功能，对于变量声明，不论定义什么类型的变量，都可以使用var来定义。对于常量声明，go语言使用关键字const来声明。

**实例（2）**：在go语言编辑器中实现包的声明。

```go
package main                // 包的声明
```

**解析：**package main 代表当前的 .go文件属于哪个包，其中 package 是 Go 语言声明包的关键字，main 是要声明的包名。package main表示一个可独立执行的程序，每个 Go 应用程序都包含一个名为 main 的包。

**实例（3）**：在go语言编辑器中实现包名为package的包的声明。

```go
package package
```

**解析：**如果在声明时使用关键字做包名，则go语言编译器就无法正确编译，报错显示出现关键字符号。

终端输出：

![](.\imgs\16.png)

**总结：Go语言中的关键字, 是指被Go语言赋予特殊含义的单词，在定义程序中的类型，方法，函数，接口等各方面都有特殊用处，它与C语言中的关键字存在形式是类似的。需要注意的是他本身的特殊含义不能作为变量或方法命名。**

### 3-2 变量分析

#### 标准格式声明

**1. 结构：**

```go
var [name] [type]
```

结构分解：

![](.\imgs\17.png)

**解析：**其中 var 是关键字（固定不变），name 是变量名，type 是类型。在 Go 程序中，变量的声明通过关键字var来引导变量的声明从而在编辑器编译中获取存储位置，而Go语言和许多编程语言不同的是，它在**声明变量时将变量的类型放在变量的名称之后**。在声明变量后再获取对应的变量类型实现标准格式化的变量声明。

**2. 举例：**分别声明一个字符串类型变量和一个整型类型的变量并分别输出。

```go
var PrevBlockHash string             // 变量的声明  
var blockHeight int
fmt.Println(PrevBlockHash)           // 语句&表达式输出
fmt.Println(blockHeight)
```

终端输出：

![](.\imgs\18.png)

**结构解析：**

```go
var PrevBlockHash string              // 变量的声明  
var blockHeight int
```

**变量声明：**Go语言是**静态类型语言**，因此变量（variable）是有明确类型的。变量通过关键字var来命名标识符再通过明确变量类型来完成变量的完整声明的过程。编译器也会检查函数调用中，来验证变量类型的正确性。

```go
fmt.Println(PrevBlockHash)         // 语句&表达式输出
fmt.Println(blockHeight)
```

**语句&表达式输出**：fmt.Println(...) 可以将字符串输出到控制台，并在最后自动增加换行字符 \n。函数内语句依照main函数内依次执行。

#### 标准格式赋值分析

**1. 结构：**

```go
var [name] [type] = [value]
```

**结构分解：**

![](.\imgs\19.png)

**解析：**其中 var 是关键字（固定不变），name 是变量名，type 是类型，value是所赋值。使用 var ，虽然只指定了类型，但是 Go 会对其进行**隐式初始化**。

**2. 举例：**分别对一个字符串类型变量和一个整型类型的变量进行初始化赋值并分别输出。

```go
var PrevBlockHash string = "前置hash"       // 变量的赋值  
var blockHeight int = 3
fmt.Println(PrevBlockHash)                 // 语句&表达式输出
fmt.Println(blockHeight)
```

终端输出：

![](.\imgs\20.png)

结构解析：

```go
var PrevBlockHash string = "前置hash"            // 变量的赋值 
var blockHeight int = 3
```

**变量赋值：**在Go语言中给变量的赋值可以分为三个阶段，**先创建变量**，在内存中开辟空间后再初始化变量，**将变量初始化为underfined**，**最后再进行真正赋值**。

```go
fmt.Println(PrevBlockHash)                      // 语句&表达式输出
fmt.Println(blockHeight)
```

**语句&表达式输出**：fmt.Println(...) 可以将字符串输出到控制台，并在最后自动增加换行字符 \n。函数内语句依照main函数内依次执行。

#### 局部变量分析

**1. 释义：**

在**函数体内声明的变量**称之为**局部变量**，它们的**作用域只在函数体内**，**函数的参数和返回值变量**都属于局部变量。局部变量一定是在函数内部。在哪个{}内部声明,只能在哪个{}内部访问。

**2. 举例：**在一个基本的go语言程序中，声明一个局部变量。

```go
func main() {                      // 函数
   var age1 int = 10               // 变量的声明  
   fmt.Printf("age1 = %d ", age1)  // 语句&表达式输出
}
```

终端输出：

![](.\imgs\21.png)

结构解析：

```go
var age1 int = 10            // 局部变量声明 
```

**局部变量声明**：局部变量，它们的作用域只在函数体内，函数的参数和返回值变量都属于局部变量。局部变量一定是在函数内部。在哪个{}内部声明,只能在哪个{}内部访问。局部变量不是一直存在的，它只在定义它的函数被调用后存在，**函数调用结束后这个局部变量就会被销毁**。

#### 全局变量分析

**1. 解析：**

在**函数体外声明的变量**称之为**全局变量**。全局变量**只需要在一个源文件中定义**，就**可以在所有源文件中使用**。当然，不包含这个全局变量的源文件需要**使用“import”关键字引入全局变量所在的源文件**之后才能使用这个全局变量。

**2. 举例：**在一个基本的go语言程序中，声明一个全局变量。

```go
var age4 int = 2022                    // 变量的声明
func main() {                                                         //函数
   fmt.Printf("age4 = %d", age4)       // 语句&表达式输出
}
```

终端输出：

![](.\imgs\22.png)

结构解析：

```go
var age4 int = 2022          // 全局变量声明
```

**全局变量声明：**在函数体外声明的变量称之为全局变量。全局变量声明必须以 var 关键字开头，如果**想要在外部包中使用全局变量的首字母必须大写**。全局变量声明到函数外部，**整个包都可以访问**。如果**全局变量首字母大写，跨包也可以访问**。

### 3-3 常量分析

#### 常量初始化分析

1.**显式定义**

结构：

```go
const [name] [type] = [value]
```

结构分析：

![](.\imgs\23.png)

解析：常量使用一个名称来绑定一块内存地址，该内存地址中存放的数据类型由定义常量时指定的类型来决定，并且**存放在该内存地址里面的存放的值是不可以被改变的**。

**举例：**请用go语言的结构去输出一个值为字符串类型的常量name。

```go
const name string = "知链-区块链人才培养摇篮"           // 常量的显式赋值
fmt.Println(name)                                   // 语句&表达式输出
```

终端输出：

![](.\imgs\24.png)

结构解析：

```go
const name string = "知链-区块链人才培养摇篮"             // 常量的显式赋值
```

**常量的显式赋值：**常量的声明以关键字const开头，后接常量类型并进行赋值，行尾没有其他标点符号。需要注意的是**常量在定义的时候必须赋值**，但**不能使用短变量声明关键字 := 来定义**常量。

2.**隐式定义**

结构：

```go
const [name] = [value]
```

**解析：**由于Go是**编译型语言**，**定义常量时可以省略常量类型**，因为**编译器可以根据变量的值来推断其类型**。常量声明可以同时指定类型和值，如果没有显式指定类型，则类型**根据右边的表达式推断**。若**同时声明一组变量**，除了第一项之外，**其他项在等号右侧的表达式都可以省略**，这意味着**会复用前面一项的表达式及其类型**。

**举例：**请用go语言的结构去输出一个值为字符串类型的常量pi。

```go
const pi = 3.1415926                                  // 常量的隐式赋值
fmt.Println(pi)                                       // 语句&表达式输出
```

终端输出：

![](.\imgs\25.png)

结构解析：

```go
const pi = 3.1415926                                  // 常量的隐式赋值
```

**常量的隐式赋值：**由于Go是编译型语言，定义常量时可以省略常量类型，因为编译器可以根据变量的值来推断其类型。常量声明可以同时指定类型和值，如果没有显式指定类型，则类型根据右边的表达式推断。若同时声明一组变量，除了第一项之外，其他项在等号右侧的表达式都可以省略，这意味着会复用前面一项的表达式及其类型。

#### 常量的作用域分析

**1. 局部常量作用域**

**解析：**在**函数体内声明的常量**称之为**局部常量**，它们的**作用域只在函数体内**，局部常量一定是在函数内部。在哪个{}内部声明,只能在哪个{}内部访问。但需要注意**与变量不同的是，常量在初始化后不需要必须使用，可以只声明赋值不做其他操作**。

**举例：**在一个基本的go语言程序中，声明一个局部常量。

```go
func main() {                                   // 函数
   const name string = “知链-区块链人才培养摇篮”    // 常量的初始化
   fmt.Printf(name)                             // 语句&表达式输出
}
```

**2. 全局常量作用域**

**解析：在函数体外声明的变量称之为全局常量**。**全局常量只需要在一个源文件中定义**，就**可以在所有源文件中使用**。当然，不包含这个全局常量的源文件需要**使用“import”关键字引入全局常量所在的源文件**之后才能使用这个全局常量。

**举例：**在一个基本的go语言程序中，声明一个全局常量。

```go
const PI = 3.1415926                        // 常量的初始化
func main() {                               // 函数
	fmt.Println(PI)                         // 语句&表达式输出
}
```

终端输出：

![](.\imgs\26.png)

结构解析：

```go
const PI = 3.1415926                       // 常量的初始化
```

**全局常量声明：**在函数体外声明的常量称之为全局常量。全局常量声明必须以 const 关键字开头，如果想**要在外部包中使用全局常量的首字母必须大写**。**全局常量声明到函数外部,整个包都可以访问。如果全局常量首字母大写,跨包也可以访问**。

------



## 4.Go运算符

### 4-1 算术与赋值运算符分析

#### 算术运算符分析

1.结构：

```go
变量3 := 变量1 算术运算符 变量2
```

结构分析：

![](.\imgs\27.png)

**解析：** 该算术语句中使用**短类型声明（:=）**在语句编译过程中**先获取一个变量3的数据存储位置**（即语句经过算术**运算后所得的新数据存储地址**），再通过算术运算符的特性（只对数值类型的变量进行运算）**将变量1和变量2的数值相加**，所得的**新结果再赋值给变量3，并存储到新地址中**从而完成整个计算。

**算术运算符是对数值类型的变量进行运算的，比如：加减乘除。**

算数运算符一览：

| 算术运算符 | 描述 |
| ---------- | ---- |
| +          | 相加 |
| -          | 相减 |
| *          | 相乘 |
| /          | 相除 |
| %          | 求余 |
| ++         | 自增 |
| --         | 自减 |

**2. 举例：**声明三个整形类型变量，三者之间进行交互运算并输出结果。

```go
var a1, a2, a3 = 1, 2, 3           // 变量的赋值
a4 := a1 + a2                      // 变量的运算
a5 := a1 + a2*a3
a6 := a1 + a2/a3
fmt.Println(a4, a5, a6)            // 语句&表达式输出
```

终端输出：

![](.\imgs\28.png)

结构解析：

```go
var a1, a2, a3 = 1, 2, 3          // 变量的赋值
```

 变量赋值：在Go语言中给变量的赋值可以分为三个阶段，先创建变量，在内存中开辟空间后再初始化变量，将变量初始化为underfined,最后再进行真正赋值。

```go
a4 := a1 + a2                       // 变量的算术运算
a5 := a1 + a2*a3
a6 := a1 + a2/a3
```

**变量的算术运算**：在go语言中，通过算术运算符的特性（只对数值类型的变量进行运算）将初始化赋值的变量进行算术运算，**得到新的数值赋值给临时变量，并存储到新地址中**从而完成整个计算。

#### 赋值运算符分析

**1. 结构：**

```go
[变量] 赋值运算符 [value]
```

结构分解：

![](.\imgs\29.png)

**解析：**在Go语言中，赋值运算符使用时，通过**将经过运算后的值或者是直接赋值的数据赋给指定的变量**。

**赋值运算符就是将某个运算后的值，赋给指定的变量。**

赋值一览表：

| 赋值运算符 | 描述                                           |
| ---------- | ---------------------------------------------- |
| =          | 简单的赋值运算符，将一个表达式的值赋给一个左值 |
| +=         | 相加后再赋值                                   |
| -=         | 相减后再赋值                                   |
| *=         | 相乘后再赋值                                   |
| /=         | 相除后再赋值                                   |
| %=         | 求余后再赋值                                   |
| <<=        | 左移后赋值                                     |
| >>=        | 右移后赋值                                     |
| &=         | 按位与后赋值                                   |
| ^=         | 按位异或后赋值                                 |
| \|=        | 按位或后赋值                                   |

**2. 举例：**现有两个变量，a 和 b，要求将其进行交换，最终打印结果。

```go
a := 2018                      // 变量的初始化赋值
b := 2022
num := a                       // 运算符的交换赋值
a = b
b = num
fmt.Printf("交换后 a = %v b = %v\n", a, b)   // 语句&表达式输出
```

终端输出：

![](.\imgs\30.png)

**结构解析：**

```go
a := 2018                    // 变量的初始化赋值
b := 2022     
```

**变量的初始化赋值：**Go语言是静态类型语言，因此**变量（variable）是有明确类型的**。编译器会检查函数调用中，变量类型的正确性。在数学概念中，变量表示没有固定值且可改变的数。但从计算机系统实现角度来看，变量是一段或多段用来存储数据的内存。**在此语句中通过短类型声明将变量声明并指定出变量数据存储的空间，然后通过赋值运算符将数值赋值给变量。**

```go
num := a                    // 赋值运算符的赋值
a = b
b = num
```

 **赋值运算符赋值：**通过运算符的特性将经过运算后的值或者是直接赋值的数据赋给指定的变量。

```go
 fmt.Printf("交换后 a = %v b = %v\n", a, b)     // 语句&表达式输出（函数体）
```

**函数体：**在函数中具有实现指定功能的代码块即为函数的函数体，在此函数体中实现的功能就是打印输出返回的结果，Println 函数是属于包 fmt 的函数。

### 4-2 关系与逻辑运算符分析

#### 关系运算符分析

**1. 结构：**

```go
变量 比较运算符 变量
```

**结构分解：**

![](.\imgs\31.png)

**解析：**关系运算符也叫比较运算符，在Go语言中，关系运算符的**结果都是bool型**，也就是运算的**结果要么是ture，要么是false**，而通过关系运算符形成的**关系表达式经常用在if结构的条件中或循环结构的条件中**。

**关系运算符一览：**

| 关系运算符 | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| ==         | 检查两个值是否相等，如果相等返回 True 否则返回 False。       |
| !=         | 检查两个值是否不相等，如果不相等返回 True 否则返回 False。   |
| >          | 检查左边值是否大于右边值，如果是返回 True 否则返回 False。   |
| <          | 检查左边值是否小于右边值，如果是返回 True 否则返回 False。   |
| >=         | 检查左边值是否大于等于右边值，如果是返回 True 否则返回 False。 |
| <=         | 检查左边值是否小于等于右边值，如果是返回 True 否则返回 False。 |

**2. 举例：**声明两个变量后再用关系运算符进行比较运算。

```go
var a1, a2 = 1, 2                // 变量的初始化赋值
fmt.Println(a1 == a2)            // 语句&表达式输出
```

终端输出：

![](.\imgs\32.png)

**结构分解：**

```go
var a1, a2 = 1, 2                   // 变量的初始化赋值
```

**变量的初始化赋值：**Go语言是静态类型语言，因此变量（variable）是有明确类型的。编译器会检查函数调用中，变量类型的正确性。在数学概念中，变量表示没有固定值且可改变的数。但从计算机系统实现角度来看，变量是一段或多段用来存储数据的内存。**在此语句中通过元组变量声明并指定出变量数据存储的空间，然后通过赋值运算符将数值赋值给变量。**

```go
fmt.Println(a1 == a2)          // 语句&表达式输出
```

**语句&表达式输出：**在函数中具有实现指定功能的代码块即为函数的函数体，在此函数体中实现的功能就是打印输出返回的结果，Println 函数是属于包 fmt 的函数。在表达式输出的过程中关系运算符实现比较运算，然后输出bool类型的结果。

**扩展：**如果我们将关系运算符中的**==**换成**=**时是否会产生相同的比较效果？

```go
fmt.Println(a1 = a2)                    // 语句&表达式输出
```

终端输出：

![](.\imgs\33.png)

**输出分析：在函数中具有实现指定功能的代码块即为函数的函数体**，在此函数体中实现的功能就是打印输出返回的结果，Println 函数是属于包 fmt 的函数。在表达式输出的过程中关系运算符实现比较运算，然后输出bool类型的结果。然而将**==**换成**=**时，表达式中的**a1 = a2**表达的意思时将**变量a2**的值赋值给**变量a1**，而不是将两个变量进行比较输出。

**结论：因此关系运算符中的运算符==不能换写成运算符=。**

#### 逻辑运算符分析

**1. 结构：**

```go
变量 逻辑运算符 变量
```

结构分解：

![](.\imgs\34.png)

**解析：**逻辑运算符**用于连接多个条件**（条件一般为关系表达式），**最终的结果也是一个bool值**。而在此结构中**逻辑运算符&&**也叫短路与，它代表的是如果第一个条件为false，则第二个条件不会判断，最终结果为false.与之对应的还有一个**逻辑运算符||**如果第一个条件为true,则第二个条件不会进行判断，最终结果为true.

**关系运算符一览：**

| 逻辑运算符 | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| &&         | 逻辑AND运算符。如果两边操作数都是true，则结果为true，否则为false |
| \|\|       | 逻辑OR运算符。如果两边操作数有一个true，则结果为true，否则为false |
| ！         | 逻辑NOT运算符。如果条件为true，则结果为false，否则为true     |

**2.** **举例**：当前有一个变量赋值为2018，当赋值大于2010小于2022年时则会输出“知链科技”。

```go
var age int = 2018                // 变量的初始化赋值
if age > 2010 && age < 2022 {     // 逻辑表达式判断
   fmt.Println("知链科技")         // 语句&表达式输出
}
```

**结构分解：**

```go
var age int = 2018           // 变量的初始化赋值
```

 **变量赋值**：在Go语言中变量通过**等号“=”**来赋值，通过“=”可以将指定的数据指给变量，从而使变量表示这个数据相应的内存地址，在之后的数据流转操作的过程中，通过使用变量操作，让变量所对应的数据进行交互处理从而完成数据的各类处理。

```go
if age > 2010 && age < 2022 {        // 逻辑表达式判断
   fmt.Println("知链科技")            // 语句&表达式输出
}
```

**逻辑语句&表达式输出**：逻辑语句表达式进行if判断，当条件满足时，逻辑语句运行成功，则函数体内的fmt.Println(...) 将字符串输出到控制台，并在最后自动增加换行字符 \n。函数内语句依照main函数内依次执行。

### 4-3 运算符优先级

#### 运算符优先级分析

**1. Go语言运算符优先级和结合性一览表：**

| 优先级 | 分类           | 运算符                                         | 结合性   |
| ------ | -------------- | ---------------------------------------------- | -------- |
| 1      | 逗号运算符     | ,                                              | 从左到右 |
| 2      | 赋值运算符     | =、+=、-=、*=、/=、 %=、 >=、 <<=、&=、^=、\|= | 从右到左 |
| 3      | 逻辑或         | \|\|                                           | 从左到右 |
| 4      | 逻辑与         | &&                                             | 从左到右 |
| 5      | 按位或         | \|                                             | 从左到右 |
| 6      | 按位异或       | ^                                              | 从左到右 |
| 7      | 按位与         | &                                              | 从左到右 |
| 8      | 相等/不等      | ==、!=                                         | 从左到右 |
| 9      | 关系运算符     | <、<=、>、>=                                   | 从左到右 |
| 10     | 位移运算符     | <<、>>                                         | 从左到右 |
| 11     | 加法/减法      | +、-                                           | 从左到右 |
| 12     | 乘法/除法/取余 | *（乘号）、/、%                                | 从左到右 |
| 13     | 单目运算符     | !、*（指针）、& 、++、--、+（正号）、-（负号） | 从右到左 |
| 14     | 后缀运算符     | ( )、[ ]、->                                   | 从左到右 |

**释义：**有些运算符拥有较高的优先级，**二元运算符的运算方向均是从左至右。只有单目运算，赋值运算是从右往左运算的。**

**2. 举例：**有三个变量x,y和z，当它们通过交互运算后得到新的结果再赋值的给变量sum再进行输出。

```go
var x = 8                   // 变量的声明
var y = 8
var z = 7
sum := (x + y) * z          // 变量的运算赋值
fmt.Printf("赋值后sum= %v", sum)  // 语句&表达式输出
```

终端输出：

![](.\imgs\35.png)

**结构分析：**

```go
var x = 8                            // 变量的声明
var y = 8
var z = 7
```

**变量的声明赋值：**Go语言是静态类型语言，因此变量（variable）是有明确类型的。编译器会检查函数调用中，变量类型的正确性。在数学概念中，变量表示没有固定值且可改变的数。但从计算机系统实现角度来看，变量是一段或多段用来存储数据的内存。

```go
sum := (x + y) * z             // 变量的运算赋值
```

**变量的运算赋值：**在Go语言中，运算符优先级，是描述在计算机运算计算表达式时执行运算的先后顺序。 **先执行具有较高优先级的运算**，然后执行较低优先级的运算。运算符优先级的大致顺序为：单目运算符 > 算术运算符 > 位移运算符 > 关系运算符 > 位运算符 > 逻辑运算符 > 赋值运算符 ，在此语句中，先对优先级高的变量相加，再将得到的结果与优先级较低的乘法进行运算，获得结果后赋值给变量sum。

```go
fmt.Printf("赋值后sum= %v", sum)       // 语句&表达式输出（函数体语句）
```

**函数体语句：**在函数中具有实现指定功能的代码块即为函数的函数体，在此函数体中实现的功能就是打印输出返回的结果，Println 函数是属于包 fmt 的函数。

------



## 5.基础数据类型

### 5-1 布尔类型

#### 布尔类型分析

**1. 结构：**

```go
var [name] bool = [value]
```

结构分解：

![](.\imgs\36.png)

**解析：**布尔类型的数据声明关键字为bool，且布尔数据类型可取值一般只有两种true和false两种，当数据类型取值为true时，则代表为真，表示成立，二进制时则表示1为真，当数据类型取值为false，则代表为假，表示不成立，二进制时则表示0为假。

**2. 举例：**声明一个布尔类型且代表为真输出为“b = false”。

```go
var b bool = false                     // 布尔类型的初始化赋值
fmt.Println("b=", b)                   // 语句&表达式输出
```

终端输出：

![](.\imgs\37.png)

**结构解析：**

```go
var b bool = false              // 布尔类型的初始化赋值
```

**布尔类型的初始化赋值**：Go语言是静态类型语言，因此变量（variable）是有明确类型的。变量**通过关键字Var来命名标识符再通过明确数据类型来完成变量的完整声明的过程。（在声明过程中数据类型要明确写为bool类型）**编译器也会检查函数调用中，来验证变量类型的正确性。

```go
fmt.Println("b=", b)           // 语句&表达式输出
```

**语句&表达式输出**：fmt.Println(...) 可以将字符串输出到控制台，并在最后自动增加换行字符 \n。函数内语句依照main函数内依次执行。

### 5-2 整数类型

#### 整型类型分析

**1. 结构：**

```go
var [name] [整型数据类型] = [value]
```

结构分解：

![](.\imgs\38.png)

**解析：**整型是所有编程语言里最基础的数据类型。且需要注意的是整数类型的声明，都是按照Go的声明规则进行声明，需要注意的是，**不同大小的整型类型是不能直接比较**，**不能直接运算**，如果需要**对两个不同类型的值执行运算操作，需要进行类型转换**。

**整数类型释义**：Go语言严格来说**一共有9种不同大小的整型**，无符号四种，有符号四种，还有一种uintptr类型（多用于底层编程）。其中int8、int16、int32和int64这四种不同大小的有符号整型数类型分别对应8、16、32、64b大小的有符号整型数；对应的无符号整型数分别是uint8、uint16、uint32、uint64；除了指定整数位数以外，也**可以直接使用int和uint类型**。**Go语言可以根据不同平台的实现对int进行调整，既可以是int32，也可以是int64，uint同理**。

**2. 整数基础类型一览：**

| 类型    | 长度（单位：字节） | 说明                                 | 值范围                                   | 默认值 |
| ------- | ------------------ | ------------------------------------ | ---------------------------------------- | ------ |
| int     | 1                  | 带符号8位整型                        | -128~127                                 | 0      |
| uint8   | 1                  | 无符号8位整型，与 byte 类型等价      | 0~255                                    | 0      |
| int16   | 2                  | 带符号16位整型                       | -32768~32767                             | 0      |
| uint16  | 2                  | 无符号16位整型                       | 0~65535                                  | 0      |
| int32   | 4                  | 带符号32位整型，与 rune 类型等价     | -2147483648~2147483647                   | 0      |
| uint32  | 4                  | 无符号32位整型                       | 0~4294967295                             | 0      |
| int64   | 8                  | 带符号64位整型                       | -9223372036854775808~9223372036854775807 | 0      |
| uint64  | 8                  | 无符号64位整型                       | 0~18446744073709551615                   | 0      |
| int     | 32位或64位         | 与具体平台相关                       | 与具体平台相关                           | 0      |
| uint    | 32位或64位         | 与具体平台相关                       | 与具体平台相关                           | 0      |
| uintptr | 与对应指针相同     | 无符号整型，足以存储指针值的未解释位 | 32位平台下为4字节，64位平台下为8字节     | 0      |

**3. 举例：**声明一个整数类型并赋值为100输出。

```go
var z1 int                    // 整数类型变量的声明
z1 = 100                      // 变量的赋值  
fmt.Print("数据类型z1=",z1)     // 语句&表达式输出
```

终端输出：

![](.\imgs\39.png)

**结构解析：**

```go
var z1 int          // 整数类型变量的声明
```

**整数类型变量的声明：**Go语言是静态类型语言，因此变量（variable）是有明确类型的。**整数类型的声明，都是按照Go的声明规则进行声明，通过关键字Var来命名标识符再通过明确变量类型来完成整数类型变量的完整声明的过程。此外Go语言可以根据不同平台的实现对int进行调整，既可以是int32，也可以是int64，uint同理。**编译器也会检查函数调用中，来验证变量类型的正确性。

```go
z1 = 100                // 变量的赋值 
```

**变量赋值：**在Go语言中变量通过等号“=”来赋值，通过“=”可以将指定的数据指给变量，从而使变量表示这个数据相应的内存地址，在之后的数据流转操作的过程中，通过使用变量操作，让变量所对应的数据进行交互处理从而完成数据的各类处理。

```go
fmt.Print("数据类型z1=",z1)       // 语句&表达式输出
```

**语句&表达式输出**：fmt.Println(...) 可以将字符串输出到控制台，并在最后自动增加换行字符 \n。函数内语句依照main函数内依次执行。

### 5-3 字符串类型

#### 字符串类型分析

**1. 结构：**

```go
var [name] string = [赋值]
```

**赋值：**字符串是一种值类型，所赋值是一个**不可改变的字节序列**。

结构分解：

![](.\imgs\40.png)

**解析：**Go语言中的**字符串以原生数据类型出现**，使用字符串就像使用其他原生数据类型（int、bool、float32、float64 等）一样。Go 语言里的**字符串的内部实现使用UTF-8编码**。**字符串的值为双引号(")中的内容**，**可以在Go语言的源码中直接添加非ASCII码字符**。另外**字符串是一种值类型，且值不可变**，即创建某个文本后将**无法再次修改这个文本的内容**，更深入地讲，**字符串是字节的定长数组**。

**2. 举例**：声明一个字符串数据类型并输出：“知链”。

```go
var string1 string = "知链"             // 字符串类型变量的初始化
fmt.Println(string1)                   // 语句&表达式输出
```

终端输出：

![](.\imgs\41.png)

**结构解析：**

```go
var string1 string = "知链"        // 字符串类型变量的初始化
```

**字符串类型变量的初始化：**Go语言是静态类型语言，因此变量（variable）是有明确类型的。**字符串是一个或多个字符(字母，数字，符号)的序列，可以是常数或变量。 且字符串由Unicode组成 ，是不可变的序列，这意味着它们是不变的。（当字符为 ASCII 码表上的字符时则占用 1 个字节，其它字符根据需要占用 2-4 个字节）。通过关键字var来命名标识符再通过明确变量类型来完成字符串类型变量的完整声明的过程。**编译器也会检查函数调用中，来验证变量类型的正确性。**从另一方面讲字符串是一种值类型，且值不可变，即创建某个文本后将无法再次修改这个文本的内容，更深入地讲，字符串是字节的定长数组。**

```go
fmt.Println(string1)             // 语句&表达式输出
```

**语句&表达式输出**：fmt.Println(...) 可以将字符串输出到控制台，并在最后自动增加换行字符 \n。函数内语句依照main函数内依次执行。

### 5-4 浮点类型

#### 浮点类型分析

 **1. 结构：**

```go
var [name] [浮点类型] = [value]
```

**结构分解：**

![](.\imgs\42.png)

**解析**：**Golang 浮点类型有固定的范围和字段长度，不受具体OS(操作系统)的影响**。且在Go语言中的**默认声明为float64类型**，浮点数在机器中**存放时主要分成三部分存储**，分别为**符号位**,**指数位**，**尾数位**，而**在具体的存储过程中精度会有丢失**。这些浮点数类型的取值范围可以从很微小到很巨大。**浮点数的范围极限值可以在math包找到**。常量 **math.MaxFloat32**表示float32能表示的最大数值，大约是 3.403e38；对应的**math.MaxFloat64**常量大约是 1.798e308。它们分别能表示的最小值近似为 -3.403e38和 -1.798e308。

**2. 举例：**声明一个32位浮点类型并输出结果：“2018.0516”。

```go
var f float32 = 2018.0516          // 浮点类型变量的初始化
fmt.Println(f) 
```

终端输出：

![](.\imgs\43.png)

**结构解析：**

```go
var f float32 = 2018.0516    // 浮点类型变量的初始化
```

**浮点类型变量的初始化：**Go语言是静态类型语言，因此变量（variable）是有明确类型的。**浮点类型变量的初始化通过关键字var来命名标识符再通过明确浮点类型变量类型来完成变量的完整声明的过程。且Go语言支持两种浮点型数据格式：float32 和 float64。这两种浮点型数据格式遵循 IEEE 754 标准。一个float32类型的浮点数可以提供大约6个十进制数的精度，而float64则可以提供约15个十进制数的精度。**同时编译器也会检查函数调用中，来验证变量类型的正确性。

```go
fmt.Println(f)           // 语句&表达式输出
```

**语句&表达式输出**：fmt.Println(...) 可以将字符串输出到控制台，并在最后自动增加换行字符 \n。函数内语句依照main函数内依次执行。

### 5-5 字符类型

#### 字符类型分析

**1. 结构：**

```go
var [name] [字符数据类型] = [value]
```

结构分解：

![](.\imgs\44.png)

**解析：Golang中没有专门的字符类型**，如果**要存储单个字符(字母)，一般使用byte来保存**。<u>字符串就是一串固定长度的字符连接起来的字符序列</u>。**Go的字符串是由单个字节连接起来的**。也就是说对于传统的字符串是由字符组成的，而**Go的字符串不同，它是由字节组成的**。**go里面没有字符类型（char），而是使用byte（uint8）和rune（int32）来代表字符。声明一个字符时，默认是rune类型，除非特别定义。** **一个string变量既可以被拆分为字符，也可以被拆分为字节**；**前者使用rune[]切片表示**，**后者使用byte[]切片表示**。**一个rune值就是代表一个字符**，在输入输出中经常看到类似’\U0001F3A8’，’\u2665’的就是一个rune字符（unicode字符），其中的每位都是一个16进制数。

总结：Go没有专门的类似于C语言的字符类型char；Go里面用byte(别名uint8)和rune(别名int32)代表字符；Go声明一个字符默认是rune类型；string类型变量可以分为字符(rune[]切片)或者字节(byte[]切片)；Go的字符串由字节组成；

**2. 举例：**声明一个字符类型的字节，要求输出ASCII 码表中的字符A。

```go
var a byte = 65                   // 字符类型变量的初始化
fmt.Printf("a = %c\n", a)         // 语句&表达式输出
```

终端输出：

![](.\imgs\45.png)

**结构解析：**

```go
var a byte = 65              // 字符类型变量的初始化  
```

**字符类型变量的初始化：**Go语言是静态类型语言，因此变量（variable）是有明确类型的。**变量通过关键字var来命名标识符再通过明确变量类型来完成变量的完整声明的过程。**编译器也会检查函数调用中，来验证变量类型的正确性。**go没有没有专门的字符类型，如果要存储单个的字符或字母，一般用byte来保存。Byte（即是uint8），8位，0~255，一个字节 ，表示的是 ASCII 码表中的一个字符。如果我们保存的字符在ASCII表中，比如[0-1,a-z,A-Z...]，可以直接用byte。如果保存的字符对应码值大于255，此时可以用int型保存。但如果我们需要字符格式输出，则必须用格式化输出。**

```go
fmt.Printf("a = %c\n", a)       // 语句&表达式输出        
```

**语句&表达式输出**：fmt.Println(...) 可以将字符串输出到控制台，并在最后自动增加换行字符 \n。函数内语句依照main函数内依次执行。

------



## 6.基础数据类型转换

### 6-1 整型间的转换

#### 整数间的类型转换分析

**1. 结构：**

```go
[转换后变量] = 整数数据类型([转换前变量])
```

结构分解：

![](.\imgs\46.png)

**解析：**整型之间的数据转换，使用的是官方标准api，且**只有相同底层类型的变量之间可以进行相互转换**（如将 int16 类型转换成 int32 类型）。由于**Go语言不存在隐式类型转换，因此所有的类型转换都必须显式的声明**。

**2. 举例：**声明赋值三个不同整数数据类型的变量sum1,sum2,sum3,并将他们的数据转换后输出。

```go
var sum1 int = 20               // 变量的初始化赋值
var sum2 int32 = 30
var sum3 int64 = 50
sum4 := int16(sum1)             // 变量的数据类型转换
sum5 := int8(sum2)
sum6 := int32(sum3)
fmt.Println(sum4, sum5, sum6)   // 语句&表达式输出
```

终端输出：

![](.\imgs\47.png)

**结构解析：**

```go
var sum1 int = 20             // 变量的初始化赋值
var sum2 int32 = 30
var sum3 int64 = 50
```

**变量的初始化赋值：**在Go语言中给变量的赋值可以分为三个阶段，**先创建变量，在内存中开辟空间后再初始化变量，将变量初始化为underfined,最后再进行真正赋值。**变量**通过关键字var来命名标识符再通过明确变量类型来完成变量的完整声明的过程。**编译器也会检查函数调用中，来验证变量类型的正确性。

```go
sum4 := int16(sum1)             // 变量的数据类型转换
sum5 := int8(sum2)
sum6 := int32(sum3)
```

**变量的数据类型转换**：在go语言中，**类型转换是用于将一种数据类型的变量转换为另外一种类型的变量的方法，**在转换的过程中**要指明正确的转换数据类型**。但不是所有数据类型都能转换的。**只有相同底层类型的变量之间可以进行相互转换**（如将 int16 类型转换成 int32 类型），不同底层类型的变量相互转换时会引发编译错误。

```go
fmt.Println(sum4, sum5, sum6)      // 语句&表达式输出
```

**语句&表达式输出**：fmt.Println(...) 可以将字符串输出到控制台，并在最后自动增加换行字符 \n。函数内语句依照main函数内依次执行。

### 6-2 字符类型与整型的转换

#### 字符串类型与整数类型的转换分析

1.结构：

```go
int, err := strconv.Atoi(string)
```

释义：**int为整数类型变量名**，**err是返回值判断**（当**err换为下划线_则表示不进行返回值判断**），strconv.Atoi表示使用**strconv** **包中的Atoi()函数**，**Go语言的 strconv 包提供了一个Atoi()函数，**该函数**等效于ParseInt(str string，base int，bitSize int)用于将字符串类型转换为int类型**。要访问Atoi()函数，您需要在程序中导入 strconv 软件包。最后的**string表示被转换的字符串类型变量**。

结构分解：

![](.\imgs\48.png)

**解析：将字符串类型的变量通过strconv包中的特殊函数转换成整数类型的变量，strconv 包提供了一个Atoi()函数，该函数等效于ParseInt(str string，base int，bitSize int)用于将字符串类型转换为int类型。**

**举例：有一个字符串类型变量的值为“20180516”，请将它试着转换为整数类型并输出。**

```go
package main                    // 包的声明
import (                        // 引入包
   "fmt"
   "strconv"
)

func main() {                  // 函数
   num := "20180516"           // 变量的初始化赋值
   var data, _ = strconv.Atoi(num)  // 变量的数据类型转换
   fmt.Println(data)                // 语句&表达式输出
}
```

终端输出：

![](.\imgs\49.png)

结构解析：

```go
package main                 // 包的声明
```

**包声明**：package main 定义了包名。它在源文件中指明了这个文件属于那个包。

```go
import (                           // 引入包
   "fmt"
   "strconv"
)
```

**引入包**： import （"fmt""strconv"） 告诉 Go 编译器这个程序需要使用 fmt 包和strconv 包，fmt 包实现了格式化 IO（输入/输出）的函数。Go语言的 strconv 包提供了一个Atoi()函数，该函数等效于ParseInt(str string，base int，bitSize int)用于将字符串类型转换为int类型。要访问Atoi()函数，您需要在程序中导入 strconv 软件包。

```go
func main() {                  // 函数
```

**函数**：func main() 是程序开始执行的函数。main 函数是每一个可执行程序所必须包含的，一般来说都是在启动后第一个执行的函数（如果有 init() 函数则会先执行该函数）。函数执行开始后，函数内语句依照main（）函数内依次执行。

```go
num := "20180516"         // 变量的初始化赋值
```

**变量的初始化赋值：**在Go语言中给变量的赋值可以分为三个阶段，**先创建变量，在内存中开辟空间后再初始化变量，将变量初始化为underfined,最后再进行真正赋值。**变量**通过关键字Var来命名标识符再通过明确变量类型来完成变量的完整声明的过程。**编译器也会检查函数调用中，来验证变量类型的正确性。

```go
var data, _ = strconv.Atoi(num)      // 变量的数据类型转换
```

**变量的数据类型转换：**类型转换是用于将一种数据类型的变量转换为另外一种类型的变量的方法，在转换的过程中要指明正确的转换数据类型。**将字符串类型的变量通过strconv包中的特殊函数转换成整数类型的变量，strconv 包提供了一个Atoi()函数，该函数等效于ParseInt(str string，base int，bitSize int)用于将字符串类型转换为int类型。**

```go
fmt.Println(data)            // 语句&表达式输出
```

**语句&表达式输出**：fmt.Println(...) 可以将字符串输出到控制台，并在最后自动增加换行字符 \n。函数内语句依照main函数内依次执行。

#### **int整数类型转字符串类型分析**

1.结构：

```go
string, err := strconv. Itoa (int)
```

结构分解：

![](.\imgs\50.png)

**解析：strconv 包提供了字符串与简单数据类型之间的类型转换功能。其中strconv 包提供了一个Itoa()函数，将int类型转换为字符串类型。**

**举例：从上个例子中获得的int整数类型的结果再重新转换成字符串。**

```go
var string, _ = strconv.Itoa(data)       // 变量的数据类型转换
fmt.Println(string)                      // 语句&表达式输出
```

终端输出：

![](.\imgs\51.png)

**结构解析：**

```go
var string, _ = strconv.Itoa(data)   // 变量的数据类型转换
```

**变量的数据类型转换：**类型转换是用于将一种数据类型的变量转换为另外一种类型的变量的方法，在转换的过程中要指明正确的转换数据类型。**将整数类型的变量通过strconv包中的特殊函数转换成字符串类型的变量，strconv 包提供了一个Itoa()函数，该函数用于将int类型转换为字符串类型。**

```go
fmt.Println(string)        // 语句&表达式输出
```

**语句&表达式输出**：fmt.Println(...) 可以将字符串输出到控制台，并在最后自动增加换行字符 \n。函数内语句依照main函数内依次执行。

### 6-3 字节类型与字符串类型的转换

#### **字符串类型转换成字节数组分析**

1.结构：

```go
字节数组变量 := 字节数组数据类型(字符串变量)
```

**结构分解：**

![](.\imgs\52.png)

**解析：**在字符串和字节切片（数组）之间进行转换时，**会得到一个全新的切片**，其中包含与字符串相同的字节，反之亦然。通过[]btye字节数组类型的转换将字符串变量变成字节数组变量。需要注意的是**转换不会修改数据**的，但两者之间的不同是，字符串始终是不可变，然而**字节片是可以修改的**。

**举例:声明一个字符串类型的变量其赋值为“知链区块链人才培养摇篮”，将它转换成字节数组后再输出。**

```go
var string1 string = "知链区块链人才培养摇篮"     // 变量的初始化赋值
result1 := []byte(string1)                   // 变量的数据类型转换
fmt.Println(result1)                         // 语句&表达式输出
```

**终端输出：![](.\imgs\53.png)**

**结构解析：**

```go
var string1 string = "知链区块链人才培养摇篮"  // 变量的初始化赋值
```

**变量的初始化赋值：**在Go语言中给变量的赋值可以分为三个阶段，**先创建变量，在内存中开辟空间后再初始化变量，将变量初始化为underfined,最后再进行真正赋值。**变量**通过关键字Var来命名标识符再通过明确变量类型来完成变量的完整声明的过程。**编译器也会检查函数调用中，来验证变量类型的正确性。

```go
result1 := []byte(string1)         // 变量的数据类型转换
```

**变量的数据类型转换：**类型转换是用于将一种数据类型的变量转换为另外一种类型的变量的方法，在转换的过程中要指明正确的转换数据类型。通过[]btye字节数组类型的转换将字符串变量的赋值变成字节数组变量类型。

```go
fmt.Println(result1)        // 语句&表达式输出
```

**语句&表达式输出**：fmt.Println(...) 可以将字符串输出到控制台，并在最后自动增加换行字符 \n。函数内语句依照main函数内依次执行。

#### **字节数组转换成字符串类型**

**结构：**

```go
字符串变量 := 字符串数据类型(字节数组变量)
```

**结构分解：**

![](.\imgs\54.png)

**解析：**在字符串和字节切片（数组）之间进行转换时，会得到一个全新的切片，其中包含与字符串相同的字节，反之亦然。**通过string字符串数据类型的转换将字节数组变量变成字符串变量。需要注意的是如果只将字节的一部分转换为字符串，你将会获得一个新的字符串，其中包含与该片段相同的字节。**

**举例:将上个例子中输出的字节数组再重新转换为字符串类型。**

```go
result2 := string(result1)    // 变量的数据类型转换
fmt.Println(result2)          // 语句&表达式输出
```

**终端输出：**

![](.\imgs\55.png)

**结构解析：**

```go
result2 := string(result1)        // 变量的数据类型转换
```

**变量的数据类型转换：**类型转换是用于将一种数据类型的变量转换为另外一种类型的变量的方法，在转换的过程中要指明正确的转换数据类型。**通过string字符串数据类型的转换将字节数组变量变成字符串变量。需要注意的是如果只将字节的一部分转换为字符串，你将会获得一个新的字符串，其中包含与该片段相同的字节。**

```go
fmt.Println(result2)           // 语句&表达式输出
```

**语句&表达式输出**：fmt.Println(...) 可以将字符串输出到控制台，并在最后自动增加换行字符 \n。函数内语句依照main函数内依次执行。

### 6-4 字节类型与整型的转换

#### 字节类型与整型的转换分析

**1. 基本介绍：**

字节与整型的转换通过Golang自带的**binary.BigEndian 库**实现，int和byte的数组转换以大端形式存储。

**释义：**一般来说网络传输的字节序，可能是大端序或者小端序，取决于软件开始时通讯双方的协议规定。TCP/IP协议RFC1700规定使用“大端”字节序为网络字节序，开发的时候需要遵守这一规则。**默认golang是使用大端序**。

**扩展：**

1、大端模式(Big endian)：将高序字节存储在起始地址(按照从低地址到高地址的顺序存放数据的高位字节到低位字节)

2、小端模式(Little endian)：将低序字节存储在起始地址(按照从低地址到高地址的顺序存放据的低位字节到高位字节)

**2. 举例：**将一个整数类型转换为字节类型的go语言程序。

```go
// TODO 整型转字节
var x int32 = 20180516         // 变量的初始化赋值
bytesBuffer := bytes.NewBuffer([]byte{})      // 数据转换处理
binary.Write(bytesBuffer, binary.BigEndian, x)
result3 := bytesBuffer.Bytes()                // 函数赋值
fmt.Println(result3)                          // 语句&表达式输出
```

**终端输出：**

![](.\imgs\56.png)

**结构解析：**

```go
// TODO 整型转字节
var x int32 = 20180516            // 变量的初始化赋值
```

**变量的初始化赋值：**在Go语言中给变量的赋值可以分为三个阶段，**先创建变量，在内存中开辟空间后再初始化变量，将变量初始化为underfined,最后再进行真正赋值。**变量**通过关键字Var来命名标识符再通过明确变量类型来完成变量的完整声明的过程。**编译器也会检查函数调用中，来验证变量类型的正确性。

```go
bytesBuffer := bytes.NewBuffer([]byte{})     // 数据转换处理
binary.Write(bytesBuffer, binary.BigEndian, x)
```

**数据转换处理：**在输出的过程中，通过短类型声明来初始化一个**临时变量用以存储整型数据** ，而在go语言储存过程中，是可以通过Golang自带的binary.BigEndian 库实现，int和byte的数组转换以大端形式存储。

```go
result3 := bytesBuffer.Bytes()       // 函数赋值
```

**函数赋值：**在输出的过程中，通过短类型声明来初始化一个临时变量获取在数据存储中储存的字节数据。

```go
fmt.Println(result3)            // 语句&表达式输出
```

**语句&表达式输出**：fmt.Println(...) 可以将字符串输出到控制台，并在最后自动增加换行字符 \n。函数内语句依照main函数内依次执行。

**3.扩展思考**：那是否我们能通过该Golang自带的binary.BigEndian 库实现**字节数据的整型回转**。

**利用上例进行扩展：**

```go
// TODO 字节转整型
bytesBuffer2 := bytes.NewBuffer(result3)     // 变量初始化
var tmp int32                                // 变量声明
binary.Read(bytesBuffer2, binary.BigEndian, &tmp) // 数据读取处理
result4 := int32(tmp)                                         
fmt.Println(result4)                              // 语句&表达式输出
```

**终端输出：**

![](.\imgs\57.png)

**结构解析：**

```go
// TODO 字节转整型
bytesBuffer2 := bytes.NewBuffer(result3)      // 变量初始化
```

**变量的初始化：**在Go语言中给变量的赋值可以分为三个阶段，**先创建变量，在内存中开辟空间后再初始化变量，将变量初始化为underfined,最后再进行真正赋值。需要注意的是对该**变量**赋值是通过Buffer函数来获取。**

```go
var tmp int32                         // 变量声明
```

**变量声明：**Go语言是静态类型语言，因此变量（variable）是有明确类型的。变量通过关键字Var来命名标识符再通过明确变量类型来完成变量的完整声明的过程。编译器也会检查函数调用中，来验证变量类型的正确性。

```go
binary.Read(bytesBuffer2, binary.BigEndian, &tmp)     // 数据读取处理
result4 := int32(tmp)        
```

**数据读取处理：**在数据获取的过程中，**使用binary包中的Read方法读取数据**，但又因为该方法**读取数据时必须读取到一个指定的指针中**，因此我们声明了一个整型变量tmp,并通过它来获取读出的数据。

```go
fmt.Println(result4)           // 语句&表达式输出
```

**语句&表达式输出**：fmt.Println(...) 可以将字符串输出到控制台，并在最后自动增加换行字符 \n。函数内语句依照main函数内依次执行。

------



## 7.时间的输出与转换

### 7-1 时间格式化输出

#### 时间格式化输出分析

**1. 结构：**

```go
关键字 变量名 = time函数
```

结构分解：

![](.\imgs\58.png)

**解析**：由于Go语言中**time包**为我们提供了一个**数据类型 time.Time（作为值使用）**以及显示和测量时间和日期的功能函数。在go语言内置的time库中，**time.Now() 函数能直接获取当前的时间**，但**Date()方法也可以获取当前时间**，而相对来说time.Now()用起来比较简单。

**2. 举例：**请用一个程序实现直接获取当前时间。

```go
var nowTime = time.Now()            // 变量的初始化
fmt.Println(nowTime)                // 语句&表达式输出
```

终端输出：

![](.\imgs\59.png)

**结构解析：**

```go
var nowTime = time.Now()             // 变量的初始化 
```

**变量的初始化赋值：**在Go语言中给变量的赋值可以分为三个阶段，**先创建变量，在内存中开辟空间后再初始化变量，将变量初始化为underfined,最后再进行真正赋值。与之前不一样的是，该变量赋值是通过time.Now()函数获得赋值，另外变量通过关键字Var来命名标识符再通过明确变量类型来完成变量的完整声明的过程。**

```go
fmt.Println(nowTime)                 // 语句&表达式输出  
```

**语句&表达式输出**：fmt.Println(...) 可以将字符串输出到控制台，并在最后自动增加换行字符 \n。函数内语句依照main函数内依次执行。

### 7-2 时间转时间戳

#### 时间与时间戳转换分析

**1. 结构：**

```go
关键字 变量名1 = 变量名2.time函数
```

结构分解：

![](.\imgs\60.png)

**解析**：由于Go语言中time包为我们提供了一个数据类型 time.Time（作为值使用）以及显示和测量时间和日期的功能函数。在go语言内置的time库中，time.Now() 函数能直接获取当前的时间，但同时它还存在一个**Unix()函数，能直接将获得的时间转换成时间戳输出**。

**2. 举例：**请用一个程序实现直接获取当前时间并实现时间戳转换

```go
var Time2 = time.Now()             // 变量的初始化
var TimeStamp = Time2.Unix()       // 变量的函数转换
fmt.Println(TimeStamp)             // 语句&表达式输出
```

终端输出：

![](.\imgs\61.png)

**结构解析：**

```go
var Time2 = time.Now()           // 变量的初始化
```

**变量的初始化赋值：**在Go语言中给变量的赋值可以分为三个阶段，**先创建变量，在内存中开辟空间后再初始化变量，将变量初始化为underfined,最后再进行真正赋值。与之前不一样的是，该变量赋值是通过time.Now()函数获得赋值，另外变量通过关键字Var来命名标识符再通过明确变量类型来完成变量的完整声明的过程。**

```go
var TimeStamp = Time2.Unix()      // 变量的函数转换
```

 **变量的函数转换：在go语言中，**类型转换是用于将一种数据类型的变量转换为另外一种类型的变量的方法，**在转换的过程中要指明正确的转换数据类型**。**在go语言内置的time库中，time.Now() 函数获取当前的时间后，通过time包中的函数Unix()将获取的时间直接转换成时间戳。**

```go
fmt.Println(TimeStamp)        // 语句&表达式输出(函数体输出)  
```

**语句&表达式输出（函数体输出）**：fmt.Println(...) 可以将字符串输出到控制台，并在最后自动增加换行字符 \n。函数内语句依照main函数内依次执行。

**那如果我们要将时间戳转换为时间函数，依据上一个例子实现时间戳转换为时间的过程。**

```go
var timeObj = time.Unix(TimeStamp, 0)            // 变量的函数转换
fmt.Println(timeObj)                            // 语句&表达式输出
```

终端输出：

![](.\imgs\62.png)

**结构解析：**

```go
var timeObj = time.Unix(TimeStamp, 0)       // 变量的函数转换
```

**变量的函数转换：在go语言中，这里的Unix函数和time对象的Unix方法是两个东西，这里的Unix函数有两个入参，是将时间戳转为time对象：时间戳====>time对象；**而time的Unix（）方法没有入参，是将time对象转为时间戳：time对象====>时间戳，常用的写法是**time.Now().Unix()打印时间戳**。

```go
fmt.Println(TimeStamp)      // 语句&表达式输出  
```

**语句&表达式输出**：fmt.Println(...) 可以将字符串输出到控制台，并在最后自动增加换行字符 \n。函数内语句依照main函数内依次执行。

------



## 8.区块哈希实现

### 8-1 哈希的实现导入

运用SHA256 Hash实现生成Data的hash值

### 8-2 哈希的实现

#### hash实现

```go
package main

import (
	"crypto/sha256"
	"encoding/hex"
	"fmt"
)

func main() {
	var data = "hash"
	hash := sha256.Sum256([]byte(data))
	fmt.Printf("%+v\n", hash) // 字节形式

	hash2 := hex.EncodeToString(hash[:])
	fmt.Printf("%s", hash2) // 转换为string
}
```

运行结果：

![](.\imgs\63.png)

### 8-3 区块哈希的实现导入

#### 区块哈希模型

```
chainHigh(区块高度):
PrevBlockHash(前置hash):
Nonce(随机数):
Version(版本号):
MerKleRoot(默克尔根):
TimeStamp(时间戳):
Difficulty(难度值):
Data(数据):
Hash(区块hash):
```

### 8-4 区块哈希的实现

```go
package main

import (
	"crypto/sha256"
	"encoding/hex"
	"fmt"
	"math/rand"
	"strconv"
	"time"
)


/*
//	Hash string  // 哈希
//	PrevBlockHash string // 前置哈希
//	ChainHigh int // 区块高度
//	Version int // 版本号
//	MerKleRoot string // 默克尔树
//	TimeStamp int64 // 时间戳
//	Difficulty int // 难度值
//	Nonce int // 随机数
//	Data string // 数据
*/

func main() {
	PrevBlockHash := "d04b98f48e8f8bcc15c6ae5ac050801cd6dcfd428fb5f9e65c4e16e7807340fa"
	ChainHigh := 1
	Version := 1.0
	MerKleRoot := "7e79b52807201de9611f500f68a3b5fae43e719665ca1b534ca841e799a8d6ed"
	TimeStamp := time.Now().Unix()
	Difficulty := 1
	Nonce := rand.Intn(100)
	Data := "知链"
	var s = PrevBlockHash + strconv.FormatInt(int64(Version), 10) +
		string(ChainHigh) + MerKleRoot +
		string(TimeStamp) + string(Difficulty) + string(Nonce) + Data
	h := sha256.Sum256([]byte(s))
	fmt.Printf("%+v\n", h) // 字节形式

	Hash := hex.EncodeToString(h[:])
	fmt.Printf("%s", Hash) // 转换为string
}
```

运行结果：

![](.\imgs\64.png)

------

> © 2023 iWyh2
