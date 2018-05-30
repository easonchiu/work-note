

## map的特性

### map的基本用法:

```go
m := map[string]string{
  "a": "1",
  "b": "2",
  "c": "3",
}

m2 := map[string]string{} // -> 空的map
m3 := make(map[string]string) // -> 也是一个空的map
```

### map的key必须是组内唯一的：
```go
foo := map[bool]bool{
  true: true,
  false: false,
  true: false, // -> Error!! 编译错误
}
```

### 设置值：
```go
m := make(map[string]string)

m["first"] = "hello"

fmt.Println(m) // -> [first:hello]
```

### 任意值的map
一般情况下，map在初始化时是要声明key和value的数据类型的，但如果value的类型是interface{}，那它便是个任意类型的map
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
  // ...
}

// 我们把上面m2的world传递给hello这个函数，hello接收一个string，world的值是string吗？

hello(m2["world"])  // -> 实际情况： 编译错误!!

// 其实它当然是string

fmt.Printf("%T", m2["world"]) // -> string

// 但我们要这样使用
hello(m2["world"].(string)) // ok!
```
需要注意的是这种转换与强制类型转换不同，当它不属于这个类型时，它是会报runtime错误的！！

### 获取一个值：
```go
foo := map[string]int{
  "a": 1,
}

val := foo["a"]

fmt.Println(val) // -> 1
```

### 修改一个值：
```go
foo := map[string]int{
  "a": 1,
}

foo["a"] = 288

fmt.Println(foo["a"]) // -> 288
```

### 删除一个值：
```go
foo := map[string]int{
  "a": 1,
}

delete(foo, "a")

fmt.Println(foo["a"]) // -> 0  因为int类型的空值为0
```

### 判断值是否存在：

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


### map在作为参数传递的时候有个特殊的情况：

```go

func set(m map[string][string]) {
  m["a"] = "999"
}

m := map[string]string{
  "a": "1",
}

fmt.Println(m) // -> map[a:1]

set(m)

fmt.Println(m) // -> map[a:999]
```

也就是说，当将map传递给另一个函数，那个函数如果对该map进行操作，这个map本身也会被改变，这一点和大多数数据类型的表现不同。

### 下面这种情况也不例外：
```go

type test struct {
  foo map[string]int
}

func set(s test) {
  s.foo["a"] = 999
}

s := test{
  foo: map[string]int{
    "a": 1,
  }
}

fmt.Println(s) // -> {map[a:1]}

set(s)

fmt.Println(s) // -> {map[a:999]}
```
