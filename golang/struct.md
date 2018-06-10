
## 结构体

结构体（struct）也属于一种数据类型，但与我们常见的数据类型不同，所以我们分开讲

struct是属于复合类型，包括之前讲的array、slice和map都属于复合类型，但他更为灵活，也更有其特点

### 声明一个struct

下方例子中的struct包含了一个类型为string的name，类型为int的age和类型为map的skill，当然包括之前讲到的其他类型都可以包含在内

```go

type myStruct struct {
  name string
  age int
  skill map[string]int
}

```

### 如何使用他

前面的struct只是定义了一个框架，一个结构，接下来，我们根据这个框架，定义一个struct，注意的是逗号的使用，如果是换行声明的话，最后的那个逗号是不可以省略的，包括map也是，在javascript中我们称为尾逗号，如果同行声明，那就不需要了

```go

m := myStruct{
  name: "eason",
  age: 18,
  skill: map[string]int{
    "js": 70,
    "golang": 60,
    "css": 70,
  },
}

// 注意：不能使用make方法创建

m2 := make(myStruct) // !!! 编译错误

```

当然他还可以更简洁的使用，不过一般我们不建议这样用，原因很简单，不易阅读（struct内定义的内容少的话可以考虑）

```go
m := myStruct{
  "eason",
  18,
  map[string]int{
    "js": 70,
    "golang": 60,
    "css": 70,
  },
}
```

另外我们可以省略部分或全部定义，其他的部分系统会用零值填充

```go
m := myStruct{name: "eason"}
```

### 给struct定义内部方法

可能有同学会觉得，struct和map[string]interface{}的区别在哪里，我们就来说说struct更有趣的地方

```go
type myStruct struct {
  name string
  age int
  skill map[string]int
}

func (m myStruct) printName(say string) {
  fmt.Println(say + m.name)
}

m := myStruct{name: "eason"}

m.printName("hello, i am ") // -> hello, i am eason
```

### struct是按值传递的

不管定义内部方法或是将struct作为参数传递给方法，系统都会拷贝一份，当然如果其内部使用了slice或map这类引用传递的数据，是拷贝他们的引用

所以上面这个例子中如果修改m.name的话，在外部是不受影响的，我们需要这样做才是正确的

```go

func (m *myStruct) editName(name string) {
  m.name = name
}

m := myStruct{name: "eason"}

m.editName("woow~")

fmt.Println(m.name) // -> woow~

```

可以看到我们定义editName时在其前方使用的是\*myStruct，是一个指针；通常情况下，我们建议都使用指针操作，这样更节省资源，因为拷贝这个过程是需要消耗资源的

**除此之外，struct可以实现接口，这个我们在接口这章会讲**



