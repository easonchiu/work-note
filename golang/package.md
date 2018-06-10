## 包

包的关键字是package，一般我们go文件的第一行总是package+包名字开头的

```go
package myPackage
```

### 主包

一般我们在开发项目的时候都会有一个入口文件，这个文件的package名字需要叫main，并有一个入口方法，他也叫main

```go
package main

func main() {
  // entry...
}
```

### 包的导入

包的导入我们一般使用import关键字，写在package之后

```go
package main

import "fmt" // fmt包就包含了之前我们例子中经常使用到的fmt.Println打印方法
```

多个包导入的话我们可以像const/var这样的组声明的方式
```go
package main

import (
  "fmt"
  "somePackage1"
  "somePackage2"
)
```

包也可以导入了不用任何方法，因为如果导入一个包，就必须使用他，如果不使用将会编译错误，但有些情况是我们需要导入一个包，但不使用任何方法的

```go
package main

import (
  _ "fmt" // 我们导入了fmt包，但不使用他，但有其他用处，我们后面讲到
  "somePackage1"
  "somePackage2"
)
```

### 一个目录下只允许有一个包

比如我们在a目录中有两个包，下面这种写法是会编译错误的，我们需要修改其中一个包的名称使其统一，且一般我们包名会有目录名相同（虽然说不同也不会出错）

```go
// a目录

  // a.go
  package main // !! error，该目录下存在两个包

  // b.go
  package b // !! error，该目录下存在两个包
```

### 访问权限

变量这节我们说了声明的时候大写开头即为public，小写开头为private，这些都是针对包来讲的

```go
// a.go
package main

const foo = "bar"

// b.go
package main

import "fmt"

func do() {
  fmt.Println(foo)
}

// main.go
package main

func main() {
	do() // -> bar
}
```

同目录下的包可以无缝的相互调用其方法或变量等，也不需要设置为大写开头

下面这个例子就需要大写了

```go
// a/b目录

// b.go
package b

import "fmt"

const Hello = "hello"

func Do(str string) {
  fmt.Println(str)
}

// a目录

// main.go
package main

import "someProject/b" // 注意，除了b.go里的这个方法需要大写开头之外，在该文件中还需要将其import进来

func main() {
	b.Do(b.Hello) // -> hello
}
```

### 省略包名（不建议）

包名是可以省略的，但我们不建议这么使用

```go
// a/b目录

// b.go
package b

import "fmt"

const Hello = "hello"

func Do(str string) {
  fmt.Println(str)
}

// a目录

// main.go
package main

import . "someProject/b" // 注意，在导入的包前面有一个点

func main() {
  Do(Hello) // -> hello
}
```

### 包的init方法

每个包（包括主包）都可以声明n个init方法，如果会在导入后第一时间调用，如果是主包，在main之前调用

```go
package main

func init() {
  fmt.Println("init")
}

func main() {
  fmt.Println("main")
}

/*
执行这个文件后依次打印
init
main
*/
```

如果是多个的话，**注意：可以多个同样名字的init方法**

```go
package main

import "fmt"

func init() {
  fmt.Println("init 1")
}

func init() {
  fmt.Println("init 2")
}

func init() {
  fmt.Println("init 3")
}

func main() {
  fmt.Println("main")
}

/*
执行这个文件后依次打印
init 1
init 2
init 3
main
*/
```

如果是层层引用的情况下，init方法自内向外执行

```go
// a/b/c目录
package c

import "fmt"

func init() {
  fmt.Println("init c 1")
}

func init() {
  fmt.Println("init c 2")
}

// a/b目录
package b

import "fmt"
import _ "someProject/b/c" // 注意，使用_可以只导入包而不使用任何方法

func init()  {
  fmt.Println("init b")
}

// a目录
package main

import "fmt"
import _ "someProject/b" // 注意，使用_可以只导入包而不使用任何方法

func init() {
  fmt.Println("init")
}

func main() {
  fmt.Println("main")
}


/*
执行这个文件后依次打印
init c 1
init c 2
init b
init
main
*/
```

