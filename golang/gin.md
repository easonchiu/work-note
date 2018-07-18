## Gin框架

首先我们通过最简单的一个例子了解一下Gin框架

第一步，安装并搭建Golang

第二步，创建项目并执行`go get -u github.com/gin-gonic/gin`

第三步，在main.go中编写如下代码

```go
package main

import "github.com/gin-gonic/gin"

func main() {
  r := gin.Default()
  r.GET("/ping", func(c *gin.Context) {
    c.JSON(200, gin.H{
      "message": "pong",
    })
  })
  r.Run()
}
```

第四步，运行并打开浏览器`http://localhost:8080/ping`

不出意外的话，浏览器中将可以看到
```
{ message: "pong" }
```

#### 路由参数

```go
r.GET("/api/:name", func(c *gin.Context) {
  name := c.Param("name")

  c.String(200, name)
})
```

```
get http://localhost:8080/api/hello // -> "hello"
```

#### query参数

使用c.Query时如果没有传递参数取到的值会是零值，如果要确定是否有获取到值的话，使用GetQuery

```go
r.GET("/api", func(c *gin.Context) {
  name := c.Query("name")

  age, ok := c.GetQuery("age")
  
  if ok {
    c.String(200, fmt.Sprintf("name=%v,age=%v", name, age))
  } else {
  	c.String(200, name)
  }
})
```

```
get http://localhost:8080/api?name=who // -> "who"
```

#### Raw参数

获取该参数较为复杂，Gin框架没有做封装，一般我们会在开发早期对Gin框架做一些扩展，通过一些第三方解析json的库获取body中的参数，比如gjson

```go
bytes, err := c.GetRawData()

if err != nil {
  panic(err)
}

res := gjson.GetBytes(bytes, "key")

// res是个封装的struct，有很多有用的方法
res.Str // 字符串类型的值
res.Int() // int类型的值
...
res.Exists() // 值是否存在
```

直接用的话相信大家会崩溃，所以有兴趣的可以尝试使用并封装一些方法出来，比如我是这样使用的

```go
ctx := CreateCtx(c) // 对Gin的Context进行封装，返回自己包装过的一个struct，然后就好做了，给这个struct扩展方法就好了

nickname, exist := ctx.getRaw("nickname")
age := ctx.getRawIntDefault("age", 18)
...
```
#### 请求头和设置头

```go
// 获取header
ua := c.GetHeader("User-Agent")

// 设置header
ua := c.Header("X-Real-IP", "0.0.0.0")
```

#### 路由分组

Gin和Koa一样，可以对路由进行分组

```go
r := gin.Default()

// 对用户的CURD操作
users := r.Group("/users")

users.GET("", GetUsersList)
users.GET("/:id", GetUsersOne)
users.POST("", CreateUser)
users.PUT("/:id", UpdateUser)

// 对商品的CURD操作
goods := r.Group("/goods")

goods.GET("", GetGoodsList)
goods.GET("/:id", GetGoodsOne)
...

```

#### 中间件

注意，gin.Default() 默认是加载了一些框架内置的中间件的，而 gin.New() 则没有，根据需要自己手动加载中间件。

所以一般在本地开发时使用Default模式，线上正式环境使用New()

中间件有3种方式注入

##### 全局中间件

```go
r := gin.New()

r.Use(gin.Logger()) // 自带的log中间件
```

##### 组内中间件

```go

r := gin.New()

users := r.Group("/users")
users.Use(gin.Logger()) // 只对/users开头的路由添加中间件

```
##### 单个路由的中间件

```go
r := gin.New()

r.GET("/users", gin.Logger(), GetUsersList)
```

##### 中间件的写法

中间件的写法相当简单，而且跟Koa也很类似

通过c.Next()继续向下执行即可

```go
func test(g *gin.Context) {
  fmt.Println("before test")
  g.Next()
  fmt.Println("after test")
}
```

中间件可以是多个，并且跟Koa一样是洋葱皮的方式

```go

// 中间件1
func test1(g *gin.Context) {
  fmt.Println("before test1")
  g.Next()
  fmt.Println("after test1")
}

// 中间件2
func test2(g *gin.Context) {
  fmt.Println("before test2")
  g.Next()
  fmt.Println("after test2")
}

r.GET("/ping", test1, test2, func(c *gin.Context) {
  c.JSON(200, gin.H{
    "message": "pong",
  })
})

r.Run("1234")

// 访问网页
get http://localhost:1234/ping

// 打印结果
before test1
before test2
after test2
after test1

```

#### 日志文件

Gin自带日志写入文件的方法，在启动之前写入以下代码，就可以在同目录的gin.log中保存日志了

```go
gin.DisableConsoleColor()
f, _ := os.Create("gin.log")
gin.DefaultWriter = io.MultiWriter(f)
```

#### 重定向

```go
r.GET("/test", func(c *gin.Context) {
  c.Redirect(301, "http://www.baidu.com")
})
```

#### 状态码

前面的例子中我们都是手写的状态码，golang自带的的http库中有所有的http status可供使用，他们都是常量

```go
http.StatusOK = 200
http.StatusMovedPermanently = 301
http.StatusForbidden = 403
http.StatusNotFound = 404
...
```

**关于Gin的介绍就暂时到这里**

因为我们现在的工作当中http server一般只用来提供数据操作而不渲染页面，所以通过以上的内容就可以搭建一套REST Api了

另外，数据库也有mongodb的mgo库，redis的Redigo、go-redis库，还有Go-MySQL-Driver这些等等。

golang的优秀http框架也不止Gin一个，只不过Gin的性能较好，而且语法与Koa相似，个人比较喜欢，其他框架有兴趣的可以自行学习研究