
### 取某个返回值
```go
// 这是一个返回4个int的方法
func get() (int, int, int, int) {
  return 1, 2, 3, 4
}

// 我只需要第三个值
_, _, v, _ := get()
```

### for range中
一般情况下，for range会产生两个变量
```go
for k, v := range some {
  // ...
}

// 如果不要key
for _, v := range some {
  // ...
}

// 如果不要value呢？不设置这个变量就行...
for k := range some {
  // ...
}
```

### 初始化包
我们知道import一个包的时候，如果这个包内有init方法，会首先调用他，然后再调用其main方法(如果有)

```go
// 但如果我们这么写有什么好处呢
import _ 'xxx/xxx/xxx'
```
实际上是这样的，上面的这个写法会调用这个包的init方法，但不去使用这个包的任何方法（如果普通的导入，而没有使用的话，是编译不过的）

比如我们有个包会去连接数据库，但当前的这个包中我不需要实际去操作他，可能在接下来的某一步骤使用他，那么可以把连接的这个操作写在该包的init方法中，并用这种方式去预连接数据库


### iota跳跃
iota是逐个叠加的
例子：
```go
const (
  a = iota
  b
  c
  d
)

// 这时，a=0, b=1, c=2, d=3
```
但如果我不需要中间的某个数字，可以这样
```go
const (
  a = iota
  _
  b
  c
  d
)

// 这里变成了a=0, b=2, c=3, d=4
```


### 表达式
iota是可以进行运算的
```go
const (
  a = iota * 2 + 1
  b
  c
  d
)

// a, b, c, d => 1, 3, 5, 7
```


### 用于判断struct是否实现了interface
例子：
```go

// 定义一个结构体
type demo struct {
  ValA string
  ValB string
}

// 定义一个接口
type iDemo interface {
  Get()
}

// 判断demo是否实现了iDemo
// 如果未实现，则会编译错误
// 比如现在这样就会编译错误，因为该struct并没实现Get方法
var _ iDemo = &demo{}

// 实现Get
func (d demo) Get() {
  // do something...
}

// 这里就不会编译出错了
var _ iDemo = &demo{}

```
