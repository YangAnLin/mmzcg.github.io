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

```go
// 行注释

/*
多行注释
*/
```

`gofmt -w demo.go` 格式化工具

# 声明变量

基本类型,声明变量,不赋值,就有默认值

```go
func main() {
	// 不赋值,存在默认值
	var age, age2, age3 int
	println("不赋值,就打印默认值:", age, age2, age3)
}
```

一次性声明不同类型的变量

```go
func method5() {
	var age, name = 10, "anthony"
	println("可以声明不同类型,只能自动推导,不能定义类型:", age, name)

	// 报错,多变量赋值值,不能声明类型
	var age2 int,name2 string = 10, "anthony"
	println(age2,name2)
}

func method6() {
	var (
		age  int    = 23
		name string = "anthony"
		sex  bool
	)
	println("使用集合的方式声明变量,可以声明不同类型,并且可以赋值", age, name, sex)
}
```

先声明,后赋值

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

类型推导

```go
func method2() {
	var name = "anthony"
	print("类型推导:", name)
}
```

# 字符串

```go
func main() {
	name := "Hello World"
	for index := range  name {
		fmt.Printf("遍历字符串:%v,%v\n",index,name[index])
	}
}
```

# 常量

常量是一个简单值的标识符，在程序运行时，不会被修改的量

出于习惯,常量用大写,不过小写也没有问题

```go
// 常量的格式定义
const identifier [type] = value
// 显式类型定义
const A string = "abc"
// 隐式类型定义
const B = "abc"
// 多个相同类型的声明可以简写为
const C,D = 1,2
// 多个不同类型的声明可以简写为
const E, F, G = 1, false, "str"
```

# 常量用作枚举
常量可以作为枚举，常量组
```go
func main() {

	const (
		RED   = 1
		BLACK = 2
		WHITE = 3
	)

	const (
		x int = 16
		y
		s = "abc"
		z
	)

	fmt.Println(RED, BLACK, WHITE)
	fmt.Println("常量组中如不指定类型和初始化值，则与上一行非空常量右值相同:", x, y, s, z)
}
```

# 特殊常量:iota
iota，特殊常量，可以认为是一个可以被编译器修改的常量
1. 如果中断iota自增，则必须显式恢复。且后续自增值按行序递增
2. 自增默认是int类型，可以自行进行显示指定类型
3. 数字常量不会分配存储空间，无须像变量那样通过内存寻址来取值，因此无法获取地址
```go
func demo() {
	// 报错
	//a = iota

	const (
		a = iota //0
		b        //1
		c        //2
		d = "ha" //独立值，iota += 1
		e        //"ha"   iota += 1
		f = 100  //iota +=1
		g        //100  iota +=1
		h = iota //7,恢复计数
		i        //8
	)
	fmt.Println(a, b, c, d, e, f, g, h, i)
}
```

# type

```go
func main() {

	// 类型转换
	var one int = 17
	mean := float32(one)
	fmt.Println(mean)

	// 字符串不能强转整型

}
```

# 命令行输入

```go
func method_if() {
	reader := bufio.NewReader(os.Stdin)
	str, _ := reader.ReadString('\n')
	fmt.Printf("输入的值是:%s\n", str)
}
```

# 流程控制

```go
// if
func method_if() {

	reader := bufio.NewReader(os.Stdin)
	str, _ := reader.ReadString('\n')

	if str == "" {
		fmt.Println("空的")
	}

	fmt.Printf("输入的值是:%s\n", str)

}


// if-else
func method_if2() {

	if num := 10; num%2 == 0 { //checks if number is even
		fmt.Println(num, "is even")
	} else {
		fmt.Println(num, "is odd")
	}

}

// switch
func method_switch() {

	num := 1
	result := -1

	switch num {
	case 1:
		result = 1
	case 2, 3, 4:
		result = 2
	default:
		result = -2

	}

	fmt.Println(result)

}

// for  开始012345678910结束
func demo() {
	fmt.Print("开始")
	for i := 0; i <= 10; i++ {
		fmt.Print(i)
	}
	fmt.Print("结束")
}

// break 开始01234结束
func demo_break() {

	fmt.Print("开始")
	for i := 0; i <= 10; i++ {
		if i == 5 {
			break
		}
		fmt.Print(i)
	}

	fmt.Print("结束")
}

// coutinue   1 3 5 7 9 
func demo_continue() {

	for i := 1; i <= 10; i++ {
		if i%2 == 0 {
			continue
		}
		fmt.Printf("%d ", i)
	}
}

// go
// 会打印两次 ===
func demo_goto() {

	/* 定义局部变量 */
	var a = 0

	/* 循环 */
LOOP:
	fmt.Println("===")
	for a < 10 {
		if a == 5 {
			/* 跳过迭代 */
			a = a + 1
			goto LOOP
		}
		fmt.Printf("a的值为 : %d\n", a)
		a++
	}
}
```

# 函数

```go
// 函数的定义
func 函数名字(参数名字 参数类型,参数名字 参数类型.... )返回值类型 {
   函数体
}

// 函数的使用
// 函数返回两个数的最大值
func max(num1, num2 int) int {
   /* 声明局部变量 */
   var result int

   if (num1 > num2) {
      result = num1
   } else {
      result = num2
   }
   return result
}

// 函数调用
func main() {
   /* 定义局部变量 */
   var a int = 100
   var b int = 200
   var ret int

   /* 调用函数并返回最大值 */
   ret = max(a, b)

   fmt.Printf( "最大值是 : %d\n", ret )
}
```

# 函数返回多个值

```go
// 入参多个值,x和y 都是字符串
// 返回值是多个,都是字符串
func swap(x, y string) (string, string) {
   return y, x
}

func main() {
   a, b := swap("Google", "Runoob")
   fmt.Println(a, b)
}
```

# 函数作为实参

```go
type fb func(x int) int

func main() {

   myfunc := func(x int) int{
      return x
   }

    // 知识点1
	fmt.Println(myfunc(3))
    
    // 知识点2
	demo(2, myfunc)
}

// 函数作为参数传递，实现回调
func demo(x int,myfb fb)  {
	myfb(x)
}
```

# 值传递

程序中使用的是值传递, 所以两个值并没有实现交互

```go
func main() {
   /* 定义局部变量 */
   var a int = 100
   var b int = 200

   fmt.Printf("交换前 a 的值为 : %d\n", a )
   fmt.Printf("交换前 b 的值为 : %d\n", b )

   /* 通过调用函数来交换值 */
   swap(a, b)

   fmt.Printf("交换后 a 的值 : %d\n", a )
   fmt.Printf("交换后 b 的值 : %d\n", b )
}

/* 定义相互交换值的函数 */
func swap(x, y int) int {
   var temp int

   temp = x /* 保存 x 的值 */
   x = y    /* 将 y 值赋给 x */
   y = temp /* 将 temp 值赋给 y*/

   return temp;
}


//交换前 a 的值为 : 100
//交换前 b 的值为 : 200
//交换后 a 的值 : 100
//交换后 b 的值 : 200
```

# 引用传递

```go
func main() {
   /* 定义局部变量 */
   var a int = 100
   var b int= 200

   fmt.Printf("交换前，a 的值 : %d\n", a )
   fmt.Printf("交换前，b 的值 : %d\n", b )

   /* 调用 swap() 函数
   * &a 指向 a 指针，a 变量的地址
   * &b 指向 b 指针，b 变量的地址
   */
   swap(&a, &b)

   fmt.Printf("交换后，a 的值 : %d\n", a )
   fmt.Printf("交换后，b 的值 : %d\n", b )
}

func swap(x *int, y *int) {
   var temp int
   temp = *x    /* 保存 x 地址上的值 */
   *x = *y      /* 将 y 值赋给 x */
   *y = temp    /* 将 temp 值赋给 y */
}

//交换前，a 的值 : 100
//交换前，b 的值 : 200
//交换后，a 的值 : 200
//交换后，b 的值 : 100
```

# 变量作用域

```go
// 全局变量
var global int = 32

func main() {

	// 局部变量
	var a,b int =1, 2

	fmt.Printf("打印全局变变量:%v\n", global)
	global = a+ b
	fmt.Printf("打印全局变变量:%v\n", global)

	var global int = 3
	fmt.Printf("打印全局变变量:%v\n", global)

}
```

# 数组

```go
func get() {

	list := [5]int{1,2,3,4,5}

	for i:=0;i< len(list);i++ {
		fmt.Println(i, "==", list[i])
	}
}

func create(){

	var numbers []int
	fmt.Println("新建个空数组:",numbers)

	var defaultcount [4]int
	fmt.Println("新建个指定长度的数组:",defaultcount)

	var balance =  []int{1,2,3,4,5}
	fmt.Println("新建个不指定长度的数组:",balance)

	// 根据元素的个数，设置数组的大小
	d := [...] int{1,2,3,4,5}
	fmt.Println("新建个指定位置的数组:",d)

	// 指定位置
	e := [5] int{4: 100} // [0 0 0 0 100]
	fmt.Println("新建个指定位置的数组:",e)

	// 指定位置
	f := [...] int{0: 1, 4: 1, 9: 1} // [1 0 0 0 1 0 0 0 0 1]
	fmt.Println("新建个指定位置的数组:",f)

}
```

# 切片

```go
func define() {

	var identifier []int
	fmt.Println("空数组",identifier)

	var slice1 []int = make([]int, 10)
	fmt.Println("切片",slice1)

	i0 := slice1[0]
	i1 := slice1[1]
	i2 := slice1[2]
	i3 := slice1[3]
	fmt.Println("通过索引,获取值",i0,i1,i2,i3)

	// 修改切片
	for i := 0 ;i< len(slice1);i++ {
		slice1[i] = i
	}
	fmt.Println("修改完切片::",slice1)

	fmt.Println("获取切片区间1:",slice1[0:2])
	fmt.Println("获取切片区间2:",slice1[5:])
	fmt.Println("获取切片区间3:",slice1[:5])

	slice1 = append(slice1, 10,11,12)
	fmt.Println("追加完切片::",slice1)

	slice2 := make([]int,len(slice1),cap(slice1)*2)
	fmt.Println("创建个容量是原来容量两位的数组:",slice2)

	number := copy(slice2,slice1)
	fmt.Printf("slice:%v\n,slice2:%v\n,number:%v\n:",slice1,slice2,number)

}
```

# Map

```go
func main() {

	keyvale := make(map[string]string)
	keyvale["k1"] = "v1"
	keyvale["k2"] = "v2"
	keyvale["k3"] = "v3"
	keyvale["k4"] = "v4"

	// 循环遍历
	for key:=range keyvale {
		fmt.Println("循环遍历:",key,keyvale[key])
	}

	// 删除元素
	delete(keyvale,"k1")
	for key:=range keyvale {
		fmt.Println("删除值之后,循环遍历:",key,keyvale[key])
	}

	// 查看元素是否存在
	_,ok := keyvale["United States"]
	if ok {
		fmt.Printf("存在\n")
	}else {
		fmt.Printf("不存在\n")
	}

	// 但是当key如果不存在的时候，我们会得到该value值类型的默认值，比如string类型得到空字符串，int类型得到0。但是程序不会报错
	m := make(map[string]int)
	m["a"] = 1
	x, ok := m["b"]
	fmt.Println("key不存在时,会有默认值",x, ok)

	// map长度
	fmt.Println("map长度",len(m))

	// map是引用类型
	mymap := map[string]int{
		"steven":12000,
		"anthony":15000,
	}
	mymap["mike"] = 9000
	fmt.Printf("原来的数据:%v\n",mymap)

	newmymap := mymap
	newmymap["anthony"] = 50000
	fmt.Printf("改变后,原来的数据:%v\n",mymap)
	fmt.Printf("改变后,新的的数据:%v\n",newmymap)
}
```

# 指针

```go
func main() {
   var a int= 20   /* 声明实际变量 */
   var ip *int        /* 声明指针变量 */

   ip = &a  /* 指针变量的存储地址 */

   fmt.Printf("a 变量的地址是: %x\n", &a  )

   /* 指针变量的存储地址 */
   fmt.Printf("ip 变量储存的指针地址: %x\n", ip )

   /* 使用指针访问值 */
   fmt.Printf("*ip 变量的值: %d\n", *ip )
}


// 函数使用指针
func main() {
    /* 定义局部变量 */
   var a int = 100
   var b int= 200
   swap(&a, &b);

   fmt.Printf("交换后 a 的值 : %d\n", a )
   fmt.Printf("交换后 b 的值 : %d\n", b )
}

/* 交换函数这样写更加简洁，也是 go 语言的特性，可以用下，c++ 和 c# 是不能这么干的 */
 
func swap(x *int, y *int){
    *x, *y = *y, *x
}
```

# 结构体

```go
type Books struct {
	title string
	author string
	subject string
	book_id int
}

type Library struct {
	// 匿名字段，那么默认Student就包含了Human的所有字段
	Books

	address string
}

func main() {
	var book Books
	book.title = "Go 语言"
	book.author = "www.runoob.com"
	book.subject = "Go 语言教程"
	book.book_id = 6495407

	fmt.Println( "======================调用方法打印===================")
	printBook2(book)

	fmt.Println( "=======================调用方法指针打印==================")
	printBook(&book)

	fmt.Println( "=======================手动打印==================")
	fmt.Printf( "Book 1 title : %s\n", book.title)
	fmt.Printf( "Book 1 author : %s\n", book.author)
	fmt.Printf( "Book 1 subject : %s\n", book.subject)
	fmt.Printf( "Book 1 book_id : %d\n", book.book_id)

	// 初始化一个图书馆
	mark := Library{Books{"Go 语言","www.runoob.com","Go 语言教程",6495407},"广东"}
	// 我们访问相应的字段
	fmt.Println("His name is ", mark.title)
	fmt.Println("His age is ", mark.author)
	fmt.Println("His weight is ", mark.subject)
	fmt.Println("His speciality is ", mark.address)
	// 修改对应的备注信息
	mark.title = "AI"
	fmt.Println("Mark changed his speciality")
	fmt.Println("His speciality is ", mark.title)
}

func printBook2(book Books) {
	fmt.Printf( "Book title : %s\n", book.title);
	fmt.Printf( "Book author : %s\n", book.author);
	fmt.Printf( "Book subject : %s\n", book.subject);
	fmt.Printf( "Book book_id : %d\n", book.book_id);
	book.title= "函数修改后"
}

func printBook(book *Books) {
	fmt.Printf( "Book title : %s\n", book.title);
	fmt.Printf( "Book author : %s\n", book.author);
	fmt.Printf( "Book subject : %s\n", book.subject);
	fmt.Printf( "Book book_id : %d\n", book.book_id);
	book.title= "指针修改后"
}

```

# 面向对象

```go
// 定义接口
type PersonMethod interface {
	show()
}

// 定义结构体
type Person struct {
	name string
	age  int
}

// 对象成员方法
func (person *Person) setAge(age int) {
	person.age = age
}

// 对象成员方法实现接口
func (person Person) show() {
	fmt.Printf("Person类打印SHOW:%v\n", person)
}

// 多态
func show2(person PersonMethod) {
	person.show()
}

// 学生类,继承Person类
type Student struct {
	// 匿名,相当于继承
	Person
	level string
}

func (student *Student) show() {
	fmt.Printf("Student类打印SHOW:%v\n", student)
}

// 老师类,继承Person类
type Teacher struct {
	Person
	price int
}

func (teacher Teacher) show() {
	fmt.Printf("Teacher类打印SHOW:%v\n", teacher)
}

func main() {
	anthony := Person{"anthony", 25}
	fmt.Printf("anthony信息:%v\n", anthony)

	// 调用成员方法
	anthony.setAge(12)
	fmt.Printf("anthony信息:%v\n", anthony)

	anthony2 := Person{}
	fmt.Printf("anthony2信息:%v\n", anthony2)

	anthony2.age = 26
	anthony2.name = "anthony2"
	fmt.Printf("anthony2信息:%v\n", anthony2)

	// 学生,继承
	student := Student{}
	student.level = "小学生"
	student.name = "anthony"
	student.age = 23
	fmt.Printf("学生继承类:%v\n", student)

	// 老师,继承
	teacher := Teacher{price: 12, Person: Person{name: "li teacher", age: 56}}
	fmt.Printf("老师继承类:%v\n", teacher)

	show2(&student)
	show2(teacher)
	show2(anthony)

}
```

# 接口

```go
type Phone interface {
    call()
}

type NokiaPhone struct {
}

func (nokiaPhone NokiaPhone) call() {
    fmt.Println("I am Nokia, I can call you!")
}

type IPhone struct {
}

func (iPhone IPhone) call() {
    fmt.Println("I am iPhone, I can call you!")
}

func main() {
    var phone Phone

    phone = new(NokiaPhone)
    phone.call()

    phone = new(IPhone)
    phone.call()

}
```

# 错误处理

```go
// 自定义错误信息结构
type DivErr struct {
	etype int  // 错误类型
	v1 int     // 记录下出错时的除数、被除数
	v2 int
}

// 实现接口方法 error.Error()
func (divErr DivErr) Error() string {
	if 0== divErr.etype {
		return "除零错误"
	}else{
		return "其他未知错误"
	}
}

// 除法
func div(a int, b int) (int,*DivErr) {
	if b == 0 {
		// 返回错误信息
		return 0,&DivErr{0,a,b}
	} else {
		// 返回正确的商
		return a / b, nil
	}
}

func main() {

	// 正确调用
	v,r :=div(100,2)
	if nil!=r{
		fmt.Println("(1)fail:",r)
	}else{
		fmt.Println("(1)succeed:",v)
	}
	// 错误调用
	v,r =div(100,0)
	if nil!=r{
		fmt.Println("(2)fail:",r)
	}else{
		fmt.Println("(2)succeed:",v)
	}

}
```

# GoRoutine

```go
func main() {
	go say("world")
	say("hello")
}


func say(s string) {
	for i := 0; i < 5; i++ {
		time.Sleep(100 * time.Millisecond)
		fmt.Println(s)
	}
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


# GO库

## flag

```go
import (
	"errors"
	"flag"
	"fmt"
	"strings"
	"time"
)

/**

// 编译
go build demo.go

// 帮助信息
demo.exe -h

// 正常使用
demo.exe -name yang
*/
func main() {

	//demo1()

	demo2()

}

// 第一种用法
func demo1() {
	var nFlag = flag.String("name", "anthony", "help message for flag n")
	flag.Parse()
	fmt.Println(*nFlag)
}

// 第二种用法
func demo2() {
	var name string
	flag.StringVar(&name, "name", "anthonyyang", "help message for flag n")
	flag.Parse()
	fmt.Println(name)
}

// 第三种用法
func demo3() {
	var intervalFlag mystring
	flag.Var(&intervalFlag, "anthonyyang", "name")
	fmt.Println(intervalFlag)
}

type mystring []time.Duration

func (i *mystring) String() string {
	return fmt.Sprint()
}

func (i *mystring) Set(value string) error {
	if len(*i) > 0 {
		return errors.New("interval flag already set")
	}
	for _, dt := range strings.Split(value, ",") {
		duration, err := time.ParseDuration(dt)
		if err != nil {
			return err
		}
		*i = append(*i, duration)
	}
	return nil
}
```

[参考](https://darjun.github.io/2020/01/10/godailylib/flag/)

## log

```go

```



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





