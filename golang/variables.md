## 变量与常量

### 数据类型

在学习变量之前我们先来了解一下golang有哪些数据类型

```go

// 有符号整型
int, int8, int16, int32(rune), int64

// int:
// 在32位机器上运行他就是int32，如果是在64位机上运行那他就是int64

// 无符号整型
uint, uint8(byte), uint16, uint32, uint64

// uint与int性质一样

// 复数（包含实数和虚数部分）
complex64, complex128

// 字符串
string

// 数组与slice
[N]Type, []Type

// map
map[Type]Type

// func
func(...Type) ...Type

```

### 声明变量的方式

golang可以使用var或const关键字声明，或者使用:=进行短声明，他是声明+赋值的一个过程，并且短声明不能指定类型（由系统来判定他是什么类型），所以对变量第一次声明时用了:=，那么之后就需要使用=，不能用:=再进行值的修改，但也有例外情况（后面会讲到）

在使用const和var这两种方式声明的时候，可以在包内或函数体内进行声明，而短声明(:=)只允许在函数体内，另外，声明的时候可以省略类型，系统会帮我们判断是什么类型（并不代表他是任意类型）

注意：const在声明的时候就需要赋值，且在拼接的时候他也只允许是两个const声明的常量进行拼接

```go

var a int = 100

const b = "foo" // 系统会判断他是string类型

var d = []int{1, 2, 3}

var n, m int = 100, 200

var j, k = 100, "str"

c := 100 + 5i // 复数

q, p := 999, 666
```

以下情况会报错

```go

const a = 100

let b = 200

const c = a + b // !! 报错，const不允许与变量组合

```
```go

const n int // !! 报错，常量在声明的时候就要赋值

```
```go

const n = 100

n = 200 // !! 报错

```
```go

k := "str"

k := "hello" // !! 报错，:=是声明+赋值的概念，所以第二次使用相当于又声明了一次

```
```go

q := "str"

q = 100 // !! 报错，系统已判定他是string类型，不能再赋其他类型的值

```
```go
// 等等...
```

前面我们说到:=声明+赋值只允许一次，重复这么干会报错，但也有例外情况

这种情况并不会报错，因为c是一个新的变量，所以允许a与c一起声明的时候使用:=，但如果c在之前已经存在了，那还是会报错的

```go

a, b := 100, 200

a, c := 200, 300

```

go语言还允许var和const声明一组的变量/常量，:=声明的方式不允许组的方式

```go
// var

var (
  a = 1
  b = "str"
  c = false
)

// const

const (
  a = 1
  b = "str"
  c = false
)
```

### const声明的特殊情况

有些数据类型是不可以使用const声明的，指针类型的就不行，还有map、array、slice等

const在按组声明的方式还有另一个特点，就是如果这个常量没有赋值，他将继承前一个值，并且他可以与iota形成一个奇妙的组合，在后面会讲到

```go

const (
  a = 1
  b
  c
)

/*
a -> 1
b -> 1
c -> 1
*/

const (
  x = 999
  y = 1
  z
)

/*
x -> 999
y -> 1
z -> 1
*/
```

### 自定义数据类型

除了系统内建的数据类型外，我们还可以自定义数据类型，使用type关键字

```go

type myInt int

var a myInt = 1

```
```go

type myFunc func(int) int

var a myFunc = func(i int) int {
  return i
}

```

自定义数据类型还有个好处就是能给这个数据类型定义方法，在function这节我们会讲

### 访问权限

golang并没有public或是private这些关键字，也没有常量一定要是大写的说法，一般我们常量还是小写的

```go
var Str = "hello" // public

var foo = "world" // private
```

**他们的区别在于首字母是否为大写**

这种访问权限的控制方式，除了const/var之外，func、type也是如此

### 变量的类型转换

golang只有强制转换，没有隐式转换，一般来说，我们使用Type(n)的方式来强制转换，但也有一些类型是没法强制转的，需要借助标准库或第三方库

```go

var a float64 = 123.4

var b int = int(a) // -> 123

var c string = string(b) // !!! 注意：c转出来之后是"{"，系统会把他当作字节码

// 所以int转string的正确方式是：
var d = strconv.Itoa(b) // strconv是一个标准库

```

### interface{}类型

interface{}类型可以认为是任意类型，他与interface接口是两个不同的东西

```go

var a interface{} = 123
  
a = "str"

a = false

```

如果不是interface{}类型，或者没有显式的告诉系统他是该类型，这样重复赋值是会编译错误的，但是interface{}在使用时也是需要转换类型的，就像前面所说的，golang没有隐式转换，如下代码

```go

var x, y interface{} = 1.5, 3.3

math.Max(x, y) // !!! 编译错误, x, y需要float64

```
```go

func foo(val string)  {
  // do something...
}

var str interface{} = "i am a string"

foo(str) // !!! 编译错误

```

可能大家会想到用强制转换的方式

```go

var str interface{} = "i am a string"

foo(string(str)) // !!! 同样的，编译错误

```

正确的interface{}类型用法，使用类型断言，x.(Type)

```go
var str interface{} = "i am a string"

foo(str.(string)) // ok
```

需要注意的是，类型断言意味着该变量确实是该类型的，否则会报运行时错误

```go
func foo(val int)  {
  // do something...
}

var val interface{} = 3.14

foo(val.(int)) // !!! 运行时错误，系统会告诉你val其实是float64

// 如果真要让他正常运行，需要这样做

foo(int(val.(float64))) // ok

```

### 零值

所有变量的声明都会有默认值，这个值我们称为零值，常量例外，因为常量在声明的时候是必须赋值的

```go

var a int

fmt.Println(a) // -> 0

```
```go

var b float32

fmt.Println(b) // -> 0  float类型的零值也是0，并不是0.0之类的

```
```go

var c string

fmt.Println(c) // -> 注意，系统不会打印任何内容，其实他是空字符串，下面这样打印会比较明显
fmt.Printf("%q", c) // -> ""

```
```go

var d bool

fmt.Println(d) // -> false

```
```go

var e complex64

fmt.Println(e) // -> (0+0i)

```
```go

var f interface{}

fmt.Println(f) // -> nil

```

其他的还有例如方法和指针，默认值是nil；array和map，默认值是他的value类型对应的零值


### new函数

变量声明是可以使用系统内建的new函数声明的，不同的是，该函数返回的是一个指针

```go
var a = new(int)

*a = 123 // 用*取指针指向的内容，然后对他赋值

fmt.Println(*a) // -> 123
```
但一般我们不会用于new一个int或是float这类基础类型，比如定义工厂方法，去创建一个struct，是比较常规的(后面会讲到struct，func这些)

```go
type person struct {
  name string
  age int
}

func createPerson(name string, age int) *person {
  var p = new(person)

  p.name = name
  p.age = age

  return p
}

p := createPerson("eason", 18) // better
```

### iota

iota其实是个值，而且他只能用于const，他常被用于枚举，因为golang其实没有enum类型，注意他的特性

- 他的值默认是0，但在不断的赋给常量后会自动增加
- 当他遇到下一个const时，他会自动归零

```go
const a = iota // -> 0
const b = iota // -> 0
```

**他通常用于一组const的声明，这样才会发挥他的功效**

```go
const (
  a = iota // -> 0
  b = iota // -> 1
  c = iota // -> 2
)

const ( // 注意：这里遇到第二个const了，iota会归零
  x = iota // -> 0
  y = iota // -> 1
  z = iota // -> 2
)
```

当然我们使用的时候应该结合const组声明时继承的特性

```go
const (
  a = iota // -> 0
  b // -> 1
  c // -> 2
)
```

他也可以参与运算

```go
const (
  a = iota * 2 + 1 // -> 1
  b // -> 3
  c // -> 5
)
```

### 堆/栈

关于变量存在于堆内还是栈内其实我们不需要关心，golang有自动的垃圾回收机制

在golang里，其实func是一等公民，是可以作为参数传递的，他也有闭包的概念，比如这样

```go

func foo() func() *int {
  v := 1
  return func() *int {
    return &v
  }
}

f := foo()

res := f()

fmt.Println(*res) // -> 1

```

这个例子是这样的，foo方法返回一个func，这个被返回的func没有参数并返回一个int的指针，所以我们执行了foo之后拿到的其实是一个func，然后我们调用这个func会拿到在foo方法内但不在返回的这个func内的一个int(因为用了指针，所以就是这个int，并不是一个新的值相同的int)

通常情况下，变量的声明是存在于栈内的，函数执行完成出栈后该函数包括函数内的变量都会被销毁，但这个闭包例子就是个例外，如果v存在于栈内，随着foo方法的执行完成，v会随之被销毁，但实际上并没有，因为我们在调用f()的时候还是可以拿到他，所以这个时候这个int就会存在于堆内，系统会在不再使用他之后进行销毁
