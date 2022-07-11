# GOLANG

## switch

Go 自动提供了在这些语言中每个 case 后面所需的 `break` 语句。     除非以 `fallthrough` 语句结束，否则分支会自动终止。

没有条件的 switch 同 `switch true` 一样。  

## defer

defer 语句会将函数推迟到外层函数返回之后执行。  

## 

```
type Vertex struct {
	X int
	Y int
}

func main() {
	v := Vertex{1, 2}
	v.X = 4
	fmt.Println(v.X)
}
```

## 结构体

```
v := Vertex{1, 2}
	p := &v
	p.X = 1e9
```

## 数组

```
	//initialize the array 
	primes := [6]int{2, 3, 5, 7, 11, 13}
```

## var()

var(

```
./prog.go:14:4: syntax error: unexpected :=, expecting =
```

)

用等号

## 切片

```
// 构造一个数组 然后 构建一个引用了它的切片：
	[]bool{true, true, false}
```

```
func printSlice(s []int) {
	fmt.Printf("len=%d cap=%d %v\n", len(s), cap(s), s)
}
//type of s is "slice"
```

切片的容量是从它的**第一个元素**开始数，到其**底层数组元素末尾**的个数。  

切片的零值是 `nil`。  

​    `for` 循环的 `range` 形式可遍历切片或映射。  

​    当使用 `for` 循环遍历切片时，每次迭代都会返回两个值。第一个值为当前元素的下标，第二个值为该下标所对应元素的一份副本。  

可以将下标或值赋予 `_` 来忽略它, 如果只需要索引，可以直接忽略第二项

## 映射

映射的零值为 `nil` 。`nil` 映射既没有键，也不能添加键。  

```
var m = map[string]Vertex{
	"Bell Labs": Vertex{
		40.68433, -74.39967,
	},
	"Google": Vertex{
		37.42202, -122.08408,
	},
}
```

## 方法

方法只是个带接收者参数的函数。

可以为非结构体类型声明方法。  

就是接收者的类型定义和方法声明必须在同一包内；不能为内建类型声明方法。

```go
package main

import (
	"fmt"
	"math"
)

type MyFloat float64

func (f MyFloat) Abs() float64 {  //f是接受者 MyFloat是接受者类型
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

```go
func (v *Vertex) Scale(f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}

func main() {
	v := Vertex{3, 4}
	v.Scale(10)  //可以通用
}
```

## 接口

在内部，接口值可以看做包含值和具体类型的元组：  

`(value, type)`

​    接口值保存了一个具体底层类型的具体值。  

​    接口值调用方法时会执行其**底层类型**的同名方法。  



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
	describe(i)   //(<nil>, <nil>)
	
	var t *T
	i = t
	describe(i)   //(<nil>, *main.T)
	i.M()

	i = &T{"hello"}
	describe(i)
	i.M()
}

func describe(i I) {
	fmt.Printf("(%v, %T)\n", i, i)
}
```

```go
var i I
//对于i，值为nil，类型也为nil
//为nil接口调用方法会产生运行时错误，因为接口的元组内并未包含能够指明该调用哪个具体方法的类型。
```

> 即便接口内的具体值为 nil，方法仍然会被 nil 接收者调用。  
>
> // i有可能值为nil但类型不为nil。  

空接口被用来处理未知类型的值。例如，`fmt.Print` 可接受类型为 `interface{}` 的任意数量的参数。  

## 类型断言

t := i.(T) 类型断言 若接口值i保存的类型不是T，则引起panic

t, ok := i.(T) 

## 类型选择

```go
func do(i interface{}) {
	switch v := i.(type) {
	case int:  //T
		fmt.Printf("Twice %v is %v\n", v, v*2)
	case string:  //S
		fmt.Printf("%q is %v bytes long\n", v, len(v))
	default:
		fmt.Printf("I don't know about type %T!\n", v)
	}
}
```

此选择语句判断接口值 `i` 保存的值类型是 `T` 还是 `S`。在 `T` 或 `S` 的情况下，变量 `v` 会分别按 `T` 或 `S` 类型保存 `i` 拥有的值。在默认（即没有匹配）的情况下，变量 `v` 与 `i` 的接口类型和值相同。 

## Stringer 

​    [`fmt`](https://go-zh.org/pkg/fmt/) 包中定义的 [`Stringer`](https://go-zh.org/pkg/fmt/#Stringer) 是最普遍的接口之一。  

```
type Stringer interface {
    String() string
}
```

​    `Stringer` 是一个可以用字符串描述自己的类型。`fmt` 包（还有很多包）都通过此接口来打印值。  

## error

​    Go 程序使用 `error` 值来表示错误状态。  

​    与 `fmt.Stringer` 类似，`error` 类型是一个内建接口：  

```
type error interface {
    Error() string
}
```

​    （与 `fmt.Stringer` 类似，`fmt` 包在打印值时也会满足 `error`。）  

​    通常函数会返回一个 `error` 值，调用的它的代码应当判断这个错误是否等于 `nil` 来进行错误处理。  

   `error` 为 nil 时表示成功；非 nil 的 `error` 表示失败。  

## Reader

​    `io.Reader` 接口有一个 `Read` 方法：  

```
func (T) Read(b []byte) (n int, err error)
```

​    `Read` 用数据填充给定的字节切片并返回填充的字节数和错误值。在遇到数据流的结尾时，它会返回一个 `io.EOF` 错误。  

## Go程

Go 程（goroutine）是由 Go 运行时管理的轻量级线程。  

```
go f(x, y, z)
```

 `f`, `x`, `y` 和 `z` 的求值发生在当前的 Go 程中，而 `f` 的执行发生在新的 Go 程中。  

## 信道

信道是带有类型的管道，可以通过它用信道操作符 `<-` 来发送或者接收值。  

```
ch <- v    // 将 v 发送至信道 ch。
v := <-ch  // 从 ch 接收值并赋予 v。
```

信道在使用前必须创建：  

```
ch := make(chan int)
```

信道缓冲区：

```
ch := make(chan int, 100)
```

当信道的缓冲区填满后，向其发送数据时，发送端会阻塞。



若没有值可以接收且信道已被关闭，那么在执行完  

```
v, ok := <-ch
```

之后 `ok` 会被设置为 `false`。  

循环 `for i := range c` 会不断从信道接收值，直到它被关闭（`close ch`）。  

## select

```
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
```

 `select` 语句使一个 Go 程可以等待多个通信操作。  

 `select` 会**阻塞**到某个分支可以继续执行为止，这时就会执行该分支。当多个分支都准备好时会随机选择一个执行。  

当 `select` 中的其它分支都没有准备好时，`default` 分支就会执行。  

为了在尝试发送或者接收时不发生阻塞，可使用 `default` 分支：  

```
select {
case i := <-c:
    // 使用 i
default:
    // 从 c 中接收会阻塞时执行
}
```

信道非常适合在各个 Go 程间进行通信



