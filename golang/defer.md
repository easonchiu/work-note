## defer

defer是个关键字，在其后方执行函数，他会在延后到函数退出之前执行

```go
func do() {
  fmt.Println("do")
}

func main() {
  defer do()

  fmt.Println("main")
}

/*
run这个文件依此打印：
main
do
*/
```

### 也可以使用匿名函数的方式

```go
func main() {
  defer func() {
	fmt.Println("defer")
  }()

  fmt.Println("main")
}

/*
run这个文件依此打印：
main
defer
*/
```

### 一个方法内可以有多个defer，但顺序是倒序执行的

```go
func main() {
  defer func() {
	fmt.Println("defer1")
  }()

  defer func() {
	fmt.Println("defer2")
  }()

  defer func() {
	fmt.Println("defer3")
  }()

  fmt.Println("main")
}

/*
run这个文件依此打印：
main
defer3
defer2
defer1
*/
```

### defer有什么用

因为有些方法在调用并使用完成后是需要关闭的，比如数据库连接、http库的请求等

```go
func main() {
  resp, _ := http.Get("http://www.baidu.com")
  defer resp.Body.Close() // 在方法退出的时候关闭reader

  bytes, _ := ioutil.ReadAll(resp.Body)

  fmt.Printf(string(bytes)) // -> 百度的html内容输出...
}
```

还有就是用于错误处理，recover，我们在panic/recover这章来讲