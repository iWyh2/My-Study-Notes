# go mod详解

## 什么是go.mod?

Go.mod是Golang1.11版本新引入的官方包管理工具用于解决之前没有地方记录依赖包具体版本的问题，方便依赖包的管理。

Go.mod其实就是一个Modules，关于Modules的官方定义为：

Modules是相关Go包的集合，是源代码交换和版本控制的单元。go命令直接支持使用Modules，包括记录和解析对其他模块的依赖性。Modules替换旧的基于GOPATH的方法，来指定使用哪些源文件。

**Modules和传统的GOPATH不同，不需要包含例如src，bin这样的子目录，一个源代码目录甚至是空目录都可以作为Modules，只要其中包含有go.mod文件**

------



## 如何使用go.mod?

1. 首先将go的版本升级为1.11以上

2. 设置GO111MODULE

   GO111MODULE有三个值：off, on和auto（默认值）。

   * GO111MODULE=off，go命令行将不会支持module功能，寻找依赖包的方式将会沿用旧版本那种通过vendor目录或者GOPATH模式来查找。
   * GO111MODULE=on，go命令行会使用modules，而一点也不会去GOPATH目录下查找。
   * GO111MODULE=auto，默认值，go命令行将会根据当前目录来决定是否启用module功能。这种情况下可以分为两种情形：
     当前目录在GOPATH/src之外且该目录包含go.mod文件
     当前文件在包含go.mod文件的目录下面。

------



## go mod命令：

golang 提供了 go mod命令来管理包。go mod 有以下命令：

![](https://img-blog.csdnimg.cn/20190729135250528.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTAwMzIyOQ==,size_16,color_FFFFFF,t_70)

------



## go.mod如何在项目中使用？

1.首先我们要在GOPATH/src 目录之外新建工程，或将老工程copy到GOPATH/src 目录之外。

PS：go.mod文件一旦创建后，它的内容将会被go toolchain全面掌控。go toolchain会在各类命令执行时，比如go get、go build、go mod等修改和维护go.mod文件。

go.mod 文件内提供了module, require、replace和exclude四个关键字，这里注意区分与上表中命令的区别，一个是管理go mod命令，一个是go mod文件内的关键字。

* module语句指定包的名字（路径）
* require语句指定的依赖项模块
* replace语句可以替换依赖项模块
* exclude语句可以忽略依赖项模块

下面是我们建立了一个hello.go的文件

```go
package main

import (
	"fmt"
)

func main() {
    fmt.Println("Hello, world!")
}
```

2.在当前目录下，命令行运行 `go mod init + 模块名称` 初始化模块（即 go mod init hello）

运行完之后，会在当前目录下生成一个go.mod文件，这是一个关键文件，之后的包的管理都是通过这个文件管理。

官方说明：除了go.mod之外，go命令还维护一个名为go.sum的文件，其中包含特定模块版本内容的预期加密哈希 
go命令使用go.sum文件确保这些模块的未来下载检索与第一次下载相同的位，以确保项目所依赖的模块不会出现意外更改，无论是出于恶意、意外还是其他原因。 go.mod和go.sum都应检入版本控制。 
go.sum 不需要手工维护，所以可以不用太关注。

【注意：**子目录里是不需要init的，所有的子目录里的依赖都会组织在根目录的go.mod文件里**】

接下来，让我们的项目依赖一下第三方包

如修改hello.go文件如下，按照过去的做法，要运行hello.go需要执行go get 命令 下载gorose包到 $GOPATH/src

```go
package main

import (
	"fmt"
	"github.com/gohouse/gorose"
)

func main() {
    fmt.Println("Hello, world!")
}
```

但是，使用了新的包管理就不在需要这样做了

直接 go run hello.go

稍等片刻，go 会自动查找代码中的包，下载依赖包，并且把具体的依赖关系和版本写入到go.mod和go.sum文件中。
查看go.mod，它会变成这样：

```go
module test

require (
	github.com/gohouse/gorose v1.0.5
)
```

require 关键字是引用，后面是包，最后v1.11.1 是引用的版本号

这样，一个使用Go包管理方式创建项目的小例子就完成了。

------



## 问题

#### 问题一：依赖的包下载到哪里了？还在GOPATH/src里吗？

不在。
【使用Go的包管理方式，依赖的第三方包被下载到了$GOPATH/pkg/mod路径下】

#### 问题二： 依赖包的版本是怎么控制的？

在上一个问题里，可以看到最终下载在GOPATH/pkg/mod 下的包中最后会有一个版本号 v1.0.5，也就是说，GOPATH/pkg/mod里可以保存相同包的不同版本。

版本是在go.mod中指定的。如果，在go.mod中没有指定，go命令会自动下载代码中的依赖的最新版本，本例就是自动下载最新的版本。如果，在go.mod用require语句指定包和版本 ，go命令会根据指定的路径和版本下载包，
指定版本时可以用latest，这样它会自动下载指定包的最新版本；

#### 问题三： 可以把项目放在$GOPATH/src下吗？

可以。但是go会根据GO111MODULE的值而采取不同的处理方式，默认情况下，GO111MODULE=auto 自动模式

1.auto 自动模式下，项目在GOPATH/src里会使用GOPATH/src的依赖包，在$GOPATH/src外，就使用go.mod 里 require的包

2.on 开启模式，1.12后，无论在$GOPATH/src里还是在外面，都会使用go.mod 里 require的包

3.off 关闭模式，就是老规矩。

#### 问题四： 依赖包中的地址失效了怎么办？比如 golang.org/x/… 下的包都无法下载怎么办？

在go快速发展的过程中，有一些依赖包地址变更了。以前的做法：

1.修改源码，用新路径替换import的地址

2.git clone 或 go get 新包后，copy到$GOPATH/src里旧的路径下

无论什么方法，都不便于维护，特别是多人协同开发时。

使用go.mod就简单了，在go.mod文件里用 replace 替换包，例如

```go
replace golang.org/x/text => github.com/golang/text latest
```

这样，go会用 github.com/golang/text 替代golang.org/x/text，原理就是下载github.com/golang/text 的最新版本到 $GOPATH/pkg/mod/golang.org/x/text下。

#### 问题五： init生成的go.mod的模块名称有什么用？

本例里，用 go mod init hello 生成的go.mod文件里的第一行会申明module hello

因为我们的项目已经不在$GOPATH/src里了，那么引用自己怎么办？就用模块名+路径。

例如，在项目下新建目录 utils，创建一个tools.go文件:

```go
package utils

import “fmt”

func PrintText(text string) {
    fmt.Println(text)
}
```

在根目录下的hello.go文件就可以 import “hello/utils” 引用utils

```go
package main

import (
	"hello/utils"
	"github.com/astaxie/beego"
)

func main() {
	utils.PrintText("Hi")
	beego.Run()
}
```

#### 问题六：以前老项目如何用新的包管理

1. 如果用auto模式，把项目移动到$GOPATH/src外
2. 进入目录，运行 go mod init + 模块名称
3. go build 或者 go run 一次

------



## go mod发布和使用

### Creating a Module

如果你设置好go mod了，那你就可以在任何目录下随便创建

```bash
$mkdir gomodone
$cd gomodone
```

在这个目录下创建一个文件`say.go`

```go
package gomodone

import "fmt" 

// say Hi to someone
func SayHi(name string) string {
   return fmt.Sprintf("Hi, %s", name)
}
```

初始化一个 `go.mod`文件

```bash
$ go mod init github.com/jacksonyoudi/gomodone
go: creating new go.mod: module github.com/jacksonyoudi/gomodone
```

查看 go.mod内容如下：

```go
github.com/jacksonyoudi/gomodone
go 1.14
```

### 发布Module

下面我们要将这个module发布到github上，然后在另外一个程序使用

```bash
$git init
$vim .gitiiignore
$git commit -am "init"
// github创建对应的repo
$git remote add origin git@github.com:jacksonyoudi/gomodone.git
$git push -u origin master
```

执行完，上面我们就相当于发布完了。

如果有人需要使用，就可以使用

```bash
go get github.com/jacksonyoudi/gomodone
```

这个时候没有加tag，所以，没有版本的控制。默认是v0.0.0后面接上时间和commitid。如下：

```bash
gomodone@v0.0.0-20200517004046-ee882713fd1e
```

官方不建议这样做，没有进行版本控制管理。

### module versioning

使用tag，进行版本控制

#### making a release

```bash
git tag v1.0.0
git push --tags
```

操作完，我们的module就发布了一个v1.0.0的版本了。

推荐在这个状态下，再切出一个分支，用于后续v1.0.0的修复推送,不要直接在master分支修复

```bash
$git checkout -b v1
$git push -u origin v1
```

### use our module

上面已经发布了一个v1.0.0的版本，我们可以在另一个项目中使用，创建一个go的项目

```bash
$mkdir Gone
$cd Gone
$vim main.go
```

```go
package main

import (
    "fmt"
    "github.com/jacksonyoudi/gomodone"
)

func main() {
    fmt.Println(gomodone.SayHi("Roberto"))
}
```

代码写好了，我们生成 go mod文件

```bash
go mod init Gone
```

上面命令执行完，会生成 go mod文件
 看下mod文件：

```bash
module Gone

go 1.14

require (
    github.com/jacksonyoudi/gomodone v1.0.0
)
```

```bash
$go mod tidy
go: finding module for package github.com/jacksonyoudi/gomodone
go: found github.com/jacksonyoudi/gomodone in github.com/jacksonyoudi/gomodone v1.0.0
```

同时还生成了go.sum, 其中包含软件包的哈希值，以确保我们具有正确的版本和文件。

```bash
github.com/jacksonyoudi/gomodone v1.0.1 h1:jFd+qZlAB0R3zqrC9kwO8IgPrAdayMUS0rSHMDc/uG8=
github.com/jacksonyoudi/gomodone v1.0.1/go.mod h1:XWi+BLbuiuC2YM8Qz4yQzTSPtHt3T3hrlNN2pNlyA94=
github.com/jacksonyoudi/gomodone/v2 v2.0.0 h1:GpzGeXCx/Xv2ueiZJ8hEhFwLu7xjxLBjkOYSmg8Ya/w=
github.com/jacksonyoudi/gomodone/v2 v2.0.0/go.mod h1:L8uFPSZNHoAhpaePWUfKmGinjufYdw9c2i70xtBorSw=
```

这个内容是下面的，需要操作执行的结果

`go run main.go`就可以运行了

### Making a bugfix release

假如fix一个bug,我们在v1版本上进行修复

修改代码如下：

```go
// say Hi to someone
func SayHi(name string) string {
-       return fmt.Sprintf("Hi, %s", name)
+       return fmt.Sprintf("Hi, %s!", name)
}
```

修复好，我们开始push

```bash
$ git commit -m "Emphasize our friendliness" say.go
$ git tag v1.0.1
$ git push --tags origin v1
```

#### Updating modules

刚才fix bug，所以要在我们使用项目中更新

这个需要我们手动执行更新module操作

我们通过使用go ge来做到这一点：

- 运行  `go get -u` 以使用最新的  minor  版本或修补程序版本（即它将从1.0.0更新到例如1.0.1，或者，如果可用，则更新为1.1.0）
- 运行  go get -u=patch 以使用最新的  修补程序  版本（即，将更新为1.0.1但不更新  为1.1.0）
- 运行go get package@version 以更新到特定版本（例如[github.com/jacksonyoudi/gomodone@v1.0.1](https://links.jianshu.com/go?to=http%3A%2F%2Fgithub.com%2Fjacksonyoudi%2Fgomodone@v1.0.1)）

目前module最新的也是v1.0.1

```bash
// 更新最新
$go get -u
$go get -u=patch
//指定包，指定版本
$go get github.com/jacksonyoudi/gomodone@v1.0.1
```

操作完，go.mod文件会修改如下:

```go
module Gone

go 1.14

require (
    github.com/jacksonyoudi/gomodone v1.0.1
)
```

#### Major versions

根据语义版本语义，主要版本与次要版本不同。主要版本可能会破坏向后兼容性。从Go模块的角度来看，主要版本是完全不同的软件包。乍一看这听起来很奇怪，但这是有道理的：两个不兼容的库版本是两个不同的库。

比如下面修改，完全破坏了兼容性。

```go
package gomodone

import (
    "errors"
    "fmt"
)

// Hi returns a friendly greeting
// Hi returns a friendly greeting in language lang
func SayHi(name, lang string) (string, error) {
    switch lang {
    case "en":
        return fmt.Sprintf("Hi, %s!", name), nil
    case "pt":
        return fmt.Sprintf("Oi, %s!", name), nil
    case "es":
        return fmt.Sprintf("¡Hola, %s!", name), nil
    case "fr":
        return fmt.Sprintf("Bonjour, %s!", name), nil
    default:
        return "", errors.New("unknown language")
    }
}
```

如上，我们需要不同的大版本，这种情况下

修改 go.mod如下

```go
module github.com/jacksonyoudi/gomodone/v2

go 1.14
```

然后，重新tag，push

```bash
$ git commit say.go -m "Change Hi to allow multilang"
$ git checkout -b v2 # 用于v2版本，后续修复v2
$ git commit go.mod -m "Bump version to v2"
$ git tag v2.0.0
$ git push --tags origin v2 
```

### Updating to a major version

即使发布了库的新不兼容版本，现有软件也不会中断，因为它将继续使用现有版本1.0.1。go get -u 将不会获得版本2.0.0。
 如果想使用v2.0.0,代码改成如下：

```go
package main

import (
    "fmt"
    "github.com/jacksonyoudi/gomodone/v2"
)

func main() {
    g, err := gomodone.SayHi("Roberto", "pt")
    if err != nil {
        panic(err)
    }
    fmt.Println(g)
}
```

执行 go mod tidy

```bash
go: finding module for package github.com/jacksonyoudi/gomodone/v2
go: downloading github.com/jacksonyoudi/gomodone/v2 v2.0.0
go: found github.com/jacksonyoudi/gomodone/v2 in github.com/jacksonyoudi/gomodone/v2 v2.0.0
```

当然，两个版本都可以同时使用, 使用别名
如下：

```go
package main

import (
    "fmt"
    "github.com/jacksonyoudi/gomodone"
    mv2 "github.com/jacksonyoudi/gomodone/v2"
)

func main() {
    g, err := mv2.SayHi("Roberto", "pt")
    if err != nil {
        panic(err)
    }
    fmt.Println(g)

    fmt.Println(gomodone.SayHi("Roberto"))
}
```

执行一下 `go mod tidy`

------



## Vendoring

默认是忽略vendor的，如果想在项目目录下有vendor可以执行下面命令

```bash
$go vendor
```

当然，如果构建程序的时候，希望使用vendor中的依赖，

```go
$ go build -mod vendor
```

------

