# http头相关的知识梳理

## CORS跨域

### Access-Control-Allow-Origin

跨域白名单，只能设置一个域名，当然你可以在设置之前进行判断请求的url，然后动态的设置该值，如果设置'\*'，则表示所有域名均可以访问

```
Access-Control-Allow-Origin: *

Access-Control-Allow-Origin: https://www.baidu.com
```

### Access-Control-Allow-Methods

默认只允许get、head和post，如果要使用其他的method，则需要设置该头，并用逗号分隔多个method

```
Access-Control-Allow-Methods: PUT, DELETE, PATCH
```

### Access-Control-Allow-Headers

除了系统自带的header之外，在跨域情况下，自定义的header都是不被允许的，若我们需要使用，则要设置该头，同样的，该头也使用逗号分隔多个header

```
Access-Control-Allow-Headers: X-My-Header, X-Your-Header
```

### Access-Control-Max-Age

除了get、head和post，在跨域情况下，其他method请求时都需要预请求，也就是options请求，在该请求的response中，他可以设置这个值延长预请求的允许时间，他的值是个秒数，比如设置1000秒，那么在第一次options请求之后，在1000秒时间限制内，不会再发起options请求

```
Access-Control-Max-Age: 1000
```


## 缓存策略

### Cache-Control

Cache-Control可以设置多个值，并用逗号分隔开，常用的几个如下，no-cache、no-store和max-age，设置了no-cache/no-store或must-revalidate之后，强缓存将会失效


#### no-cache

no-cache表示不使用缓存，但不会禁用，也就是说会把数据缓存下来但每次都是请求服务器去拿最新的数据

如果一个资源在服务器有配置ETag或Last-Modified时，在下一次的请求中会自动把他们携带在header中，header中的If-None-Match和ETag值相同，If-Modified-Since和Last-Modified值相同，服务端可以判断这两个值然后返回304，这样就会走协商缓存，也就是使用本地缓存，否则也可以返回200给新的内容

```
Cache-Control: no-cache
```

#### no-store

no-store简单来说会禁用缓存，每次重新请求时，浏览器都会将他当作一个新的请求来处理，发起请求时不会带上If-None-Match和If-Modified-Since等和缓存相关的header

```
Cache-Control: no-store
```

#### max-age

cache-control的max-age属性可以设置一个过期时间的秒数，表示在多少秒后过期，在没有过期的时间范围内，再次刷新浏览器不会发起这个请求，直接使用本地的缓存，即强缓存；当然如果同时包含了no-cache/no-store或must-revalidate的时候例外

```
Cache-Control: max-age=31536000
```

#### public/private

这两个值表明缓存是否能被代理服务器缓存，public允许，private不允许，一般会配合max-age和s-maxage使用

如果允许代理服务器有缓存，默认情况下缓存时间等同于max-age，但也可以使用s-maxage单独修改代理服务器的缓存时间，代理服务器缓存的好处就是当一个用户访问过了，那么在缓存期间，其他任何用户都能从这台服务器上拿到缓存数据

```
Cache-Control: public, max-age=31536000

Cache-Control: private, max-age=0
```

如果要对访问的用户进行划分缓存，在服务器端也可以设置Vary这个头，他的值等于一个header名称，也就是服务器针对这个header，做缓存数据

例子：

```
// 服务器设置头
Vary: 'X-Test-Cache'


// client请求时的头
X-Test-Cache: 1  // -> 这次请求不会有缓存(在没有别的用户携带该头请求的情况下)，因为第一次使用

// client再次请求
X-Test-Cache: 1 // -> 有缓存

// client再次请求
X-Test-Cache: 2 // -> 没有缓存

// client再次请求
X-Test-Cache: 2 // -> 有缓存
```

### Last-Modified

文件的最后修改时间，理论上他的值可以是任意的，并不一定是个时间

在请求时如果返回了Last-Modified，那在下次请求中会在header中携带If-Modified-Since头，值与Last-Modified相同(Cache-Control设置了no-store的情况下除外，他不允许携带)

这时服务端可以根据某种规则计算出If-Modified-Since的值是否与资源的某个值相同，然后判定这个文件是否改变，如果没有，服务端返回304即可，告诉发起端继续使用本地的缓存文件；如果需要更新，则返回200，然后给定新的内容与新的Last-Modified

```
Last-Modified: Wed, 20 Jun 2018 10:50:00 GMT
```

### ETag

文件标识，用于判断文件是否更新，用法于Last-Modified类似，再次请求时，ETag的值会被放到If-None-Match中

```
ETag: W/"1b77-apP1Gqm7H/wVnUafUMQTmXl4L3w"
```

### Expires

该属性是个时间，也用于设置缓存过期时间，与max-age不同的是，他是指定一个到期时间，一般来讲max-age比较好用，所以更推荐max-age，当然一般大型网站两个属性都会同时设置，原因是Expires是http1.0的规范，而max-age是http1.1的，如果客户端只支持http1.0的话呢...

```
Expires: Fri, 21 Jun 2019 19:07:49 GMT
```

### 结论

所以综合以上几点，像是一般的静态资源文件，我们可以这样设置

```
Cache-Control: public, max-age=31536000
Last-Modified: Wed, 20 Jun 2018 10:50:00 GMT
ETag: W/"1b77-apP1Gqm7H/wVnUafUMQTmXl4L3w"
Expires: Fri, 21 Jun 2019 19:07:49 GMT
```

给max-age设置一个相对较长的时间范围，并且携带上Last-Modified和ETag，如果在这个时间范围内，我们直接就走强缓存，超出时间后，服务端再根据Last-Modified和ETag对应的If-Modified-Since和If-None-Match来判断文件是否更新，如果没有更新返回304继续使用原来的缓存文件，然后在补上一句Expires，防止用户因为只支持http1.0而无法缓存

当我们要更新该文件时，记住不能直接更新，因为走的强缓存，在没过期的情况下更新是不起作用的，我们一般的做法是给文件换个名字来获取最新的内容

**另外**

如果要禁用缓存，可以这样

```
Cache-Control: no-cache, no-store, must-revalidate
```
must-revalidate表示缓存必须在使用之前验证旧资源的状态，并且不可使用过期资源。

当然Cache-Control还有其他几个属性，不过比较小众，有需要了解的可以去网上搜索

## 数据协商

数据协商相关的头主要就是Accept、Accept-Encoding和Accept-Language等，更多的可以去网上参考

### Accept

这个头用来告知服务端需要返回的数据的格式

```
Accept: application/javascript
```

相应的，服务端在返回数据时可以使用Content-Type告知实际返回数据的格式

```
Content-Type: application/javascript
```

### Accept-Encoding

这个头用来告知服务端数据压缩的方式，多个时用逗号分隔

```
Accept-Encoding: gzip, deflate, br
```

相应的，服务端在返回数据时可以使用Content-Encoding告知实际返回数据的压缩方式

```
Content-Encoding: gzip
```

### Accept-Language

这个头用来告知服务端所需要的语言，多个时用逗号分隔，每一个语言信息之间再用分隔分割，前一个表示语言，后一个q=表示权重

```
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,ja;q=0.7,zh-TW;q=0.6
```
上面这段表示中文权重0.9，英文权重0.8，等等...

同样的，服务端在返回数据时可以使用Content-Language告知实际返回数据的语言

```
Content-Language: de-DE
```

## CSP内容安全策略

在html页面内的所有资源、链接请求都是可控的，我们可以通过设置CSP相关的内容来控制是否允许一个资源或者一个请求进行发送

...