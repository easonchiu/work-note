
## 变量

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

```

### 声明变量的方式

golang可以使用var或const关键字声明，或者使用:=进行短声明，他是声明+赋值的一个过程，所以对变量第一次声明时用了:=，那么之后就需要使用=，不能用:=再进行值的修改，但也有例外情况（后面会讲到）

在使用const和var这两种方式声明的时候，可以在包内或函数体内进行声明，而短声明只允许在函数体内

注意：const在声明的时候就需要赋值，且在拼接的时候他也只允许是两个const声明的常量进行拼接

```go

var a int = 100

var b = "string"

const d = []int{1, 2, 3}

var n, m int = 100, 200

var j, k = 100, "str"

c := 100 + 5i // 复数

q, p := 999, 666
```

以下情况会报错

```go

const a = 100

let b = 200

const c = a + b // !! 报错

--------

const n int // !! 报错

--------

const n = 100

n = 200 // !! 报错

--------

k := "str"

k := "hello" // !! 报错

--------

q := "str"

q = 100 // !! 报错

--------

// 等等...
```

前面我们说到:=声明+赋值只允许一次，重复这么干会报错，但也有例外情况

这种情况并不会报错，因为c是一个新的变量，所以允许a与c一起声明的时候使用:=，但如果c在之前已经存在了，那还是会报错的

```go

a, b := 100, 200

a, c := 200, 300

```

### 访问权限

golang并没有public或是private这些关键字，也没有常量一定要是大写的说法，一般我们常量还是小写的

```go
var Str = "hello" // public

var foo = "world" // private
```

**他们的区别在于首字母是否为大写**

这种访问权限的控制方式，除了const/var之外，func、struct、interface也是如此

### 变量的类型转换

...

### interface{}类型

...

### 零值

...

### new函数

...

### iota

...

### 堆/栈

...
