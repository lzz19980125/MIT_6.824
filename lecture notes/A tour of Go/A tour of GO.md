# A tour of GO

## 基础（包，变量，函数）

go的最简版本程序长这个样子：

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello, 世界")
}
```

```go
package main

import (
	"fmt"
	"math/rand"
)

func main() {
	fmt.Println("My favorite number is", rand.Intn(10))
}
```

导出名的首字母需要大写，并且导出名的开头和import包的末尾保持一致，如：`fmt.Println`。

```go
package main

import "fmt"

func add(x int, y int) int {
	return x + y
}


//func add(x, y int) int {
//	return x + y
//}

func swap(x, y string) (string, string) {
	return y, x
}

func main() {
	fmt.Println(add(42, 13))
}

```

函数定义方式：参数先写名字，再写类型，最后写函数返回类型。当多个参数类型一致时，可简写，参见上述代码注释。*** 注意：GO中的函数支持多值返回！***

```go
package main

import "fmt"

var i, j int = 1, 2

func main() {
	var k int
    var c, python, java = true, false, "no!" //简写,省略变量类型
    h, python_, java_ := true, false, "no!"  //用:=省略关键字var
	fmt.Println(i, c, python, java)
}
```

变量定义采用关键字`var`.与函数一样，先定义变量名再写类型。变量若进行初始化，go可根据初始化值自动判别变量类型，因此可进行简写。此外，还可利用`:=`省略关键字`var`，**但`:=`不能够在函数外使用！**

### 基本类型

Go 的基本类型有

```go
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // uint8 的别名

rune // int32 的别名
    // 表示一个 Unicode 码点

float32 float64

complex64 complex128
```

### 类型转换

**GO中必须进行显示类型转换，没有自动装箱机制！**类型转化如下：

```go
package main

import (
	"fmt"
	"math"
)

func main() {
	var x, y int = 3, 4
	var f float64 = math.Sqrt(float64(x*x + y*y))
	var z uint = uint(f)
	fmt.Println(x, y, z)
    //在函数内也可简写为：
    // i := 42
    // f := float64(i)
    // u := uint(f)
}

```

### 常量

常量的声明与变量类似，只不过是使用 `const` 关键字。常量可以是字符、字符串、布尔值或数值，**常量不能用 `:=` 语法声明！**

```go
package main

import "fmt"

const Pi = 3.14

func main() {
	const World = "世界"
	fmt.Println("Hello", World)
	fmt.Println("Happy", Pi, "Day")

	const Truth = true
	fmt.Println("Go rules?", Truth)
}
```

## 基础（流程控制）

### for循环

Go 只有一种循环结构：`for` 循环。**注意**：和 C、Java、JavaScript 之类的语言不同，Go 的 for 语句后面的三个构成部分外没有小括号， 大括号 `{ }` 则是必须的。

```go
package main

import "fmt"

func main() {
	sum := 0
	for i := 0; i < 10; i++ {
		sum += i
	}
	fmt.Println(sum)
}

```

GO中的for循环的初始和后置语句是可选的，如下：

```go
package main

import "fmt"

func main() {
	sum := 1
	for ; sum < 1000; {
		sum += sum
	}
	fmt.Println(sum)
}
```

此时可以去掉for中的分号（这里的for循环就相当于c中的 while）：

```go
package main

import "fmt"

func sqrt(x float64) string {
	if x < 0 {
		return sqrt(-x) + "i"
	}
	return fmt.Sprint(math.Sqrt(x))
}

func main() {
	sum := 1
	for ; sum < 1000; {
		sum += sum
	}
	fmt.Println(sum)
}
```

### if else

Go 的 `if` 语句与 `for` 循环类似，表达式外无需小括号 `( )` ，而大括号 `{ }` 则是必须的。`if`语句可以在条件表达式前执行一个简单的语句，如下：**（注意：该语句声明的变量作用域仅在 `if` 之内！）**

```go
package main

import (
	"fmt"
	"math"
)

func pow(x, n, lim float64) float64 {
	if v := math.Pow(x, n); v < lim {
		return v
    } else {
        fmt.Printf("%g >= %g\n", v, lim)
    }
    // 这里开始就不能使用 v 了
	return lim
}

func main() {
	fmt.Println(
		pow(3, 2, 10),
		pow(3, 3, 20),
	)
}
```

### defer

defer 语句会将函数推迟到外层函数返回之后执行。推迟调用的函数其参数会立即求值，但直到外层函数返回前该函数都不会被调用。**推迟的函数调用会被压入一个栈中。当外层函数返回时，被推迟的函数会按照后进先出的顺序调用。**

```go
package main

import "fmt"

func main() {
	fmt.Println("counting")

	for i := 0; i < 10; i++ {
		defer fmt.Println(i)
	}

	fmt.Println("done")
}

//---------------------------运行结果-------------------------//
counting
done
9
8
7
6
5
4
3
2
1
0
```

### switch

```go
package main

import (
	"fmt"
	"runtime"
)

func main() {
	fmt.Print("Go runs on ")
	switch os := runtime.GOOS; os {
	case "darwin":
		fmt.Println("OS X.")
	case "linux":
		fmt.Println("Linux.")
	default:
		// freebsd, openbsd,
		// plan9, windows...
		fmt.Printf("%s.\n", os)
	}
}
```

与if语句相同，上面的switch语句同样是可以支持在switch之前先执行一个操作。Go 的 switch 语句类似于 C、C++、Java、JavaScript 和 PHP 中的，不过 Go 只运行选定的 case，而非之后所有的 case。 实际上，Go 自动提供了在这些语言中每个 case 后面所需的 `break` 语句。 除非以 `fallthrough` 语句结束，否则分支会自动终止。 Go 的另一点重要的不同在于 switch 的 case 无需为常量，且取值不必为整数。

特别的，经常使用没有条件的switch(对应true)书写一系列的if else判断，代码如下所示：

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	t := time.Now()
	switch {
	case t.Hour() < 12:
		fmt.Println("Good morning!")
	case t.Hour() < 17:
		fmt.Println("Good afternoon.")
	default:
		fmt.Println("Good evening.")
	}
}
```

## 指针，struct，切片和map

### 指针

```go
var p *int
```

`&` 操作符会生成一个指向其操作数的指针：

```go
i := 42
p = &i
```

`*` 操作符表示指针指向的底层值：

```go
fmt.Println(*p) // 通过指针 p 读取 i
*p = 21         // 通过指针 p 设置 i
```

```go
package main

import "fmt"

func main() {
	i, j := 42, 2701

	p := &i         // 指向 i
	fmt.Println(*p) // 通过指针读取 i 的值
	*p = 21         // 通过指针设置 i 的值
	fmt.Println(i)  // 查看 i 的值

	p = &j         // 指向 j
	*p = *p / 37   // 通过指针对 j 进行除法运算
	fmt.Println(j) // 查看 j 的值
}
```

### 结构体

结构体的定义方法如下：

```go
package main

import "fmt"

type Vertex struct {
	X int
	Y int
}

func main() {
	fmt.Println(Vertex{1, 2})
    v := Vertex{1, 2}
	v.X = 4
	fmt.Println(v.X)
    p := &v
	p.X = 1e9
	fmt.Println(v)
}
```

结构体字段可以通过`.`操作符来访问，也同样可以通过结构体指针来访问。

结构体文法通过直接列出字段的值来新分配一个结构体。使用 `Name:` 语法可以仅列出部分字段。（字段名的顺序无关。）特殊的前缀 `&` 返回一个指向结构体的指针。

```go
package main

import "fmt"

type Vertex struct {
	X, Y int
}

var (
	v1 = Vertex{1, 2}  // 创建一个 Vertex 类型的结构体
	v2 = Vertex{X: 1}  // Y:0 被隐式地赋予
	v3 = Vertex{}      // X:0 Y:0
	p  = &Vertex{1, 2} // 创建一个 *Vertex 类型的结构体（指针）
)

func main() {
	fmt.Println(v1, p, v2, v3)
    fmt.Println(v1, *p, v2, v3)
}
```

### 数组与切片

数组的定义方式如下：

```go
var a [10]int
```

```go
package main

import "fmt"

func main() {
	var a [2]string
	a[0] = "Hello"
	a[1] = "World"
	fmt.Println(a[0], a[1])
	fmt.Println(a)

	primes := [6]int{2, 3, 5, 7, 11, 13}
	fmt.Println(primes)
}
```

数组的文法：

```go
[3]bool{true, true, false}
```

切片定义如下：

```go
package main

import "fmt"

func main() {
	primes := [6]int{2, 3, 5, 7, 11, 13}

	var s []int = primes[1:4]
	fmt.Println(s)
}

```

切片本身不存储任何数据，它只是描述了底层数据中的一段。更改切片的元素会修改其底层数组中对应的元素。与它共享底层数组的切片都会观测到这些修改。

切片的文法相当于没有长度的数组文法。即他会构造一个和上面完全相同的数组，然后构建一个引用了它的切片：

```go
[]bool{true, true, false}
```

对于数组

```go
var a [10]int
```

来说，以下切片是等价的：

```go
a[0:10]
a[:10]
a[0:]
a[:]
```

#### 切片的长度和容量（重要）

切片有三个属性：指向原始数组的地址信息，长度以及容量。当以make方法创建切片时，可指定切片的长度和容量，长度即切片从位置0开始，能够看到的元素的数目，**切片对应的底层数组的大小为指定的容量**。

```go
//创建了一个长度和容量都为5的切片
slice1:=make[[]string,5]
//创建了一个长度5，容量为10的切片
slice2:=make[[]string,5,10]
```

虽然创建的切片对应底层数组的大小为 10，但是你不能访问索引值 5 以后的元素。

利用切片创建切片：

```go
package main

import "fmt"

func main() {
	s := []int{2, 3, 5, 7, 11, 13}
	printSlice(s)

    // 截取切片使其长度为 0  len(s)=0,cap(s)=6
    s1 := s[:0]
	printSlice(s)

    // 重新拓展切片(注意：拓展的是长度而不是容量) len(s)=6,cap(s)=6
    s2 := s1[:6]
	printSlice(s)

    // len(s)=4,cap(s)=4
    s3 := s2[2:]
	printSlice(s)
    
    //所有切片共享同一个底层数组。如果一个切片修改了该底层数组的共享部分，其他所有切片都能感知到
    s3[0] = 10001
	printSlice(s)
	printSlice(s3)
}

func printSlice(s []int) {
	fmt.Printf("len=%d cap=%d %v\n", len(s), cap(s), s)
}
```

**从结果我们可以推测，子切片的容量为底层数组的长度减去切片在底层数组的开始偏移量！**

nil切片。切片的零值是 `nil`。nil 切片的长度和容量为 0 且没有底层数组。

```go
package main

import "fmt"

func main() {
	var s []int
	fmt.Println(s, len(s), cap(s))
	if s == nil {
		fmt.Println("nil!")
	}
}
```

#### 向切片中追加元素（使用函数append）(重要)

为切片追加新的元素是种常用的操作，为此 Go 提供了内建的 `append` 函数：

```go
package main

import "fmt"

func main() {
	var s []int
	printSlice(s)

	// 添加一个空切片
	s = append(s, 0)
	printSlice(s)

	// 这个切片会按需增长
	s = append(s, 1)
	printSlice(s)

	// 可以一次性添加多个元素
	s = append(s, 2)
	printSlice(s)
	
	s = append(s, 3)
	printSlice(s)
	
	s = append(s, 4)
	printSlice(s)
}

func printSlice(s []int) {
	fmt.Printf("len=%d cap=%d %v\n", len(s), cap(s), s)
}

```

`append` 的第一个参数 `s` 是一个元素类型为 `T` 的切片，其余类型为 `T` 的值将会追加到该切片的末尾。

`append` 的结果是一个包含原切片所有元素加上新添加元素的切片。

当 `s` 的底层数组太小，不足以容纳所有给定的值时，它就会分配一个更大的数组。返回的切片会指向这个新分配的数组。

### 注意事项

**append返回的新切片与原切片是否指向同一块内存地址？**
**根据切片的性质，当使用append函数给切片添加元素后，切片的length未超过append前的cap，则切片不会扩容，因此append返回的切片与原切片指向同一个内存地址。反之，append返回扩容后的切片，与原切片的内存地址不同。**

#### range函数

`for` 循环的 `range` 形式可遍历切片或映射。

当使用 `for` 循环遍历切片时，每次迭代都会返回两个值。第一个值为当前元素的下标，第二个值为该下标所对应元素的一份副本。

```go
package main

import "fmt"

var pow = []int{1, 2, 4, 8, 16, 32, 64, 128}

func main() {
	for i, v := range pow {
		fmt.Printf("2**%d = %d\n", i, v)
	}
}
```

可以将下标或值赋予 `_` 来忽略它。

```go
for i, _ := range pow
for _, value := range pow
```

若你只需要索引，忽略第二个变量即可。

```go
for i := range pow
```

### 映射（map）

映射是一个键值对。映射的零值为 `nil` 。`nil` 映射既没有键，也不能添加键。`make` 函数会返回给定类型的映射，并将其初始化备用。

```go
package main

import "fmt"

type Vertex struct {
	Lat, Long float64
}

var m map[string]Vertex

var m1 = map[string]Vertex{
	"Bell Labs": Vertex{
		40.68433, -74.39967,
	},
	"Google": Vertex{
		37.42202, -122.08408,
	},
}

func main() {
	m = make(map[string]Vertex)
	m["Bell Labs"] = Vertex{
		40.68433, -74.39967,
	}
	fmt.Println(m["Bell Labs"])
    fmt.Println(m1)
}
```

**映射的修改与判断健是否存在(类似java中的`contains()`)**

在映射 `m` 中插入或修改元素：

```go
m[key] = elem
```

获取元素：

```go
elem = m[key]
```

删除元素：

```go
delete(m, key)
```

通过双赋值检测某个键是否存在：

```go
elem, ok = m[key]
```

若 `key` 在 `m` 中，`ok` 为 `true` ；否则，`ok` 为 `false`。

若 `key` 不在映射中，那么 `elem` 是该映射元素类型的零值。

同样的，当从映射中读取某个不存在的键时，结果是映射的元素类型的零值。

**注** ：若 `elem` 或 `ok` 还未声明，你可以使用短变量声明：

```go
elem, ok := m[key]
```

```go
package main

import "fmt"

func main() {
	m := make(map[string]int)

	m["Answer"] = 42
	fmt.Println("The value:", m["Answer"])

	m["Answer"] = 48
	fmt.Println("The value:", m["Answer"])

	delete(m, "Answer")
	fmt.Println("The value:", m["Answer"])

	v, ok := m["Answer"]
	fmt.Println("The value:", v, "Present?", ok)
}

```

### 闭包函数(closure)

go中的函数是一等公民，同样可以作为值或参数返回：

```go
package main

import (
	"fmt"
	"math"
)

func compute(fn func(float64, float64) float64) float64 {
	return fn(3, 4)
}

func main() {
	hypot := func(x, y float64) float64 {
		return math.Sqrt(x*x + y*y)
	}
	fmt.Println(hypot(5, 12))

	fmt.Println(compute(hypot))
	fmt.Println(compute(math.Pow))
}
```

Go提供一种称之为匿名函数的闭包，该函数可以引用生声明在函数体外的变量（相当于外部变量）

```go
// Golang program to illustrate how
// to create a Closure
package main
  
import "fmt"
  
func main() {
      
    // Declaring the variable
    GFG := 0
  
    // Assigning an anonymous  
    // function to a variable 
    counter := func() int {
       GFG += 1
       return GFG
    }
  
    fmt.Println(counter())
    fmt.Println(counter())
      
    
}
```

上述GFG变量没有输入匿名函数func() int，但该函数可以访问这一变量。这一定义有一个小问题，就是main()函数的其他语句同样可以更改GFG变量，因此GO中的闭包函数还可以通过数据隔离来避免这种问题：

```go

// Golang program to illustrate how
// to create data isolation
package main
  
import "fmt"
  
// newCounter function to 
// isolate global variable
func newCounter() func() int {
    GFG := 0
    return func() int {
      GFG += 1
      return GFG
  }
}
func main() {
    // newCounter function is
    // assigned to a variable
    counter := newCounter()
  
    // invoke counter
    fmt.Println(counter())
    // invoke counter
    fmt.Println(counter())
      
    
}
```

这样，程序就只能够通过闭包函数newCounter()来访问GFG变量，从而起到数据隔离的作用。

## 方法和接口

### 方法

Go 没有类，不过可以为结构体类型定义方法。方法就是一类带特殊的 **接收者** 参数的函数。方法接收者在它自己的参数列表内，位于 `func` 关键字和方法名之间。

```go
package main

import (
	"fmt"
	"math"
)

type Vertex struct {
	X, Y float64
}

func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func main() {
	v := Vertex{3, 4}
	fmt.Println(v.Abs())
}

```

**方法即函数**

```go
package main

import (
	"fmt"
	"math"
)

type Vertex struct {
	X, Y float64
}

func Abs(v Vertex) float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func main() {
	v := Vertex{3, 4}
	fmt.Println(Abs(v))
}
```

同样可以为非结构体类型声明方法（但不能够为内建类型声明方法）

```go
package main

import (
	"fmt"
	"math"
)

type MyFloat float64

func (f MyFloat) Abs() float64 {
	if f < 0 {
		return float64(-f)
	}
	return float64(f)
}

func main() {
	f := MyFloat(-math.Sqrt2)
	fmt.Println(f.Abs())
}

```

与值接收者相对应，还存在指针接收者的情况：

```go
package main

import (
	"fmt"
	"math"
)

type Vertex struct {
	X, Y float64
}

func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func (v *Vertex) Scale(f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}

func main() {
	v := Vertex{3, 4}
	v.Scale(10)
	fmt.Println(v.Abs())
}
```

在上述代码的23行执行的逻辑其实是：**&v -> 给16行的 v *Vertex，**即可以访问main函数中的变量v的地址，并进行操作。由于16行的func没有任何返回值，因此如果16行的接收者是(v Vertex)而不是(v *Vertex)的话，则其实是对main函数中v的副本进行一系列操作，本质上v的各个属性依然是没有变化的。

如果把指针接收者重写成函数的话，即如下所示：

```go
package main

import (
	"fmt"
	"math"
)

type Vertex struct {
	X, Y float64
}

func Abs(v Vertex) float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func Scale(v *Vertex, f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}

func main() {
	v := Vertex{3, 4}
	Scale(&v, 10)
	fmt.Println(Abs(v))
}
```

然而对于值接收者或者是指针接收者方法而言，golang提供智能的识别功能，无论是用指针调用还是用结构体类型调用，都可以完成正常的操作：

```go
package main

import "fmt"

type Vertex struct {
	X, Y float64
}

func (v *Vertex) Scale(f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}

func ScaleFunc(v *Vertex, f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}

func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func AbsFunc(v Vertex) float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func main() {
	v := Vertex{3, 4}
	v.Scale(2)
	ScaleFunc(&v, 10)

	p := &Vertex{4, 3}
	p.Scale(3)
	ScaleFunc(p, 8)

	fmt.Println(v, p)
    
    s := Vertex{3, 4}
	fmt.Println(s.Abs())
	fmt.Println(AbsFunc(s))

	x := &Vertex{4, 3}
	fmt.Println(x.Abs())
	fmt.Println(AbsFunc(*x))
}
```

使用接收者方法的好处有很多，首先，方法能够修改其接收者指向的值。其次，这样可以避免在每次调用方法时复制该值。若值的类型为大型结构体时，这样做会更加高效。但值或指针接收者二者并不应该混用，这在接口的一系列知识中得以体现。

### 接口

**接口类型** 是由一组方法签名定义的集合，接口类型的变量可以保存任何实现了这些方法的值。以下有关于接口的代码体现了值接收者方法和指针接收者方法不能够混用的具体体现：

```go
package main

import (
	"fmt"
	"math"
)

type Abser interface {
	Abs() float64
}

func main() {
	var a Abser
	f := MyFloat(-math.Sqrt2)
	v := Vertex{3, 4}

	a = &v // a *Vertex 实现了 Abser
	a = f  // a MyFloat 实现了 Abser
	

	// 下面一行，v 是一个 Vertex（而不是 *Vertex）
	// 所以没有实现 Abser。
    // 24行有错误！！！！！
	a := v

	fmt.Println(a.Abs())
}

type MyFloat float64

func (f MyFloat) Abs() float64 {
	if f < 0 {
		return float64(-f)
	}
	return float64(f)
}

type Vertex struct {
	X, Y float64
}

func (v *Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
```

在上述代码中，接口的实现是较为严格的，即值接收者就必须用值进行调用，指针接收者就必须用指针进行调用，不能够进行混用！

#### 接口也是值

接口也是值。它们可以像其它值一样传递。

接口值可以用作函数的参数或返回值。

在内部，接口值可以看做包含值和具体类型的元组：

```
(value, type)
```

接口值保存了一个具体底层类型的具体值。

接口值调用方法时会执行其底层类型的同名方法，具体请运行如下代码：

```go
package main

import (
	"fmt"
	"math"
)

type I interface {
	M()
}

type T struct {
	S string
}

func (t *T) M() {
	fmt.Println(t.S)
}

type F float64

func (f F) M() {
	fmt.Println(f)
}

func main() {
	var i I

	i = &T{"Hello"}
	describe(i)
	i.M()

	i = F(math.Pi)
	describe(i)
	i.M()
}

func describe(i I) {
	fmt.Printf("(%v, %T)\n", i, i)
}
```

#### 底层值为 nil 的接口值

只要曾经给接口赋予了底层类型，尽管这个类型没有赋值，这个对应的接口变量也不为<nil>，也依然能够调用对应底层类型的方法;

```go
package main

import "fmt"

type I interface {
	M()
}

type T struct {
	S string
}

func (t *T) M() {
	if t == nil {
		fmt.Println("<nil>")
		return
	}
	fmt.Println(t.S)
}

func main() {
	var i I

	var t *T
	i = t
	describe(i)
	i.M()

	i = &T{"hello"}
	describe(i)
	i.M()
}

func describe(i I) {
	fmt.Printf("(%v, %T)\n", i, i)
}
```

只有未曾赋予底层类型的接口变量，其才表现为真正的<nil>接口值，并且不能够调用任何方法。

#### 空接口

空接口定义为：

```go
interface{}
```

其可以保存任何类型的值（因为每个类型都至少实现了零个方法）空接口被用来处理未知类型的值。例如，`fmt.Print` 可接受类型为 `interface{}` 的任意数量的参数。

```go
package main

import "fmt"

func main() {
	var i interface{}
	describe(i)

	i = 42
	describe(i)

	i = "hello"
	describe(i)
}

func describe(i interface{}) {
	fmt.Printf("(%v, %T)\n", i, i)
}
```

## 并发
