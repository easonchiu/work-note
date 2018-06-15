## 错误捕获和处理


golang中的错误抛出和处理用的是panic和recover两个方法

### panic

在没有recover的情况下，调用panic方法会让程序挂掉

```go
func main() {
  panic(errors.New("throw an error."))
}
```

panic接收一个参数，interface{}类型，理论上可以接收任意值，但我们一般都给他一个error类型的值，例子中打印面板会将`throw an error.`打印出来，然后程序就挂掉了

### recover

程序在panic时会一层一层的返回，直到遇到recover停止panic动作；recover只能在defer中使用，是个系统的内建方法，该方法返回panic方法中的参数。

```go
func main() {
  defer func() {
    err := recover()
    fmt.Println(err) // -> throw an error.
  }()

  pan()
}

func pan() {
  panic(errors.New("throw an error."))
}
```

例子中因为有了recover，所以程序正常执行并会打印错误信息。当然如果recover拿到的错误信息不是你想处理的，你可以在其之后重新panic

```go
func main() {
  defer func() {
    err := recover()
    if err.(error) { // 这里使用断言判断他是否是error类型：如果是，则打印；如果不是，则再次panic
      fmt.Println(err)
    } else {
      panic(err)
    }
  }()

  pan()
}

func pan() {
  panic(123)
}
```