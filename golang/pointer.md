## 指针

golang的指针操作其实很简单，也没有其他语言的空指针异常这些难用的情况

简单来说 &符号用来获取值的指针， \*符号用来根据指针获取值

```go
a := "foo"

pa := &a // pa就是a的指针

fmt.Println(pa) // -> 0xc42029c0a0
fmt.Println(*pa) // -> foo
```

通过指针操作

```go
a := "foo"

pa := &a // pa就是a的指针

*pa = "hahaha"

fmt.Println(a) // -> hahaha
```

传递指针

如果在js中，定义一个基本类型的变量，通过一个函数要改变这个变量是做不到的，但在golang中就可以做到

```go

func do(val *string) {
  *val = "hello"
}

a := "foo"

do(&a)

fmt.Println(a) // -> hello
```

结构体中的指针应用

考虑下面一个例子，如果对golang的值传递有印象的话，这里的结果就很明显，a的val不会被改成hello而是继续保持abc，因为在调用do方法的时候，这个struct被拷贝了一次

```go

type Stru struct {
  val string
}

func (s Stru) do() {
  s.val = "hello"
}

a := Stru{"abc"}

a.do()

fmt.Println(a.val) // -> 这里会打印什么？
```

以上例子中将do方法改成这样就可以了

但值的注意的是，参数为struct的指针时并不需要(\*s).val的方式，golang会自动转换

```go
func (s *Stru) do() {
  s.val = "hello"
}

// 同样的做为参数也不需要指针转换为值
func do(s *Stru) {
  s.val = "hello"
}
```

另外，日常开发中，slice与map两类数据类型，一般我们不使用指针传递