[toc]

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



## 接口类型

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



## 内置接口(String和Error)

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
```



## Reader

```go
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
 * 自定义Reader
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

