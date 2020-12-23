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

![image-20200910185003638](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201222103430.png)

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



