---
title: "Go语言快速上手"
description: "Go语言快速上手"
keywords: "Golang"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Golang
tags:
  - Golang
---

## 在Linux上安装GoLang
```bash
wget https://golang.google.cn/dl/go1.19.1.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.19.1.linux-amd64.tar.gz

echo 'export PATH=$PATH:/usr/local/go/bin' >> $HOME/.profile

source $HOME/.profile
go version
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct
```
## 在Windows上安装GoLang
[官网地址](https://golang.org/dl/)
[国内镜像地址](https://golang.google.cn/dl/)

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210625212345.png#crop=0&crop=0&crop=1&crop=1&id=gAvDY&originHeight=819&originWidth=1585&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

下载`.msi`文件安装后，配置环境变量

将安装的**Go\bin** 目录添加到 **Path** 环境变量中

将工作目录也添加到环境变量中

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210625213001.png#crop=0&crop=0&crop=1&crop=1&id=oxBQt&originHeight=208&originWidth=725&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210625213024.png#crop=0&crop=0&crop=1&crop=1&id=mkK8E&originHeight=664&originWidth=677&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

确认之后检查是否成功

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210625213126.png#crop=0&crop=0&crop=1&crop=1&id=zh36O&originHeight=187&originWidth=1242&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 配置开发环境

配置「七牛云」的代理服务

```shell
$ go env -w GO111MODULE=on
$ go env -w GOPROXY=https://goproxy.cn,direct
```

配置工作路径

```shell
go env -w GOPATH=C:\WorkDir\Go # 这是我的工作路径，填自己的
```

打开`vscode`安装GO语言的插件

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210625213830.png#crop=0&crop=0&crop=1&crop=1&id=BB95z&originHeight=438&originWidth=445&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

之后工作目录下新建一个`src`目录，在该目录下创建`xxx.go`文件，根据`vscode`的提示安装所有的包

## 变量的声明

```go
// 法一
var num int = 100
var str string = "123"
// 法二 知道变量的值后可以不用声明属性
var num = 100
var str = "123"
// 法三 此方法只能用在局部变量中
num := 100
str := "123"

// 多行声明
var num, str = 100, "123"
var (
    num = 100
    str = "123"
)
num, str := 100, "123"
```

## 常量的定义

```go
// 常量只读，不允许修改
const num int = 100
// const 用来定义枚举类型
const (
    // 可以在 const() 中添加关键字 iota(也只能在const 中使用), iota 会自增，第一行默认为 1
    January = iota // iota = 0
    February       // iota = 1
    March          // iota = 2 
    April          // iota = 3
    May            // ... ...
    June
    July
)
```

## 函数的返回值

```go
package main

import "fmt"

// 1个匿名返回值
func demo1(a int) int {
	b := a * 2
	return b
}

// 2个匿名返回值
func demo2(a int, b int) (int, int) {
	return a * 2, b * 3
}

// 不匿名的返回
func demo3(a int, b int) (r1 int, r2 int) {
	r1 = a * 2
	r2 = b * 3
	return
}

func demo4(a int, b int) (r1, r2 int){
    
}

func main() {
	ret1 := demo1(10)
	fmt.Printf("ret1 = %d\n", ret1)
	ret2, ret3 := demo2(10, 20)
	fmt.Printf("ret2 = %d, ret3 = %d\n", ret2, ret3)
	ret4, ret5 := demo3(100, 200)
	fmt.Printf("ret4 = %d, ret5 = %d\n", ret4, ret5)
}
```

## init() 函数和import

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210625225745.png#crop=0&crop=0&crop=1&crop=1&id=FDWxw&originHeight=331&originWidth=771&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```go
// 程序会先执行最里面的包的init()方法
```

**注意：**函数名开头大写表示函数是`public`, 小写表示函数是`private`

```go
import (
    _ "$GOPATH/xxx/xxx" // 匿名导包，不调用也会执行init()
    name "$GOPATH/xxx/xxx" // 起别名
    . "$GOPATH/xxx/xxx" // 导入到当前程序中，可直接使用包内的方法
)
```

## defer 语句的调用顺序

`defer`在函数的生命周期结束后调用(在`return`之后)，遵循先进后出原则

```go
package main

import "fmt"

func fun1() {
	fmt.Println("this is fun1 called")
}

func fun2() int {
	fmt.Println("this is fun2 called")
	return fun3()
}

func fun3() int {
	fmt.Println("this is fun3 called")
	return 0
}

func main() {
	defer fun1()
	defer fun2()
}

/* 结果
this is fun2 called
this is fun3 called
this is fun1 called
*/
```

## 静态数组和动态数值

```go
// 静态数组，上来就声明长度或者内容,有着固定长度
arr1 := [4]int{1,2,3,4}
arr2 := [10]int{1,2,3,4} // 前四有值，后面的全为0
var arr3 [5]int // 定义五个为0的数组

// 动态数组没有固定的长度，也叫 切片 (其本质为指针)
slice := []int{1,2,3} // 默认值 1 2 3 长度为3 len=3 , cap = 3
slice1 := make([]int, len, cap) // len <= cap
slice2 := make([]int, 3) // len = 3, cap = 3
// 切片的追加
slice1 = append(slice1, 1) // 向slice1追加一个值为1的元素
// 若向一个容量已满的切片追加，则会新建一个之前两倍容量的切片，
// 之后将旧的切片赋值给新的切片，在新的切片上进行追加
// 切片的截取
slice4 := []int{1,2,3,4,5,6}
// 若修改num，则slice4也会改变 浅拷贝
num := slice4[0:3] // 左闭右开 [:2] [1:]
// 深拷贝
num2 := make([]int, 3)
copy(num2, slice4)
```

## map的使用

和切片一样，是动态的，要分配空间

```go
                                               |->值
mymap := make(map[int]string, 10) // map[key]value / cap = 10
                                          |-> 键
mymap[0] = "Jun"
mymap[1] = "xxx"
... ... 

mymap1 := make(map[int]string) // 可以不加容量

mymap := map[string]string{
    "one": "Jun",
    "tow": "Fer",
    "key": "vlaue"
}

// 删除
delete(mymap, "key")

// 遍历
for key, value := range mymap {
    fmt.Println("key = ", key)
    fmt.Println("value = ", value)
}
```

**注意：**map 和 切片 传参时传的是指针，因此改变值时，原来的也会改变

## 结构体

```go
// 大写表示public 小写表示private
type book struct{
    name string
    autoh string
    price int
}

type Student struct {
	Name string
	Gender string
	Age int8
}
```

### 结构体之间的嵌套可以看作继承

```go
type Personal struct {
	Name string
	Age int
	Gender string
}

// Student 继承 Personal
type Student struct {
	Personal
	Classroom string
}

// 与结构体绑定方法
// 父类的方法
func (p Personal) Run() {
	fmt.Println(p.Name + "在奔跑~~~~~~~~")
}

// 子类的方法
func (t Student) HaveClass() {
	fmt.Println(t.Name + "在" + t.Classroom + "上课")
}

func main() {
	var s = Student{
		Personal:  Personal{
			"Colzry",
			21,
			"男",
		},
		Classroom: "16班",
	}

	fmt.Printf("%#v\n", s)
    // 调用父类的方法
	s.Run()
    // 调用自己的方法
	s.HaveClass()
}
```

## 接口

### 使用接口来规范结构体的方法

```go
package main

import "fmt"
//定义一个接口
type Operate interface {
	start()
	stop()
}

type Phone struct {
	Name string
}

// 让结构体实现接口
func (p Phone) start() {
	fmt.Println(p.Name + "开机")
}
func (p Phone) stop() {
	fmt.Println(p.Name + "关机")
}

func main() {
    // 实例化接口
	var redmi Operate = Phone{Name: "RedMi k40"}
    // 调用实现的方法
	redmi.start()
	redmi.stop()
}
```

### 使用空接口来做泛型

```go
var a interface{}
	a = 20
	fmt.Printf("a的值: %v, a的类型: %T\n", a, a)
	a = "Colzry"
	fmt.Printf("a的值: %v, a的类型: %T\n", a, a)
	a = true
	fmt.Printf("a的值: %v, a的类型: %T\n", a, a)
	a = []int{1, 2, 3}
	fmt.Printf("a的值: %v, a的类型: %T\n", a, a)

	b := make(map[interface{}]interface{})
	b[4] = true
	b["str"] = 25
	fmt.Println(b)

	c := []interface{}{1, 2, "3", true}
	fmt.Println(c)

######## 打印 ##########
a的值: 20, a的类型: int
a的值: Colzry, a的类型: string
a的值: true, a的类型: bool
a的值: [1 2 3], a的类型: []int
map[4:true str:25]
[1 2 3 true]
```

**常用在函数的参数和返回值处**

```go
func getObj(value interface{}) interface{} {
	return value
}
```

## 类型断言

```go
package main

import "fmt"

// 类型断言
func TypePrint(value interface{}) {
    // 两种判断类型的方法(if switch)，但是switch只能使用 x.(type)
	if _, ok := value.(string); ok {
		fmt.Println("它居然是个字符串")
	} else {
		fmt.Println("可惜它不是字符串")
	}
	switch value.(type) {
	case int:
		fmt.Println("int类型")
	case string:
		fmt.Println("string类型")
	case bool:
		fmt.Println("bool类型")
	case []int:
		fmt.Println("[]int类型")
	default:
		fmt.Println("不在列表范围内")
	}
}

func main() {
	a := 20
	TypePrint(a)
	b := "Colzry"
	TypePrint(b)
}
```

打印

```go
可惜它不是字符串
int类型
它居然是个字符串
string类型
```

## 协程

使用关键字**go**可以将一个方法(函数)作为协程来使用

```go
func main() {
	go test()
	for i := 0; i < 10; i++ {
		fmt.Println("main ---------------", i)
		time.Sleep(time.Millisecond * 500)
	}
}
func test() {
	for i := 0; i < 10; i++ {
		fmt.Println("test ---------------", i)
		time.Sleep(time.Millisecond * 1000)
	}
}
```

这样main函数和test函数就能同时运行，**但会出现一个问题**，main函数会因为**先结束**而导致test函数**没运行完就结束了**，这时需要使用异步

```go
import (
	"fmt"
	"sync"
	"time"
)

var wg sync.WaitGroup

func main() {
    wg.Add(1) // 添加一个协程
	go test() // 启动一个协程
	for i := 0; i < 10; i++ {
		fmt.Println("main ---------------", i)
		time.Sleep(time.Millisecond * 500)
	}
    wg.Wait() // 等待所有协程执行完
}
func test() {
	for i := 0; i < 10; i++ {
		fmt.Println("test ---------------", i)
		time.Sleep(time.Millisecond * 1000)
	}
    wg.Done() // 完成一个协程
}

Add, Wait, Done 需要一起出现
```

## 管道

使用关键字**chan**定义一个管道(注意：管道也是地址引用型)

```go
var ch chan int // 类型为 int 型
ch = make(chan int, 10) // 给管道ch分配10个空间
ch <- 10 // 将10写入管道ch中
value := <- ch // 取出管道中的第一个值，也可匿名取出 <- ch
close(ch) // 关闭管道
```

管道中的值遵循先进先出，一旦没有导致取不出或者满了导致放不了都会报错，错误如下

```go
fatal error: all goroutines are asleep - deadlock!
```

使用for range 遍历管道时要先关闭管道

```go
func main() {
	ch := make(chan int, 10)
	for i := 0; i < 10; i++ {
		ch <- i
	}
	close(ch)
	for v := range ch{
		fmt.Println(v)
	}
}
```

**单向管道**

```go
chan<- // 只写
<-chan // 只读

ch1 = make(chan<- int, 10) // 只写的int管道
ch2 = make(<-chan int, 10) // 只读的int管道

// 形参表示
func fn1(ch chan<- int){} 
func fn2(ch <-chan int){}
```

## 管道和线程的使用

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

var wg sync.WaitGroup

func fn1(ch chan int){
	for i := 1; i <= 10; i++ {
		ch <- i
		fmt.Println("写入", i, "成功")
		time.Sleep(time.Millisecond * 100)
	}
	close(ch)
	wg.Done()
}

func fn2(ch chan int) {
	for v := range ch{
	   fmt.Println("读取", v, "成功")
	   time.Sleep(time.Millisecond * 10)
	}
	wg.Done()
}

func main() {
	ch := make(chan int, 10)
	wg.Add(2)
	go fn1(ch)
	go fn2(ch)
	wg.Wait()
}
```

```go
package main

import (
	"fmt"
	"sync"
)

var wg sync.WaitGroup

func putNum(numChan chan int) {
	for i := 2; i <= 1200000; i++ {
		numChan <- i
	}
	close(numChan)
	wg.Done()
}

func getPrime(numChan chan int, primeChan chan int, exitChan chan bool)  {
	for num := range numChan {
		flag := true
		for i := 2; i < num; i++ {
			if num % i == 0 {
				flag = false
				break
			}
		}
		if flag {
			primeChan <- num
		}
	}
	// 存放素数管道完成数 +1
	exitChan <- true
	wg.Done()
}

func printPrime(primeChan chan int) {
	for v := range primeChan{
		fmt.Println(v)
	}
	wg.Done()
}

func main() {
	numChan := make(chan int, 1000) // 存放数字的管道
	primeChan := make(chan int, 1000) // 存放素数的管道
	exitChan := make(chan bool, 16) // 存放素数管道完成数
	count := 20 // 判断素数的协程数
	wg.Add(1)
	go putNum(numChan)

	for i := 0; i < count; i++ {
		wg.Add(1)
		go getPrime(numChan, primeChan, exitChan)
	}

	wg.Add(1)
	go printPrime(primeChan)

	wg.Add(1)
	go func() {
		for i := 0; i < count; i++ {
			<- exitChan // 存放素数管道完成数 -1
		}
		// 判断存放素数管道完成数都运行完后关闭管道
		close(primeChan)
		wg.Done()
	}()

	wg.Wait()
	fmt.Println("执行结束......")
}
```

## 多路复用

多路复用使用**select**关键字，通常搭配**for**使用，让**case**中的代码随机的执行，也叫**并发**

```go
package main

import "fmt"

func main() {
	intChan := make(chan int, 10)
	strChan := make(chan string, 10)

	for i := 0; i < 10; i++ {
		intChan <- i
		strChan <- "hello" + fmt.Sprint(i)
	}

	for {
		select {
		case v := <- intChan:
			fmt.Println("intChan 读取的数据", v)
		case v := <-strChan:
			fmt.Println("strChan 读取的数据", v)
		default:
			fmt.Println("结束")
			return
		}
	}
}
```

## goroutine异常处理

使用defer + recover进行异常捕获

```go
defer func(){
    if err := recover(); err != nil {
        fmt.Println("发生错误", err)
    } 
}()
```

```go
package main

import (
	"fmt"
	"time"
)

func fn1() {
	defer func(){
		if err := recover(); err != nil {
			fmt.Println("发生错误", err)
		}
	}()
	var name map[int]string
	name[0] = "test"
}

func fn2() {
	for i := 0; i < 10; i++ {
		fmt.Println("hell0", i)
	}
}

func main() {
	go fn1()
	go fn2()
	time.Sleep(time.Second * 5)
}
```
