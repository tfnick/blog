---

title: 'Go语言FAQ-不间断更新'
date: 2021-03-25
tags: [折腾]
toc: true

---

## 资料

[go语言特性快速上手](https://gitee.com/goflyfox/gostudy/tree/master/doc)

[Go代码在线运行](https://play.golang.org/)

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

```
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

### 协程

### 异常处理
