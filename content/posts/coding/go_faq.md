---

title: 'Go语言FAQ-不间断更新'
date: 2021-03-25
tags: [折腾]
toc: true

---

## 资料

[Go语言特性快速上手](https://gitee.com/goflyfox/gostudy/tree/master/doc)

[Go代码在线运行](https://c.runoob.com/compile/21)

[Go语言设计模式](https://github.com/senghoo/golang-design-pattern)

[通用设计模式电子版](https://refactoringguru.cn/design-patterns/adapter)

## FAQ （类比java）

### 程序启动过程分析

init()函数会在每个包完成初始化后自动执行，并且执行优先级比main函数高。init 函数通常被用来：

- 对变量进行初始化
- 检查/修复程序的状态
- 注册
- 进行一次计算

![](https://img-blog.csdn.net/20180308161418275)

Go要求非常严格，不允许引用不使用的包。但是有时你引用包只是为了调用init函数去做一些初始化工作。此时空标识符（也就是下划线）的作用就是为了解决这个问题。

```go
import _ "image/png"
```

### 变量访问权限

Golang中根据首字母的大小写来确定可以访问的权限。无论是 `方法名`、`常量`、`变量名`还是`结构体的名称` ，如果`首字母大写`，则`可以被其他的包访问`；如果`首字母小写`，则`只能在本包中使用`。

```go
package demo

var Chat = &chatApi{}

type chatApi struct{
   Version string
}
```
<center>chatApi小写开头，表示其只能在package demo内被访问；Chat大写开头，表示可以被其他包访问</center>

备注：一般来说struct的属性推荐以大写开头，否则Json序列化时会将小写开头的属性排除掉。

### 对象 方法 与 “继承”

#### 对象与继承

```go
package main

import "fmt"

type Company struct {
    CompanyName string
    CompanyAddr string
}

type Staff struct {
    Name string
    Age int
    Gender string
    Position string
    Company // 匿名对象
}

func main()  {
    myCom := Company{
        CompanyName: "Tencent",
        CompanyAddr: "深圳市南山区",
    }
    
    staffInfo := Staff{
        Name:     "小明",
        Age:      28,
        Gender:   "男",
        Position: "云计算开发工程师",
        Company: myCom,
    }

    fmt.Printf("%s 在 %s 工作\n", staffInfo.Name, staffInfo.CompanyName)
    fmt.Printf("%s 在 %s 工作\n", staffInfo.Name, staffInfo.Company.CompanyName)
}
```

#### 对象与方法绑定

```go
package main

import "fmt"

// 声明一个 Profile 的结构体
type Profile struct {
	name   string
	age    int
	gender string
	mother *Profile // 指针
	father *Profile // 指针
}

// 推荐使用指针绑定方法的方式
func (person *Profile) increase_age() {
	person.age += 1
}

// 禁止按实例值绑定方法的方式
func (person Profile) print_age() {
	fmt.Printf("当前年龄：%d\n", person.age)
}

func main() {
	myself := Profile{name: "小明", age: 24, gender: "male"}
	fmt.Printf("当前年龄：%d\n", myself.age)
	myself.increase_age()
	fmt.Printf("当前年龄：%d", myself.age)
}


```

至此，我们知道了两种绑定方法的方式：

- 以值做为方法接收者，适应于
  类似只读模式
- 以指针做为方法接收者，适应于
  你需要在方法内部改变结构体内容的时候
  出于性能的问题，当结构体过大的时候

有些情况下，以值或指针做为接收者都可以，但是考虑到代码一致性，建议都使用`指针做为接收者`。


### 变量定义

```go
package main

import "fmt"

func main() {
	// hello world
	fmt.Println("hello world")

	fmt.Println("##################### arrays")
	arrays()

	fmt.Println("##################### slice")
	slice()

	fmt.Println("##################### mapFunc")
	mapFunc()

	fmt.Println("##################### rangeFunc")
	rangeFunc()
}

// 数组
func arrays() {
	// 这里我们创建了一个数组 test1 来存放刚好 5 个 int。
	// 元素的类型和长度都是数组类型的一部分。
	// 数组默认是零值的，对于 int 数组来说也就是 0。
	var test1 [6]int
	fmt.Println("内容:", test1)
	// 我们可以使用 array[index] = value 语法来设置数组指定位置的值，或者用 array[index] 得到值。
	test1[4] = 100
	fmt.Println("设置:", test1)
	fmt.Println("获取:", test1[4])
	// 使用内置函数 len 返回数组的长度
	fmt.Println("长度:", len(test1))

	// 使用这个语法在一行内初始化一个数组
	test2 := [6]int{1, 2, 3, 4, 5, 6}
	fmt.Println("数据:", test2)

	// 数组的存储类型是单一的，但是你可以组合这些数据来构造多维的数据结构。
	var twoTest [3][4]int
	for i := 0; i < 3; i++ {
		for j := 0; j < 4; j++ {
			twoTest[i][j] = i + j
		}
	}
	// 注意，在使用 fmt.Println 来打印数组的时候，会使用[v1 v2 v3 ...] 的格式显示
	fmt.Println("二维: ", twoTest)
}

// 切片
func slice() {
	// Slice 是 Go 中一个关键的数据类型，是一个比数组更加强大的序列接口

	// 不像数组，slice 的类型仅由它所包含的元素决定（不像数组中还需要元素的个数）。
	// 要创建一个长度非零的空slice，需要使用内建的方法 make。
	// 这里我们创建了一个长度为3的 string 类型 slice（初始化为零值）。
	test1 := make([]string, 3)
	fmt.Println("数据:", test1)
	// 我们可以和数组一样设置和得到值
	test1[0] = "A"
	test1[1] = "C"
	test1[2] = "B"
	fmt.Println("数据:", test1)
	fmt.Println("获取:", test1[2])
	// 如你所料，len 返回 slice 的长度
	fmt.Println("长度:", len(test1))

	// 作为基本操作的补充，slice 支持比数组更多的操作。
	// 其中一个是内建的 append，它返回一个包含了一个或者多个新值的 slice。
	// 注意我们接受返回由 append返回的新的 slice 值。
	test1 = append(test1, "D")
	test1 = append(test1, "E", "F")
	fmt.Println("追加:", test1)

	// Slice 也可以被 copy。这里我们创建一个空的和 test1 有相同长度的 slice test2，并且将 test1 复制给 test2。
	test2 := make([]string, len(test1))
	copy(test2, test1)
	fmt.Println("拷贝:", test2)
	// Slice 支持通过 slice[low:high] 语法进行“切片”操作。例如，这里得到一个包含元素 test1[2], test1[3],test1[4] 的 slice。
	l := test1[2:5]
	fmt.Println("切片1:", l)
	// 这个 slice 从 test1[0] 到（但是不包含）test1[5]。
	l = test1[:5]
	fmt.Println("切片2:", l)
	// 这个 slice 从（包含）test1[2] 到 slice 的后一个值。
	l = test1[2:]
	fmt.Println("切片3:", l)
	// 我们可以在一行代码中声明并初始化一个 slice 变量。
	t := []string{"g", "h", "i"}
	fmt.Println("数据:", t)

	// Slice 可以组成多维数据结构。内部的 slice 长度可以不同，这和多位数组不同。
	twoTest := make([][]int, 3)
	for i := 0; i < 3; i++ {
		innerLen := i + 1
		twoTest[i] = make([]int, innerLen)
		for j := 0; j < innerLen; j++ {
			twoTest[i][j] = i + j
		}
	}
	// 注意，slice 和数组不同，虽然它们通过 fmt.Println 输出差不多。
	fmt.Println("二维: ", twoTest)
}

// 键值对 key/value
func mapFunc() {
	// 要创建一个空 map，需要使用内建的 make:make(map[key-type]val-type).
	map1 := make(map[string]int)
	// 使用典型的 make[key] = val 语法来设置键值对。
	map1["k1"] = 7
	map1["k2"] = 13
	// 使用例如 Println 来打印一个 map 将会输出所有的键值对。
	fmt.Println("数据:", map1)
	// 使用 name[key] 来获取一个键的值
	v1 := map1["k1"]
	fmt.Println("值: ", v1)
	// 当对一个 map 调用内建的 len 时，返回的是键值对数目
	fmt.Println("长度:", len(map1))
	// 内建的 delete 可以从一个 map 中移除键值对
	delete(map1, "k2")
	fmt.Println("数据:", map1)
	// 当从一个 map 中取值时，可选的第二返回值指示这个键是在这个 map 中。
	// 这可以用来消除键不存在和键有零值，像 0 或者 "" 而产生的歧义。
	_, prs := map1["k2"]
	fmt.Println("是否存在:", prs)
	// 你也可以通过这个语法在同一行申明和初始化一个新的map。
	map2 := map[string]int{"F": 1, "B": 2}
	// 注意一个 map 在使用 fmt.Println 打印的时候，是以 map[k:v k:v]的格式输出的。
	fmt.Println("数据:", map2)
}

// Range 遍历
func rangeFunc() {
	// 这里我们使用 range 来统计一个 slice 的元素个数。数组也可以采用这种方法。
	array1 := []int{2, 3, 4}
	sum := 0
	for _, num := range array1 {
		sum += num
	}
	fmt.Println("求和:", sum)

	// range 在数组和 slice 中都同样提供每个项的索引和值。
	// 上面我们不需要索引，所以我们使用 空值定义符_ 来忽略它。
	// 有时候我们实际上是需要这个索引的。
	for i, num := range array1 {
		if num == 3 {
			fmt.Println("索引:", i)
		}
	}

	// range 在 map 中迭代键值对。
	map1 := map[string]string{"A": "苹果", "B": "香蕉"}
	for k, v := range map1 {
		fmt.Printf("%s -> %s\n", k, v)
	}
	for k := range map1 {
		fmt.Println("键:", k)
	}

	// range 在字符串中迭代 unicode 编码。
	// 第一个返回值是rune 的起始字节位置，然后第二个是 rune 自己。
	for i, c := range "abA" {
		fmt.Println(i, c)
	}
}

```

### 方法定义

```go
func [(structObject *StructObject)] function_name( [parameter list] ) [return_types] {
   函数体
}
```

实例

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	// hello world
	fmt.Println("hello world")

	// 1. 加法
	res := plus(1, 2)
	fmt.Println("1+2 =", res)
	res = plusPlus(1, 2, 3)
	fmt.Println("1+2+3 =", res)

	// 2. 多值返回
	// 这里我们通过多赋值 操作来使用这两个不同的返回值。
	a, b := vals()
	fmt.Println(a)
	fmt.Println(b)
	// 如果你仅仅想返回值的一部分的话，你可以使用空白定义符 _。
	_, c := vals()
	fmt.Println(c)

	// 3. 可变参数
	// 变参函数使用常规的调用方式，除了参数比较特殊。
	sum(1, 2)
	sum(1, 2, 3)
	// 如果你的 slice 已经有了多个值，想把它们作为变参使用，你要这样调用 func(slice...)。
	nums := []int{1, 2, 3, 4}
	sum(nums...)

	// 4. 闭包
	// 我们调用 intSeq 函数，将返回值（也是一个函数）赋给nextInt。
	// 这个函数的值包含了自己的值 i，这样在每次调用 nextInt 时都会更新 i 的值。
	nextInt := intSeq()
	// 通过多次调用 nextInt 来看看闭包的效果。
	fmt.Println(nextInt())
	fmt.Println(nextInt())
	fmt.Println(nextInt())
	// 为了确认这个状态对于这个特定的函数是唯一的，我们重新创建并测试一下。
	newInts := intSeq()
	fmt.Println(newInts())

	// 5. 递归
	fmt.Println(fact(7))

	// 6. 字符拆分
	strArr := flatMap("aa;bb;dd")
	fmt.Println(strArr)
	
	tmpStr := "ab;xxxx"
	var noInitStr string
	fmt.Println(flatMap(tmpStr ))
	fmt.Println(flatMapOnPoint(&noInitStr))
}


// 函数
// 这里是一个函数，接受两个 int 并且以 int 返回它们的和
func plus(a int, b int) int {
	// Go 需要明确的返回值，例如，它不会自动返回最后一个表达式的值
	return a + b
}

// (int, int) 在这个函数中标志着这个函数返回 2 个 int。
func plusPlus(a, b, c int) int {
	return a + b + c
}

// 多返回值函数
func vals() (int, int) {
	return 3, 7
}

// 变参函数
func sum(nums ...int) {
	fmt.Print(nums, " ")
	total := 0
	for _, num := range nums {
		total += num
	}
	fmt.Println(total)
}

// 闭包
// 这个 intSeq 函数返回另一个在 intSeq 函数体内定义的匿名函数。
// 这个返回的函数使用闭包的方式 隐藏 变量 i。
func intSeq() func() int {
	i := 0
	return func() int {
		i += 1
		return i
	}
}

// 递归
// face 函数在到达 face(0) 前一直调用自身。
func fact(n int) int {
	if n == 0 {
		return 1
	}
	return n * fact(n-1)
}

// 返回数组1
func flatMap(str string) []string {
	if str == "" {
		a := []string{str}
		return a
	}
	a := strings.Split(str, ";")
	return a
}

// 返回数组2
func flatMapOnPoint(str *string) []string {
	if str == nil {
		return nil
	}
	a := strings.Split(*str, ";")
	return a
}


```

### interface

```go
package main

import "fmt"
import "math"

// 这里是一个几何体的基本接口。
type Geometry interface {
	area() float64
	perim() float64
}

// 在我们的例子中，我们将让 rect 和 circle 实现这个接口
type Rect struct {
	width, height float64
}
type Circle struct {
	radius float64
}

// 要在 Go 中实现一个接口，我们只需要实现接口中的所有方法。
// 这里我们让 rect 实现了 geometry 接口。
func (r *Rect) area() float64 {
	return r.width * r.height
}
func (r *Rect) perim() float64 {
	return 2*r.width + 2*r.height
}

// circle 的实现。
func (c *Circle) area() float64 {
	return math.Pi * c.radius * c.radius
}
func (c *Circle) perim() float64 {
	return 2 * math.Pi * c.radius
}

// 如果一个变量的是接口类型，那么我们可以调用这个被命名的接口中的方法。
// 这里有一个一通用的 measure 函数，利用这个特性，它可以用在任何 geometry 上。
func measure(g Geometry) {
	fmt.Println(g)
	fmt.Println(g.area())
	fmt.Println(g.perim())
}
func main() {
	//area() perim() 与*Circle，*Rect绑定时
	r := &Rect{width: 3, height: 4}
	c := &Circle{radius: 5}
	measure(r)
	measure(c)
	
	//area() perim() 与Circle，Rect绑定时
	//r := Rect{width: 3, height: 4}
	//c := Circle{radius: 5}
	//measure(r)
	//measure(c)
}


```

`空接口(interface{})`不包含任何的方法，正因为如此，所有的类型都实现了空接口，因此空接口可以存储任意类型，包括基本类型、结构体、甚至指针。

```go

package main

import "fmt"

var v1 interface{} = 1     // 将int类型赋值给interface{}
var v2 interface{} = "abc" // 将string类型赋值给interface{}
var v3 interface{} = &v2   // 将*interface{}类型赋值给interface{}
var v4 interface{} = struct{ X int }{1} //将struct类型赋值给interface{}
var v5 interface{} = &struct{ X int }{1} //将&struct类型赋值给interface{}

//interface{}作为参数,...表示动态个数参数
func MyPrint(args ...interface{}){
	fmt.Println(args)
}

func main(){
	MyPrint("interface{} as function's parameter")
}

//备注：interface表示一组方法的集合
    
```

### 注解，泛型

注解不支持，泛型有计划支持（目前还不支持）

### nil

什么是nil,根据官方定义，nil是预定义标识，代表了指针`pointer`、`通道channel`、`函数func`、`接口interface`、`map`、`切片slice`类型变量的零值。


```go
bool    -> false
numbers -> 0
string  -> ""

pointers -> nil
slices -> nil
maps -> nil
channels -> nil
functions -> nil
interfaces -> nil

```

> 注意：struct类型零值不是nil，而是各字段值为对应类型的零值。且不能将struct类型和nil进行等值判断，语法校验不通过。

针对数组，稍微有点复杂：

```go
var t []string   // t -> nil

tt := []string{} // tt -> [] 空数组
```

> Golang官方建议，当声明空数组时，推荐使用第一种方法；但万事不是绝对的，当在Json编码时，推荐的是后两种方式，因为一个nil空数组会被编码为null，但非nil空数组会被编码为JSON array []，这样方便前端解析。



### 异常处理



#### error是一个接口

error类型是一个接口类型，这是它的定义：

```go
type error interface {
    Error() string
}
```

#### 返回error的函数定义

```go
package main

import "errors"
import "fmt"

// 按照惯例，错误通常是最后一个返回值并且是 error 类型，一个内建的接口。
func f1(arg int) (int, error) {
	// errors.New 构造一个使用给定的错误信息的基本error 值。
	if arg == 42 {
		return -1, errors.New("can't work with 42")
	}
	// 返回错误值为 nil 代表没有错误。
	return arg + 3, nil
}

func main() {
	//如果不关心返回的error，则用_表示
	x, _ := f1(1)
	fmt.Println(x)
}
```

#### defer函数

defer函数一般定义在某个方法中，表示当前defer所在的函数结束时必然被执行的操作；有点类似java中的finally的功能。



```go
package main

import "fmt"
import "os"

func main() {
	// 假设我们想要创建一个文件，向它进行写操作，然后在结束时关闭它。
	// 这里展示了如何通过 defer 来做到这一切。
	f := createFile("D:/defer.txt") // f := createFile("/tmp/defer.txt")

    // 故此处defer只是声明，它对应的函数closeFile(f) 真正执行是在 (main)函数最后一个操作writeFile(f)之后。
	defer closeFile(f)
	writeFile(f)
}
func createFile(p string) *os.File {
	fmt.Println("creating")
	f, err := os.Create(p)
	if err != nil {
		panic(err)
	}
	return f
}
func writeFile(f *os.File) {
	fmt.Println("writing")
	fmt.Fprintln(f, "data")
}
func closeFile(f *os.File) {
	fmt.Println("closing")
	f.Close()
}
```



#### 抛出异常



Go的类型系统会在编译时捕获很多错误，但有些错误只能在运行时检查，如数组访问越界、空指针引用等。这些运行时错误会引起painc异常。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	// 我们将在这个网站中使用 panic 来检查预期外的错误。这个是唯一一个为 panic 准备的例子。
	panic("一个异常")

	// panic 的一个基本用法就是在一个函数返回了错误值但是我们并不知道（或者不想）处理时终止运行。
	// 这里是一个在创建一个新文件时返回异常错误时的panic 用法。
	fmt.Println("继续")
	_, err := os.Create("/tmp/file")
	if err != nil {
		panic(err)
	}
	// 运行程序将会引起 panic，输出一个错误消息和 Go 运行时栈信息，并且返回一个非零的状态码。
}
```



#### 捕获异常



通常来说，不应该对panic异常做任何处理，但有时，也许我们可以从异常中恢复，至少我们可以在程序崩溃前，做一些操作。举个例子，当web服务器遇到不可预料的严重问题时，在崩溃前应该将所有的连接关闭；如果不做任何处理，会使得客户端一直处于等待状态。如果web服务器还在开发阶段，服务器甚至可以将异常信息反馈到客户端，帮助调试。

如果在deferred函数中调用了内置函数recover，并且定义该defer语句的函数发生了panic异常，recover会使程序从panic中恢复，并返回panic value。导致panic异常的函数不会继续运行，但能正常返回。在未发生panic时调用recover，recover会返回nil。



```go
package main

import "fmt"

func main() {
	// 这里我们对异常进行了捕获
	defer func() {
		if p := recover(); p != nil {
			err := fmt.Errorf("internal error: %v", p)
			if err != nil {
				fmt.Println(err)
			}
		}
	}()

	// 我们将在这个网站中使用 panic 来检查预期外的错误。这个是唯一一个为 panic 准备的例子。
	panic("一个异常")

}
```



### 协程

#### 协程（Goroutines）

在Go语言中，每一个并发的执行单元叫作一个goroutine。，我们只需要通过 go 关键字来开启 goroutine 即可。goroutine 是轻量级线程，goroutine 的调度是由 Golang 运行时进行管理的。

goroutine 语法格式：

```
//匿名函数形式
go func(形参){
	//这里定义函数操作内容
}(实参)

//或者非匿名函数形式

go 函数名(参数列表)
```



```go
package main

import "fmt"

func f(from string) {
	for i := 0; i < 3; i++ {
		fmt.Println(from, ":", i)
	}
}
func main() {
	// 假设我们有一个函数叫做 f(s)。
	// 我们使用一般的方式调并同时运行。
	f("direct")
	// 使用 go f(s) 在一个 Go 协程中调用这个函数。
	// 这个新的 Go 协程将会并行的执行这个函数调用。
	go f("goroutine")
	// 你也可以为匿名函数启动一个 Go 协程。
	go func(msg string) {
		fmt.Println(msg)
	}("going")

	// 现在这两个 Go 协程在独立的 Go 协程中异步的运行，所以我们需要等它们执行结束。
	// 这里的 Scanln 代码需要我们在程序退出前按下任意键结束。
	var input string
	fmt.Scanln(&input)
	fmt.Println("done")
	// 当我们运行这个程序时，将首先看到阻塞式调用的输出，然后是两个 Go 协程的交替输出。
	// 这种交替的情况表示 Go 运行时是以异步的方式运行协程的。
}
```



#### 通道（channel）

如果说goroutine是Go语言程序的并发体的话，那么channels则是它们之间的通信机制。通道（channel）是用来传递数据的一个数据结构。

通道可用于两个 goroutine 之间通过传递一个指定类型的值来同步运行和通讯。操作符 `<-` 用于指定通道的方向，发送或接收。如果未指定方向，则为双向通道。

```
ch <- v    // 把 v 发送到通道 ch
v := <-ch  // 从 ch 接收数据
           // 并把值赋给 v
```

声明一个通道很简单，我们使用chan关键字即可，通道在使用前必须先创建：

```
ch := make(chan int)

```

示例：

```go
package main

import (
	"fmt"
)

// 通道 是连接多个 Go 协程的管道。
// 你可以从一个 Go 协程将值发送到通道，然后在别的 Go 协程中接收。
func main() {
	// 使用 make(chan val-type) 创建一个新的通道。
	// 通道类型就是他们需要传递值的类型。
	messages := make(chan string)
	// 使用 channel <- 语法 发送 一个新的值到通道中。
	// 这里我们在一个新的 Go 协程中发送 "ping" 到上面创建的messages 通道中。
	go func() {
		messages <- "ping"
	}()
	// 使用 <-channel 语法从通道中 接收 一个值。
	// 这里将接收我们在上面发送的 "ping" 消息并打印出来。
	msg := <-messages
	fmt.Println(msg)
	// 我们运行程序时，通过通道，消息 "ping" 成功的从一个 Go 协程传到另一个中。
	// 默认发送和接收操作是阻塞的，直到发送方和接收方都准备完毕。
	// 这个特性允许我们，不使用任何其它的同步操作，来在程序结尾等待消息 "ping"。
}
```



#### 通道缓冲区

通道可以设置缓冲区，通过 make 的第二个参数指定缓冲区大小：

```go
ch := make(chan int, 100)
```

带缓冲区的通道允许发送端的数据发送和接收端的数据获取处于异步状态，就是说发送端发送的数据可以放在缓冲区里面，可以等待接收端去获取数据，而不是立刻需要接收端去获取数据。

不过由于缓冲区的大小是有限的，所以还是必须有接收端来接收数据的，否则缓冲区一满，数据发送端就无法再发送数据了。

**注意**：如果通道不带缓冲，发送方会阻塞直到接收方从通道中接收了值。如果通道带缓冲，发送方则会阻塞直到发送的值被拷贝到缓冲区内；如果缓冲区已满，则意味着需要等待直到某个接收方获取到一个值。接收方在有值可以接收之前会一直阻塞。

示例：

```go
package main

import "fmt"

// 默认通道是 无缓冲 的，这意味着只有在对应的接收（<- chan）通道准备好接收时，才允许进行发送（chan <-）。
// 可缓存通道允许在没有对应接收方的情况下，缓存限定数量的值。
func main() {
	// 这里我们 make 了一个通道，最多允许缓存 2 个值。
	messages := make(chan string, 2)
	// 因为这个通道是有缓冲区的，即使没有一个对应的并发接收方，我们仍然可以发送这些值。
	messages <- "buffered"
	messages <- "channel"
	// 然后我们可以像前面一样接收这两个值。
	fmt.Println(<-messages)
	fmt.Println(<-messages)
}
```



#### 同步实现

我们可以通过channel实现同步，典型的生产者消费者模式，例子：

##### 方案1

```go
package main

import (
    "fmt"
    "time"
)

func producer(ch chan int, count int) {
    for i := 1; i <= count; i++ {
        fmt.Println("大妈做第", i, "个面包")
        ch <- i
        
        // 睡眠一下，可以让整个生产消费看得更清晰点
        time.Sleep(time.Second * time.Duration(1))
    }
}

func consumer(ch chan int, count int) {
    for v := range ch {
        fmt.Println("大叔吃了第", v, "个面包")
        count--
        if count == 0 {
            fmt.Println("没面包了，大叔也饱了")
            close(ch)
        }
    }
}

func main() {
    ch := make(chan int)
    count := 5
    go producer(ch, count)
    consumer(ch, count)
}

```

上面代码中，我们另外起了个 goroutine 让大妈来生产5个面包（实际就是往channel中写数据），主 goroutine 让大叔不断吃面包（从channel中读数据）。我们来看一下输出结果：

```text
大妈做第 1 个面包
大叔吃了第 1 个面包
大妈做第 2 个面包
大叔吃了第 2 个面包
大妈做第 3 个面包
大叔吃了第 3 个面包
大妈做第 4 个面包
大叔吃了第 4 个面包
大妈做第 5 个面包
大叔吃了第 5 个面包
没面包了，大叔也饱了
```

上面代码，我们用 for-range 来读取 channel的数据，for-range 是一个很有特色的语句，有以下特点：

- 如果 channel 已经被关闭，它还是会继续执行，直到所有值被取完，然后退出执行
- 如果通道没有关闭，但是channel没有可读取的数据，它则会阻塞在 range 这句位置，直到被唤醒。
- 如果 channel 是 nil，那么同样符合我们上面说的的原则，读取会被阻塞，也就是会一直阻塞在 range 位置。

我们来验证一下，我们把上面代码中的 *close(ch)* 移到主协程中试试：

```go
package main

import (
    "fmt"
    "time"
)

func producer(ch chan int, count int) {
    for i := 1; i <= count; i++ {
        fmt.Println("大妈做第", i, "个面包")
        ch <- i
        
        // 睡眠一下，可以让整个生产消费看得更清晰点
        time.Sleep(time.Second * time.Duration(1))
    }
}

func consumer(ch chan int, count int) {
    for v := range ch {
        fmt.Println("大叔吃了第", v, "个面包")
        count--
        if count == 0 {
            fmt.Println("没面包了，大叔也饱了")
        }
    }
}

func main() {
    ch := make(chan int)
    count := 5
    go producer(ch, count)
    consumer(ch, count)
    close(ch)
}

```

打印输出：

```text
大妈做第 1 个面包
大叔吃了第 1 个面包
大妈做第 2 个面包
大叔吃了第 2 个面包
大妈做第 3 个面包
大叔吃了第 3 个面包
大妈做第 4 个面包
大叔吃了第 4 个面包
大妈做第 5 个面包
大叔吃了第 5 个面包
没面包了，大叔也饱了
fatal error: all goroutines are asleep - deadlock!

```

果然阻塞掉了，最终形成了死锁，抛出异常了。



##### 方案2

`sync.WaitGroup`是官方提供的一个包，用于控制协程同步。通常场景，我们需要等待一组协程都执行完成以后，做后面的处理。如果不使用这个包的话，可能会像下面这样去实现



```go
package main

import "fmt"

const SIZE = 3

func main() {
	fmt.Println("start")
    ch := make(chan int, 3)
    for i := 0;i<SIZE;i++ {
        go func(i int) {
            ch <- i
        }(i)
    }
    for i := 0;i<SIZE;i++ {
        fmt.Println(<- ch)
    }
	fmt.Println("end")
}
```

等同于:

```go
package main

import (
    "fmt"
    "sync"
)

func main() {
	fmt.Println("start")
    wg := sync.WaitGroup{}
    for i := 0;i<3;i++ {
        wg.Add(1)
        go func(i int) {
            fmt.Printf("hello %d \n", i)
            wg.Done()
        }(i)
    }
    wg.Wait()
	fmt.Println("done")
}
```



#### 通道方向示例

```go
package main

import "fmt"

// 当使用通道作为函数的参数时，你可以指定这个通道是不是只用来发送或者接收值。
// 这个特性提升了程序的类型安全性。
func ping(pings chan <- string, msg string) {
	// ping 函数定义了一个只允许发送数据的通道。
	// 尝试使用这个通道来接收数据将会得到一个编译时错误。
	pings <- msg
}
func pong(pings <-chan string, pongs chan <- string) {
	// pong 函数允许通道（pings）来接收数据，另一通道（pongs）来发送数据。
	msg := <-pings
	pongs <- msg
}
func main() {
	pings := make(chan string, 1)
	pongs := make(chan string, 1)
	ping(pings, "passed message")
	pong(pings, pongs)
	fmt.Println(<-pongs)
}
```



#### 遍历通道与关闭通道

Go 通过 range 关键字来实现遍历读取到的数据，类似于与数组或切片。格式如下：

```go
v, ok := <-ch
```

如果通道接收不到数据后 ok 就为 false，这时通道就可以使用 **close()** 函数来关闭。



#### 通道选择器（select）

````go
select {
case <-ch1:
    // ...
case x := <-ch2:
    // ...use x...
case ch3 <- y:
    // ...
default:
    // ...
}
````


select语句的一般形式。和switch语句稍微有点相似，也会有几个case和最后的default选择支。每一个case代表一个通信操作(在某个channel上进行发送或者接收)并且会包含一些语句组成的一个语句块。一个接收表达式可能只包含接收表达式自身(译注：不把接收到的值赋值给变量什么的)，就像上面的第一个case，或者包含在一个简短的变量声明中，像第二个case里一样；第二种形式让你能够引用接收到的值。

select会等待case中有能够执行的case时去执行。当条件满足时，select才会去通信并执行case之后的语句；这时候其它通信是不会执行的。一个没有任何case的select语句写作select{}，会永远地等待下去。

示例：

```go
package main

import "time"
import "fmt"

// Go 的通道选择器 让你可以同时等待多个通道操作。
// Go 协程和通道以及选择器的结合是 Go 的一个强大特性。
func main() {
	// 在我们的例子中，我们将从两个通道中选择。
	c1 := make(chan string)
	c2 := make(chan string)
	// 各个通道将在若干时间后接收一个值，这个用来模拟例如并行的 Go 协程中阻塞的 RPC 操作
	go func() {
		time.Sleep(time.Second * 1)
		c1 <- "one"
	}()
	go func() {
		time.Sleep(time.Second * 2)
		c2 <- "two"
	}()

	// 我们使用 select 关键字来同时等待这两个值，并打印各自接收到的值。
	for i := 0; i < 2; i++ {
		select {
		case msg1 := <-c1:
			fmt.Println("received", msg1)
		case msg2 := <-c2:
			fmt.Println("received", msg2)
		}
	}
	// 我们首先接收到值 "one"，然后就是预料中的 "two"了。
	// 注意从第一次和第二次 Sleeps 并发执行，总共仅运行了两秒左右。
}
```



#### 超时实现

```go
package main

import "time"
import "fmt"

// 超时 对于一个连接外部资源，或者其它一些需要花费执行时间的操作的程序而言是很重要的。
// 得益于通道和 select，在 Go中实现超时操作是简洁而优雅的。
func main() {
	c1 := make(chan string, 1)
	// 在我们的例子中，假如我们执行一个外部调用，并在 2 秒后通过通道 c1 返回它的执行结果。
	go func() {
		time.Sleep(time.Second * 2)
		c1 <- "result 1"
	}()

	// 这里是使用 select 实现一个超时操作。res := <- c1 等待结果，<-Time.After 等待超时时间 1 秒后发送的值。
	// 由于 select 默认处理第一个已准备好的接收操作，如果这个操作超过了允许的 1 秒的话，将会执行超时 case。
	select {
	case res := <-c1:
		fmt.Println(res)
	case <-time.After(time.Second * 1):
		fmt.Println("timeout 1")
	}

	// 如果我允许一个长一点的超时时间 3 秒，将会成功的从 c2接收到值，并且打印出结果。
	c2 := make(chan string, 1)
	go func() {
		time.Sleep(time.Second * 2)
		c2 <- "result 2"
	}()

	select {
	case res := <-c2:
		fmt.Println(res)
	case <-time.After(time.Second * 3):
		fmt.Println("timeout 2")
	}
	// 运行这个程序，首先显示运行超时的操作，然后是成功接收的。
	// 使用这个 select 超时方式，需要使用通道传递结果。
	// 这对于一般情况是个好的方式，因为其他重要的 Go 特性是基于通道和select 的。
}
```



#### 非阻塞选择器

```go
package main

import (
	"fmt"
)

// 常规的通过通道发送和接收数据是阻塞的。
// 然而，我们可以使用带一个 default 子句的 select 来实现非阻塞 的发送、接收，甚至是非阻塞的多路 select。
func main() {
	messages := make(chan string)
	signals := make(chan bool)
	// 这里是一个非阻塞接收的例子。
	// 如果在 messages 中存在，然后 select 将这个值带入 <-messages case中。
	// 如果不是，就直接到 default 分支中。
	select {
	case msg := <-messages:
		fmt.Println("received message", msg)
	default:
		fmt.Println("no message received")
	}

	// 一个非阻塞发送的实现方法和上面一样。
	msg := "hi"
	select {
	case messages <- msg:
		fmt.Println("sent message", msg)
	default:
		fmt.Println("no message sent")
	}

	// 我们可以在 default 前使用多个 case 子句来实现一个多路的非阻塞的选择器。
	// 这里我们试图在 messages和 signals 上同时使用非阻塞的接受操作。
	select {
	case msg := <-messages:
		fmt.Println("received message", msg)
	case sig := <-signals:
		fmt.Println("received signal", sig)
	default:
		fmt.Println("no activity")
	}
}
```

