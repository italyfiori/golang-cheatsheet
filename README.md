## HelloWorld

```go
package main
import "fmt"

func main() {
	fmt.Println("Hello, 世界")
}
```



## 包

```go
/*******************************************************************************
 * 每个Go程序都是由包构成的，程序从 main 包开始运行
 * 包名与导入路径的最后一个元素一致
 ******************************************************************************/
package main
import "fmt"

func main() {
	fmt.Println("Hello, 世界")
}

/*******************************************************************************
 * 批量导入
 ******************************************************************************/
package main
import (
	"fmt"
	"math"
)

func main() {
	fmt.Printf("Now you have %g problems.\n", math.Sqrt(7))
}

/*******************************************************************************
 * 导出, 大写字母开头元素可以被包外部访问
 ******************************************************************************/
package main
import (
	"fmt"
	"math"
)

func main() {
	fmt.Println(math.Pi) // math包的Pi变量是大写字符开头, 可以被访问
}
```



## 变量

```go
/*******************************************************************************
 * 变量
 * var语句可以出现在包或函数级别
 * 没有明确初始值的变量声明会被赋予它们的零值。
 ******************************************************************************/
// 变量声明
var i int
var a, b int 

// 自动推断变量类型
var i = 1
var a, b = 1, 2

// 短变量声明(不能在函数外使用)
k := 3 

// 常量
const World = "世界"
const Truth = true

// 分组声明
var (
	ToBe   bool       = false
	MaxInt uint64     = 1<<64 - 1
	z      complex128 = cmplx.Sqrt(-5 + 12i)
)

/*******************************************************************************
 * 基本数据类型
 ******************************************************************************/
bool
string
int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr
byte // uint8 的别名
rune // int32 的别名
float32 float64
complex64 complex128

/*******************************************************************************
 * 类型转换
 ******************************************************************************/
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)
```



## 指针、结构体和集合

```go
/*******************************************************************************
 * 指针
 ******************************************************************************/
i := 42
p := &i         // 指向 i
fmt.Println(*p) // 通过指针读取 i 的值
*p = 21         // 通过指针设置 i 的值

/*******************************************************************************
 * 结构体
 ******************************************************************************/
type Vertex struct { // 定义
	X int
	Y int
}

func main() {
	v := Vertex{1, 2} // 实例化
	p := &v
    v.X = 4 	// 对象访问
	p.X = 1e9 	// 指针简化访问，相当于(*p).X
	fmt.Println(v)
}

/*******************************************************************************
 * 数组
 ******************************************************************************/
// 声明然后赋值
var a [2]string
a[0] = "Hello"
a[1] = "World"

// 声明同时赋值
primes := [6]int{2, 3, 5, 7, 11, 13}

/*******************************************************************************
 * 切片
 ******************************************************************************/
// 创建切片
s1 := make([]int, 0, 5) // len(b)=0, cap(b)=5
s2 := []int{2, 3, 5, 7, 11, 13}

// 添加元素
s = append(s, 1)
s = append(s, 2, 3, 4)

// 索引
s = s[1:4]
s = s[:2]
s = s[1:]

// 长度和容量
fmt.Printf("len=%d cap=%d %v\n", len(s), cap(s), s)

// nil切片
var s []int

// 遍历
for i, v := range s {
    fmt.Printf("2**%d = %d\n", i, v)
}

/*******************************************************************************
 * Map
 ******************************************************************************/
package main

import "fmt"

func main() {
    // 创建Map
    // var countryCapitalMap map[string]string
    var countryCapitalMap = make(map[string]string)

    // 插入key
    countryCapitalMap [ "France" ] = "巴黎"
    countryCapitalMap [ "Italy" ] = "罗马"
    countryCapitalMap [ "Japan" ] = "东京"
    countryCapitalMap [ "India " ] = "新德里"

    // 删除元素
    delete(countryCapitalMap, "France")

    // 遍历Map
    for country := range countryCapitalMap {
        fmt.Println(country, "首都是", countryCapitalMap [country])
    }

    // 检查元素否存在
    capital, ok := countryCapitalMap [ "American" ]
    
    if (ok) {
        fmt.Println("American 的首都是", capital)
    } else {
        fmt.Println("American 的首都不存在")
    }
}
```



## 函数

```go
/*******************************************************************************
 * 函数定义
 ******************************************************************************/
// 标准
func add(x int, y int) int {
	return x + y
}

// 省略前面的变量类型
func add(x, y int) int { 
	return x + y
}

// 返回多值
func swap(x, y string) (string, string) {
	return y, x
}

// 返回值命名(相当于在函数开头声明了x和y变量作为返回值,return时可以省略x和y变量)
func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}

/*******************************************************************************
 * 函数值(函数作为变量)
 ******************************************************************************/
package main
import (
	"fmt"
	"math"
)

// 函数作为参数
func compute(fn func(float64, float64) float64) float64 {
	return fn(3, 4)
}

func main() {
    // 声明函数
	hypot := func(x, y float64) float64 {
		return math.Sqrt(x*x + y*y)
	}
	fmt.Println(hypot(5, 12))
}

/*******************************************************************************
 * 闭包
 ******************************************************************************/
package main

import "fmt"

// 该函数返回一个闭包, 闭包可以引用函数体之外的变量
func getSequence() func() int {
	i := 0
	return func() int {
		i += 1
		return i
	}
}

func main() {
	// 函数赋值给一个变量
	nextNumber := getSequence()

	// 使用该变量调用函数
	fmt.Println(nextNumber())
	fmt.Println(nextNumber())
}
```



## 方法

```go
/*******************************************************************************
 * 结构体方法，方法就是一类带特殊的接收者参数的函数。
 ******************************************************************************/
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

/*******************************************************************************
 * 非结构体方法， 接收者的类型定义和方法声明必须在同一包内
 ******************************************************************************/
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

/*******************************************************************************
 * 指针接收者的方法
 * 1. 由于Scale方法有一个指针接收者，为方便起见，Go 会将语句 v.Scale(5) 解释为 (&v).Scale(5)
 * 2. 使用指针接受者可以避免在每次调用方法时复制该值
 ******************************************************************************/
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



## 流程控制

```go
/*******************************************************************************
 * for循环-标准
 ******************************************************************************/
func main() {
	sum := 0
	for i := 0; i < 10; i++ {
		sum += i
	}
	fmt.Println(sum)
}

/*******************************************************************************
 * for循环-等于while
 ******************************************************************************/
func main() {
	sum := 1
	for sum < 1000 {
		sum += sum
	}
	fmt.Println(sum)
}

/*******************************************************************************
 * for循环-无限循环
 ******************************************************************************/
func main() {
	for {
	}
}

/*******************************************************************************
 * if
 ******************************************************************************/
// 标准
if ture {
    fmt.Println("hello world")
}

// 可以在表达式前执行一个简单的语句
if a := 1; a < 2 {
    fmt.Println(a)
}

// if else
if 2 > 1 {
    fmt.Println("true")
} else {
    fmt.Println("false")
}

/*******************************************************************************
 * switch
 ******************************************************************************/
// switch标准
switch os := runtime.GOOS; os {
case "darwin":
    fmt.Println("OS X.")
case "linux":
    fmt.Println("Linux.")
default:
    fmt.Printf("%s.\n", os)
}

// 没有条件的switch，相当于一串if else
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

/*******************************************************************************
 * defer
 * defer后的函数推迟到当前函数返回后执行
 * 被推迟的函数会按照后进先出的顺序调用(栈)
 ******************************************************************************/
func main() {
	fmt.Println("counting")
	for i := 0; i < 10; i++ {
		defer fmt.Println(i)
	}
	fmt.Println("done")
}
```



## 接口

```go

/*******************************************************************************
 * 接口类型 是由一组方法签名定义的集合。
 * 类型通过实现一个接口的所有方法来实现该接口，无需显式声明，也没有“implements”关键字。
 ******************************************************************************/
type Abser interface {
	Abs() float64
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

func main() {
	var a Abser
	f := MyFloat(-math.Sqrt2)
	v := Vertex{3, 4}

	a = f  // a MyFloat 实现了 Abser
	a = &v // a *Vertex 实现了 Abser

	fmt.Println(a.Abs())
	fmt.Println(f.Abs())
	fmt.Println(v.Abs())
}

/*******************************************************************************
 * 接口实现多态
 ******************************************************************************/
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
	i.M()

	i = F(math.Pi)
	i.M()
}

/*******************************************************************************
 * nil对象值可以调用方法
 ******************************************************************************/
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
	var t1 *T // 没有初始化
	t1.M()

	var t2 = &T{"hello"}
	t2.M()
}

/*******************************************************************************
 * nil接口值不能调用方法
 ******************************************************************************/
type I interface {
	M()
}

func main() {
	var i I
	describe(i)
}

func describe(i I) {
	fmt.Printf("(%v, %T)\n", i, i)
}
```



## 类型选择

```go
/*******************************************************************************
 * 空接口，可以保存任何类型的值
 ******************************************************************************/
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

/*******************************************************************************
 * 类型断言 提供了访问接口值底层具体值的方式。
 ******************************************************************************/
func main() {
	var i interface{} = "hello"

	s := i.(string)
	fmt.Println(s)

	s, ok := i.(string)
	fmt.Println(s, ok)

	f, ok := i.(float64)
	fmt.Println(f, ok)

	f = i.(float64) // 报错(panic)
	fmt.Println(f)
}

/*******************************************************************************
 * 类型选择 是一种按顺序从几个类型断言中选择分支的结构。
 ******************************************************************************/
func do(i interface{}) {
	switch v := i.(type) {
	case int:
		fmt.Printf("Twice %v is %v\n", v, v*2) // 变量v会按int类型保存
	case string:
		fmt.Printf("%q is %v bytes long\n", v, len(v)) // 变量v会按string类型保存
	default:
		fmt.Printf("I don't know about type %T!\n", v) // 变量v与i的接口类型和值相同。
	}
}

func main() {
	do(21)
	do("hello")
	do(true)
}
```



## 内置接口

```go
/*******************************************************************************
 * Stringer接口的String函数，用字符串描述对象
 ******************************************************************************/
type Person struct {
	Name string
	Age  int
}

func (p Person) String() string {
	return fmt.Sprintf("%v (%v years)", p.Name, p.Age)
}

func main() {
	a := Person{"Arthur Dent", 42}
	z := Person{"Zaphod Beeblebrox", 9001}
	fmt.Println(a, z)
}

/*******************************************************************************
 * error
 * error类型是一个内建接口, 通常函数会返回一个error值
 ******************************************************************************/
type error interface {
    Error() string
}

/*******************************************************************************
 * 返回error类型的系统函数
 ******************************************************************************/
i, err := strconv.Atoi("42")
if err != nil {
    fmt.Printf("couldn't convert number: %v\n", err)
}
fmt.Println("Converted integer:", i)

/*******************************************************************************
 * 自定义error类型
 ******************************************************************************/
type MyError struct {
	When time.Time
	What string
}

// 结构体类型实现了Error函数
func (e *MyError) Error() string {
	return fmt.Sprintf("at %v, %s",
		e.When, e.What)
}

func run() error {
	return &MyError{
		time.Now(),
		"it didn't work",
	}
}

func main() {
	if err := run(); err != nil {
		fmt.Println(err)
	}
}

/*******************************************************************************
 * io.Reader接口，它表示从数据流的末尾进行读取。
 ******************************************************************************/
func main() {
	r := strings.NewReader("Hello, Reader!")

	b := make([]byte, 8)
	for {
		n, err := r.Read(b)
		fmt.Printf("n = %v err = %v b = %v\n", n, err, b)
		fmt.Printf("b[:n] = %q\n", b[:n])
		if err == io.EOF {
			break
		}
	}
}

/*******************************************************************************
 * 实现Reader接口
 ******************************************************************************/
type MyReader struct{}

func (reader MyReader) Read(b []byte) (int, error) {
	for i := range b {
		b[i] = 'A'
	}
	return len(b), nil
}

func main() {
    b := make([]byte, 8)
    MyReader{}.Read(b)
    fmt.Println(b)
}
```



## 并发

```go
/*******************************************************************************
 * goroutine是go管理的轻量级线程
 ******************************************************************************/
import (
	"fmt"
	"time"
)

func say(s string) {
	for i := 0; i < 5; i++ {
		time.Sleep(100 * time.Millisecond)
		fmt.Println(s)
	}
}

func main() {
	go say("world")
	say("hello") 
}

/*******************************************************************************
 * 信道是带有类型的管道
 * ch := make(chan int) // 创建信道 
 * ch <- v    // 将 v 发送至信道 ch。
 * v := <-ch  // 从 ch 接收值并赋予 v。
 * 发送和接收操作在另一端准备好之前都会阻塞，这使得 Go 程可以在没有显式的锁或竞态变量的情况下进行同步
 ******************************************************************************/
func sum(s []int, c chan int) {
	sum := 0
	for _, v := range s {
		sum += v
	}
	fmt.Println("inner")
	c <- sum // 把 sum 发送到通道 c
}

func main() {
	s := []int{7, 2, 8, -9, 4, 0}

	c := make(chan int)
	go sum(s[:len(s)/2], c)
	go sum(s[len(s)/2:], c)
	x, y := <-c, <-c // 从通道 c 中接收

	fmt.Println(x, y, x+y)
}

/*******************************************************************************
 * 带缓冲的信道, 声明时指定缓冲区大小
 ******************************************************************************/
func main() {
	ch := make(chan int, 2)
	ch <- 1
	ch <- 2
	fmt.Println(<-ch)
	fmt.Println(<-ch)
}

/*******************************************************************************
 * 关闭和遍历信道
 * v, ok := <-ch // 测试信道是否关闭
 * close(ch) // 关闭信道。 通常情况下无需关闭它们，只有在必须告诉接收者不再有需要发送的值时才有必要关闭
 * for i := range c // 不断从信道接收值，直到信道被关闭
 ******************************************************************************/
func fibonacci(n int, c chan int) {
	x, y := 0, 1
	for i := 0; i < n; i++ {
		c <- x
		x, y = y, x+y
	}
	close(c)
}

func main() {
	c := make(chan int, 10)
	go fibonacci(cap(c), c)
	for i := range c {
		fmt.Println(i)
	}
}

/*******************************************************************************
 * select 会阻塞到某个分支可以继续执行为止，这时就会执行该分支。
 * 当多个分支都准备好时会随机选择一个执行。
 ******************************************************************************/
func fibonacci(c, quit chan int) {
	x, y := 0, 1
	for {
		select {
		case c <- x:
			x, y = y, x+y
		case <-quit:
			fmt.Println("quit")
			return
		}
	}
}

func main() {
	c := make(chan int)
	quit := make(chan int)
	go func() {
		for i := 0; i < 10; i++ {
			fmt.Println(<-c)
		}
		quit <- 0
	}()
	fibonacci(c, quit)
}

/*******************************************************************************
 * select默认分支
 ******************************************************************************/
func main() {
	tick := time.Tick(100 * time.Millisecond)
	boom := time.After(500 * time.Millisecond)
	for {
		select {
		case <-tick:
			fmt.Println("tick.")
		case <-boom:
			fmt.Println("BOOM!")
			return
		default:
			fmt.Println("    .")
			time.Sleep(50 * time.Millisecond)
		}
	}
}

/*******************************************************************************
 * sync.Mutex互斥锁类型
 ******************************************************************************/
type SafeCounter struct {
	v   map[string]int
	mux sync.Mutex
}

func (c *SafeCounter) Inc(key string) {
	c.mux.Lock() // Lock 之后同一时刻只有一个 goroutine 能访问 c.v
	c.v[key]++
	c.mux.Unlock()
}

func (c *SafeCounter) Value(key string) int {
	c.mux.Lock() // Lock 之后同一时刻只有一个 goroutine 能访问 c.v
	defer c.mux.Unlock()
	return c.v[key]
}

func main() {
	c := SafeCounter{v: make(map[string]int)}
	for i := 0; i < 1000; i++ {
		go c.Inc("somekey")
	}
	
	time.Sleep(time.Second)
	fmt.Println(c.Value("somekey"))
}

```
