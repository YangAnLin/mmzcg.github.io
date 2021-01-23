# 入门

## 环境变量

| 环境变量 | 说明                                                         |      |      |
| -------- | ------------------------------------------------------------ | ---- | ---- |
| `GOROOT` | go的安装路径,我的路径是`c:\go`                               |      |      |
| `Path`   | `%GOROOT%\bin;`                                              |      |      |
| `GOPATH` | 自己写的源代码的的位置,所以要把所有的go代码都要写在这个文件夹下 |      |      |

## Hello World

Hello world

```go
package main

import "fmt"

func main() {
	fmt.Print("hello world")
}
```

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201222103430.png)

`package main` 一定要在main的包下才能运行

一个main包下,只能有一个main入口

## 运行

运行go的两种方法

### 第一种,编译后运行`*.exe文件`

```shell
C:\Users\anthony\go\src\awesomeProject\demo\main>dir
2020/09/10  18:52    <DIR>          .
2020/09/10  18:52    <DIR>          ..
2020/09/10  18:49                69 demo.go
               1 个文件             69 字节
               2 个目录 145,636,782,080 可用字节

# 编译
C:\Users\anthony\go\src\awesomeProject\demo\main>go build demo.go

C:\Users\anthony\go\src\awesomeProject\demo\main>dir
2020/09/10  18:52    <DIR>          .
2020/09/10  18:52    <DIR>          ..
2020/09/10  18:52         2,145,792 demo.exe
2020/09/10  18:49                69 demo.go
               2 个文件      2,145,861 字节
               2 个目录 145,634,603,008 可用字节

# 运行编译后exe文件
C:\Users\anthony\go\src\awesomeProject\demo\main>demo.exe
hello world
```

### 第二种,直接运行源代码`go run *.go`

```shell
C:\Users\anthony\go\src\awesomeProject\demo\main>go run demo.go
hello world
```

# 注释

`//` 行注释

`**` 块注释

`gofmt -w demo.go` 格式化工具

# 变量

第一种赋值

```go
func main() {
	// 定义变量
	var i int
	// 给i赋值
	i=10
	// 使用变量
	fmt.Print("i=", i)
}
```

# modules

GO111MODULE

`GO111MODULE` 有三个值：`off`, `on`和`auto（默认值）`。

- `GO111MODULE=off`，go命令行将不会支持module功能，寻找依赖包的方式将会沿用旧版本那种通过vendor目录或者GOPATH模式来查找。
- `GO111MODULE=on`，go命令行会使用modules，而一点也不会去GOPATH目录下查找。
- GO111MODULE=auto
  - 默认值，go命令行将会根据当前目录来决定是否启用module功能。这种情况下可以分为两种情形：
  - 当前目录在GOPATH/src之外且该目录包含go.mod文件
  - 当前文件在包含go.mod文件的目录下面。



go mod常用命令

| 命令     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| download | download modules to local cache(下载依赖包)                  |
| edit     | edit go.mod from tools or scripts（编辑go.mod                |
| graph    | print module requirement graph (打印模块依赖图)              |
| init     | initialize new module in current directory（在当前目录初始化mod） |
| tidy     | add missing and remove unused modules(拉取缺少的模块，移除不用的模块) |
| vendor   | make vendored copy of dependencies(将依赖复制到vendor下)     |
| verify   | verify dependencies have expected content (验证依赖是否正确） |
| why      | explain why packages or modules are needed(解释为什么需要依赖) |



### 示例一：创建一个新项目

1. 在`GOPATH 目录之外`新建一个目录，并使用`go mod init` 初始化生成`go.mod` 文件

go.mod文件一旦创建后，它的内容将会被go toolchain全面掌控。go toolchain会在各类命令执行时，比如go get、go build、go mod等修改和维护go.mod文件。

go.mod 提供了`module`, `require`、`replace`和`exclude` 四个命令

- `module`  语句指定包的名字（路径）
- `require` 语句指定的依赖项模块
- `replace` 语句可以替换依赖项模块
- `exclude` 语句可以忽略依赖项模块

1. 添加依赖

新建一个 server.go 文件，写入以下代码：

```go
package main

import (
	"net/http"
	
	"github.com/labstack/echo"
)

func main() {
	e := echo.New()
	e.GET("/", func(c echo.Context) error {
		return c.String(http.StatusOK, "Hello, World!")
	})
	e.Logger.Fatal(e.Start(":1323"))
}
复制代码
```

执行 `go run server.go` 运行代码会发现 go mod 会自动查找依赖自动下载：

```sh
$ go run server.go
go: finding github.com/labstack/echo v3.3.10+incompatible
go: downloading github.com/labstack/echo v3.3.10+incompatible
go: extracting github.com/labstack/echo v3.3.10+incompatible
go: finding github.com/labstack/gommon/color latest
go: finding github.com/labstack/gommon/log latest
go: finding github.com/labstack/gommon v0.2.8
# 此处省略很多行
...

   ____    __
  / __/___/ /  ___
 / _// __/ _ \/ _ \
/___/\__/_//_/\___/ v3.3.10-dev
High performance, minimalist Go web framework
https://echo.labstack.com
____________________________________O/_______
                                    O\
⇨ http server started on [::]:1323
复制代码
```

现在查看go.mod 内容：

```go
$ cat go.mod

module hello

go 1.12

require (
	github.com/labstack/echo v3.3.10+incompatible // indirect
	github.com/labstack/gommon v0.2.8 // indirect
	github.com/mattn/go-colorable v0.1.1 // indirect
	github.com/mattn/go-isatty v0.0.7 // indirect
	github.com/valyala/fasttemplate v1.0.0 // indirect
	golang.org/x/crypto v0.0.0-20190313024323-a1f597ede03a // indirect
)
复制代码
```

go module 安装 package 的原則是先拉最新的 release tag，若无tag则拉最新的commit，详见 [Modules官方介绍](https://github.com/golang/go/wiki/Modules)。 go 会自动生成一个 go.sum 文件来记录 dependency tree：

```sh
$ cat go.sum
github.com/labstack/echo v3.3.10+incompatible h1:pGRcYk231ExFAyoAjAfD85kQzRJCRI8bbnE7CX5OEgg=
github.com/labstack/echo v3.3.10+incompatible/go.mod h1:0INS7j/VjnFxD4E2wkz67b8cVwCLbBmJyDaka6Cmk1s=
github.com/labstack/gommon v0.2.8 h1:JvRqmeZcfrHC5u6uVleB4NxxNbzx6gpbJiQknDbKQu0=
github.com/labstack/gommon v0.2.8/go.mod h1:/tj9csK2iPSBvn+3NLM9e52usepMtrd5ilFYA+wQNJ4=
github.com/mattn/go-colorable v0.1.1 h1:G1f5SKeVxmagw/IyvzvtZE4Gybcc4Tr1tf7I8z0XgOg=
github.com/mattn/go-colorable v0.1.1/go.mod h1:FuOcm+DKB9mbwrcAfNl7/TZVBZ6rcnceauSikq3lYCQ=
... 省略很多行
复制代码
```

1. 再次执行脚本 `go run server.go` 发现跳过了检查并安装依赖的步骤。
2. 可以使用命令 `go list -m -u all` 来检查可以升级的package，使用`go get -u need-upgrade-package` 升级后会将新的依赖版本更新到go.mod * 也可以使用 `go get -u` 升级所有依赖

#### go get 升级

- 运行 go get -u 将会升级到最新的次要版本或者修订版本(x.y.z, z是修订版本号， y是次要版本号)
- 运行 go get -u=patch 将会升级到最新的修订版本
- 运行 go get package@version 将会升级到指定的版本号version
- 运行go get如果有版本的更改，那么go.mod文件也会更改

### 示例二：改造现有项目(helloword)

项目目录为：

```bash
$ tree
.
├── api
│   └── apis.go
└── server.go

1 directory, 2 files
复制代码
```

server.go 源码为：

```go
package main

import (
    api "./api"  // 这里使用的是相对路径
    "github.com/labstack/echo"
)

func main() {
    e := echo.New()
    e.GET("/", api.HelloWorld)
    e.Logger.Fatal(e.Start(":1323"))
}
复制代码
```

api/apis.go 源码为：

```go
package api

import (
    "net/http"

    "github.com/labstack/echo"
)

func HelloWorld(c echo.Context) error {
    return c.JSON(http.StatusOK, "hello world")
}
复制代码
```

1. 使用 `go mod init ***` 初始化go.mod

```
$ go mod init helloworld
go: creating new go.mod: module helloworld
复制代码
```

1. 运行 `go run server.go`

```sh
go: finding github.com/labstack/gommon/color latest
go: finding github.com/labstack/gommon/log latest
go: finding golang.org/x/crypto/acme/autocert latest
go: finding golang.org/x/crypto/acme latest
go: finding golang.org/x/crypto latest
build command-line-arguments: cannot find module for path _/home/gs/helloworld/api
复制代码
```

首先还是会查找并下载安装依赖，然后运行脚本 `server.go`，这里会抛出一个错误：

```sh
build command-line-arguments: cannot find module for path _/home/gs/helloworld/api
复制代码
```

但是`go.mod` 已经更新：

```sh
$ cat go.mod
module helloworld

go 1.12

require (
        github.com/labstack/echo v3.3.10+incompatible // indirect
        github.com/labstack/gommon v0.2.8 // indirect
        github.com/mattn/go-colorable v0.1.1 // indirect
        github.com/mattn/go-isatty v0.0.7 // indirect
        github.com/valyala/fasttemplate v1.0.0 // indirect
        golang.org/x/crypto v0.0.0-20190313024323-a1f597ede03a // indirect
)
复制代码
```

##### 那为什么会抛出这个错误呢？

这是因为 server.go 中使用 internal package 的方法跟以前已经不同了，由于 go.mod会扫描同工作目录下所有 package 并且`变更引入方法`，必须将 helloworld当成路径的前缀，也就是需要写成 import helloworld/api，以往 GOPATH/dep 模式允许的 import ./api 已经失效，详情可以查看这个 [issue](https://github.com/golang/go/issues/26645)。

1. 更新旧的package import 方式

所以server.go 需要改写成：

```go
package main

import (
    api "helloworld/api"  // 这是更新后的引入方法
    "github.com/labstack/echo"
)

func main() {
    e := echo.New()
    e.GET("/", api.HelloWorld)
    e.Logger.Fatal(e.Start(":1323"))
}
复制代码
```

> `一个小坑`：开始在golang1.11 下使用go mod 遇到过 `go build github.com/valyala/fasttemplate: module requires go 1.12` [这种错误](https://github.com/golang/go/issues/27565)，遇到类似这种需要升级到1.12 的问题，直接升级golang1.12 就好了。幸亏是在1.12 发布后才尝试的`go mod` 🤷‍♂️


# GO标准包

## io

### io.Reader

对于要用作读取器的类型，它必须实现 `io.Reader` 接口的唯一一个方法 `Read(p []byte)`。
换句话说，只要实现了 `Read(p []byte)` ，那它就是一个读取器。

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}
```

`Read()` 方法有两个返回值，一个是读取到的字节数，一个是发生错误时的错误。
同时，如果资源内容已全部读取完毕，应该返回 `io.EOF` 错误。

### io.Copy

## sync
### sync.WaitGroup

```go
import (
    "fmt"
)

// 执行代码很可能看不到输出，因为有可能这两个协程还没得到执行主协程已经结束了，而主协程结束时会结束所有其他协程
func main() {
    go func() {
        fmt.Println("Goroutine 1")
    }()

    go func() {
        fmt.Println("Goroutine 2")
    }()
}
```

解决办法:

```go
import (
    "fmt"
    "time"
)

// 在main函数结尾加上等待
func main() {
    go func() {
        fmt.Println("Goroutine 1")
    }()

    go func() {
        fmt.Println("Goroutine 2")
    }()

    time.Sleep(time.Second * 1) // 睡眠1秒，等待上面两个协程结束
}
```

问题来了,不确定协程到底要用运行多长时间,比如协程需要用到2s,这里`main`等待1s,就不够用了

解决办法:用管道

```go
import (
    "fmt"
)

func main() {

    ch := make(chan struct{})
    count := 2 // count 表示活动的协程个数

    go func() {
        fmt.Println("Goroutine 1")
        ch <- struct{}{} // 协程结束，发出信号
    }()

    go func() {
        fmt.Println("Goroutine 2")
        ch <- struct{}{} // 协程结束，发出信号
    }()

    for range ch {
        // 每次从ch中接收数据，表明一个活动的协程结束
        count--
        // 当所有活动的协程都结束时，关闭管道
        if count == 0 {
            close(ch)
        }
    }
}
```

虽然比较完美的方案，但是Go提供了更简单的方法——使用sync.WaitGroup

WaitGroup顾名思义，就是用来等待一组操作完成的。

WaitGroup内部实现了一个计数器，用来记录未完成的操作个数，它提供了三个方法，Add()用来添加计数。

Done()用来在操作结束时调用，使计数减一。Wait()用来等待所有的操作结束，即计数变为0，该函数会在计数不为0时等待，在计数为0时立即返回

```go
import (
    "fmt"
    "sync"
)

func main() {

    var wg sync.WaitGroup

    wg.Add(2) // 因为有两个动作，所以增加2个计数
    go func() {
        fmt.Println("Goroutine 1")
        wg.Done() // 操作完成，减少一个计数
    }()

    go func() {
        fmt.Println("Goroutine 2")
        wg.Done() // 操作完成，减少一个计数
    }()

    wg.Wait() // 等待，直到计数为0
}
```

## net
### net.Conn.SetReadDeadline

设置超时时间

`net.Conn`为Deadline提供了多个方法`Set[Read|Write]Deadline(time.Time)`。

Deadline是一个绝对时间值，当到达这个时间的时候，所有的 I/O 操作都会失败，返回超时(timeout)错误。





