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

# sync
## sync.WaitGroup

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

# net
## net.Conn.SetReadDeadline

设置超时时间

`net.Conn`为Deadline提供了多个方法`Set[Read|Write]Deadline(time.Time)`。

Deadline是一个绝对时间值，当到达这个时间的时候，所有的 I/O 操作都会失败，返回超时(timeout)错误。

# I/O

## io.Reader

对于要用作读取器的类型，它必须实现 `io.Reader` 接口的唯一一个方法 `Read(p []byte)`。
换句话说，只要实现了 `Read(p []byte)` ，那它就是一个读取器。

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}
```

`Read()` 方法有两个返回值，一个是读取到的字节数，一个是发生错误时的错误。
同时，如果资源内容已全部读取完毕，应该返回 `io.EOF` 错误。

## io.Copy





