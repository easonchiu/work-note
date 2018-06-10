## 流程控制

### if / if ... else ...

一般用法其实golang与其他语言差不多，不同在于golang的条件都是不需要括号的

```go
value := "foo"

if value == "foo" {
  // do something...
}
```

他只有==比较，并没有像javascript一样有==也有===，因为golang不存在隐式转换，不同类型的值之间也是不可以比较的

```go
value := "foo"

if value == "foo" {
  // do something...
} else {
	// else...
}
```

```go
value := "foo"

if value == "foo" {
  // do something...
} else if value == "bar" {
	// else if...
} else {
	// else...
}
```

还允许这样使用，将if或者else if用分号分为两段，前一段声明，后一段判断条件（即使是bool类型判断是否为true，也必须要使用==）

```go

//这个函数，返回一个string为"foo"，和一个bool
func do() (string, bool) {
  return "foo", true
}

if str, b := do(); b == true {
  fmt.Println(str) // -> "foo"
}

```

需要注意的是，这个例子中的str的访问权限仅在条件语句内，在他之外是不可以访问的


### for

golang的for用法比较丰富，因为他没有while循环，所以while能做的事用for基本能做（类似javascript中的do...while...除外）

基本用法

```go
for i := 0; i < 10; i++ {
  fmt.Println(i) // 1 2 3 4 5...
}
// 同样的，在for条件中声明的这个i在外部是不能访问的
```

类似while(条件)的用法

```go
val := true

for val {
  fmt.Println("do...") // 这句话会连续打印1秒

  time.AfterFunc(time.Second, func() {
    val = false // 1秒后将val设置成false
  })
}
```

死循环

其实golang经常会用到死循环，在并发编程中我们会接触更多

```go
for {
  fmt.Println("do...") // 永远会打印下去...
}
```

还有就是for-range的用法

相当于javascript中的for-in、for-of之类的

```go
slice := []string{"a", "b", "c", "d"}

for i, value := range slice {
  fmt.Println(i, value)
}

/*
打印结果：
0 a
1 b
2 c
3 d
*/
```
```go
m := map[string]int{
  "foo": 1,
  "bar": 2,
  "third": 3,
}

for key, value := range m {
  fmt.Println(key, value)
}

/*
打印结果：(要注意的是，对map进行range，打印的顺序是不固定的)
foo 1
bar 2
third 3
*/
```

当然for-range允许我们不使用第二个值，比如数组和slice我们只取下标，map只取key

```go
for i := range slice {
  fmt.Println(i) // 1 2 3 4...
}

for key := range m {
  fmt.Println(key) // foo bar third
}
```

当我们只要值，不需要下标或key的时候可以这样

```go
for _, value := range slice {
  fmt.Println(value) // "a" "b" "c" "d"
}

for _, value := range m {
  fmt.Println(value) // 1 2 3
}
```

就是使用_去声明第一个值，下划线声明会丢掉该值，在相关章节有介绍

### switch

golang的switch和其他语言有几处不同

```go
val := "foo"

switch val {
case "foo":
  // do...
case "bar":
  // do...
default:
  // default do...
}
```

可以看到switch中并没有用到break关键字，golang会默认break，所以不需要显式的去这么写

当然我们需要不break时就用到fallthrough关键字了

```go

val := "foo"

switch val {
case "foo":
  fallthrough
case "foooooo":
  // foo or foooooo...
case "bar":
  // do...
default:
  // default do...
}
```

switch的条件也可以像if一样用分号分为两段

```go
func do() (string, string) {
  return "foo", "bar"
}

switch val1, val2 := do(); val1 {
case "foo":
  fmt.Println(val2) // -> "bar"
default:
  fmt.Println("default...")
}
```

switch的条件可以不写，在case中判断（在javascript中也可以，不过要switch(true)）

```go
val := 72

swtich {
case val > 90:
  // do...
case val > 80:
  // do...
case val > 60:
  // do...
default:
  // do...
}
```
