## map

### 声明一个map

```go
m := map[string]string{
  "a": "1",
  "b": "2",
  "c": "3",
}

m2 := map[string]string{} // -> 空的map
m3 := make(map[string]string) // -> 也是一个空的map
```

### map的key必须是组内唯一的
```go
foo := map[bool]bool{
  true: true,
  false: false,
  true: false, // -> Error!! 编译错误
}
```

### map的key的限制

key不允许func、map或是slice，它可以是个struct，但实际好像意义不大

反正别太花哨就行，一般我们用map[string]Type或是map[int]Type这些会比较多

```go
// 以下都会报错

m1 := map[func()]string{}

m2 := map[map[int]int]string{}

m3 := map[[]int]string{}
```


### 设置值
```go
m := make(map[string]string)

m["first"] = "hello"

fmt.Println(m) // -> [first:hello]
```

### 任意类型值的map
一般情况下，map在初始化时是要声明key和value的数据类型的，但如果value的类型是interface{}，那它便是个任意类型值的map
```go

m := map[string]interface{}{
  "bool": true,
  "num": 123,
  "str": "hello",
}

// 当然你也可以更夸张的这样

m2 := map[interface{}]interface{}{
  false: true,
  321: 123,
  "world": "hello",
}
```

但是interface类型会有这样一个问题

```go
func hello(str string) {
  // do something
}

m2 := map[string]interface{}{
  "world": "hello",
}

// 我们把上面m2的world传递给hello这个函数，hello接收一个string，world的值是string吗？

hello(m2["world"])  // -> 实际情况： 编译错误!!

// 其实它当然是string

fmt.Printf("%T", m2["world"]) // -> string

// 但我们要这样使用，告诉编译器我们要把他当作string来使用
hello(m2["world"].(string)) // ok!
```
需要注意的是这种转换与强制类型转换不同，当它不属于这个类型时，它是会报runtime错误的！！

### 获取

如果取的值不存在，并不会报错，而是返回该类型的一个空值

```go
foo := map[string]int{
  "a": 1,
}

a := foo["a"]
b := foo["b"]

fmt.Println(a) // -> 1
fmt.Println(b) // -> 0，其实b并不存在
```

### 修改
```go
foo := map[string]int{
  "a": 1,
}

foo["a"] = 288

fmt.Println(foo["a"]) // -> 288
```

### 删除
```go
foo := map[string]int{
  "a": 1,
}

delete(foo, "a")

fmt.Println(foo["a"]) // -> 0  因为int类型的空值为0
```

### 判断值是否存在

默认情况下，如果一个值不存在，得到的结果将会是一个零值（看map的类型而定，可能是0、false、nil或""）

可以用两个值去接map的某个key，得到返回值和是否存在的一个bool
```go
foo := map[string]int{
  "a": 1,
}

val, exist := foo["a"]

fmt.Println(val, exist) // -> 1 true

delete(foo, "a")

val, exist = foo["a"]

fmt.Println(val, exist) // -> 0 false
```


### map在作为参数传递

map作为参数传递时的表现和slice是几乎一样的，其原因是因为他内部有个指针指向一个hash表

```go

func do(m map[string][string]) {
  m["a"] = "999"
}

m := map[string]string{
  "a": "1",
}

fmt.Println(m) // -> map[a:1]

do(m)

fmt.Println(m) // -> map[a:999]
```

也就是说，当将它传递给另一个函数并操作，因为他内部有个指针指向同一个hash表，这个map本身当然是会被改变的。

### map的长度

我们可以使用内建的len方法获取map的长度

```go

m := map[string]string{
  "A": "a",
  "B": "b",
  "C": "c",
}

len(m) // -> 3
```

### map的遍历

map的遍历主要使用for range的方式

```go
m := map[string]string{
  "A": "a",
  "B": "b",
  "C": "c",
}

for key, value := range m {
  fmt.Println(key, value)
}

/*
注意：顺序是不一定的
A a
B b
C c
*/
```

### map在嵌套时可省略内部的map类型

标题比较绕，具体看例子：

```go
m := map[string]map[int]int{
  "A": {
    1: 1,
    2: 2,
  },
  "B": {
    3: 3,
    4: 4,
  },
}
```
这里的例子是map的key是string类型的，但他的value是个map[int]int类型，所以在声明的时候不需要声明value的这个类型

```go
// 不需要这样做（"A"后面的map[int]int可以省略），但也不会报错
m := map[string]map[int]int{
  "A": map[int]int{
    1: 1,
    2: 2,
  },
}
```

### 除此之外value是个struct也可以省略
```go
type some struct {
  name string
  old int
}

m := map[string]some{
  "A": {
    name: "eason",
    old: 18,
  },
  "B": {
    name: "who?",
    old: 100,
  },
}
```

