## 方法

golang中定义方法的关键字为**func**，并且func可以作为目标func的参数或返回值

```go
func foo() {
  // do...
}
```

在main函数中调用

golang中没有this关键字，包内的方法直接使用方法名调用即可

```go
package main

import "fmt"

func main() {
  foo() // -> hello
}

func foo() {
  fmt.Println("hello")
}
```

不同包的调用

简单来说就是 {包名}.{方法名}

```go
package main

import "{项目名}/bar"

func main() {
  bar.Foo() // print -> hello

  bar.do() // error，调用不到
}

// ----------- 另一外包

package bar

import "fmt"

func Foo() {
  fmt.Println("hello")
}

func do() {
  // do...
}
```

不同包的更简洁的调用（虽然简洁，但不建议使用），就是在import和路径之间加一个点

```go
package main

import . "{项目名}/bar"

func main() {
  Foo() // print -> hello
}
```

方法的传参

golang没有参数默认值的概念，所以一个方法定义有几个参数，那在调用的时候就必须传递相符合的参数，不能多，也不能少

```go
func foo(a string, b string) {
  fmt.Println(a, b)
}

foo("hello", "world") // print -> hello world
```

不定个数的传参，跟js中的rest参数类似，不同的是三个点是在后面

这种传参方式在有多个参数时，只能在所有参数的最后且只用一次

```go
func do(a... string) {
  // 这里的a实际上是[]string
}

do("a", "b", "c")

// 这里还有类似解构的用法

b := []string{"X", "Y", "Z"}

do(b...)
```

方法的返回值

```go
func foo() string {
  return "hello"
}

a := foo()

fmt.Print(a) // -> hello
```

返回值也可以定义名称

有名称或是多返回值的情况下，都是需要用括号括起来的，这时可以使用两种方式返回，建议使用第一种

A. 传统的返回
```go
func foo() (str string) {
  return "hello"
}
```

B. 对返回值的变量赋值
```go
func foo() (str string) {
  str = "hello"
  return
}
```

多返回值

```go
func foo() (str string, b bool) {
  return "hello", true
}

a, b := foo()

fmt.Println(a, b) // -> hello true
```

因为有零值的存在，golang中经常会使用到多返回值，所以对这个特性我们要非常熟悉

比如一个函数返回一个字符串，但字符串不一定存在，这时就需要2个返回值，第一个返回字符串的值，第二个返回是否存在的bool值，因为如果只有一个值，那我们将不能区分该值到底是不存在还是他的值就是空字符串(零值)

又比如调用数据库，只有一个值的情况下我们同样没法区别取到的数据就是条空内容还是取数据时出错了

所以以下两种形式的方法调用在日常开发中经常会看到

```go
data, err := getInfoFromDB()

if err != nil {
  // 出错处理
  return
}

// 成功获取到数据
fmt.Println(data)
```

```go

val, exist := getValue()

if !exist {
  // 值不存在
  return
}

// 对值的处理
do(val)
```

### struct的方法

前面讲的struct也是可以使用方法的，以上所有方法的使用方式都同样适用于struct

```go

type Stru struct {
  val string
}

func (s Stru) foo() {
  fmt.Println(s.val)
}

s := Stru{"hello"}

s.foo() // print -> hello



```

