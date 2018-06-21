## 接口

golang没有面向对象这个概念，所以他没有继承、多态等特性，也没有构造函数这些，他更推崇面向接口的这种设计模式

他也不像java的接口，java的接口是必须实现这个接口；而golang用的是duck typing的模式，例如：

```go

// 定义了一个接口，这个接口有一个do方法
type iTest interface {
  do(string) string
}

// 实现该接口
type myStruct struct {}

func (myStruct) do(s string) string {
  return "hello"
}
```
上方例子中的myStruct就实现了iTest接口，可以看到在实现这个接口时并没有直接的耦合关系，而是定义的这个struct实现了一个do方法，这个do方法的入参和出参与iTest接口的do方法是一样的，这种情况下，我就即可称为，myStruct实现了iTest接口

**在业务场景中的使用**

比如我们有个方法是这样的

```go
type iPerson interface {
  getName() string
}

func sayHello(p iPerson) {
  fmt.Println("hello, i am", p.getName())
}
```

这个sayHello方法会打印传进来参数的名字，而这个参数是个接口，当我们要调用sayHello这个方法时，就需要这个参数是实现了iPerson的

```go
// 学生的struct，有姓名和班级
type student struct {
  name string
  class string
}

func (s student) getName() string {
  return s.name
}

func (s student) getClass() string {
  return s.class
}

// 调用该方法
someone := student{
  name: "eason",
  class: "1(4)",
}

sayHello(someone) // -> hello, i am eason
```

这样的话，这个sayHello就很灵活，我们不光可以给他一个student，只要是实现了iPerson的struct，我们都可以作为这个方法的参数

```go
// 老师的struct，有姓名和年龄
type teacher struct {
 name string
 age int
}

func (t teacher) getName() string {
  return t.name
}

func (t teacher) getAge() int {
  return t.age
}

// 调用该方法
someone := teacher{
  name: "ken",
  age: 43,
}

sayHello(someone) // -> hello, i am ken
```

当然interface的方法是可以任意多的，这里不做演示，另外interface是可以组合的，下面的例子中，iIT这个interface将iPerson与iMan进行了组合，就是说如果要实现iIT的话就需要同时实现iPerson和iMan两个interface

```go
type iPerson interface {
  getName() string
}

type iMan interface {
  work(job iJob)
}

type iIT interface {
  iMan
  iPerson
}

func sayHello(p iIT) {
  fmt.Println("hello, i am", p.getName())
}

func working(p iIT, job string) {
  p.work(job)
}
```

我们来实现一下

```go
type it struct {
  name string
  job  string
}

func (i *it) getName() string {
  return i.name
}

func (i *it) work(job string) {
  i.job = job
}

i := it{name: "eason"}

sayHello(&i) // -> hello, i am eason

working(&i, "golang")
```

这里注意一个细节，我们在给it这个struct添加方法时都是使用了\*it而不是it，因为work方法是设置值，如果不使用指针的话，在golang里他会按值传递，也就是拷贝一份，所以这个设置的值不会设置到需要被设置的这个it，而是一个新的，简单的说，这个设置的值不会生效，这显然不是我们想要实现的；另外一个习惯就是，尽量使用指针，这样能最大的避免这类错误的出现，同时也能提高效率。