

### Cache-Control

#### no-cache

```
Cache-Control: no-cache
```

no-cache通常要配合Last-Modified和etag来处理请求，设置过no-cache之后，再次重新请求会发起请求到服务器并携带If-None-Match和If-Modified-Since两个头，If-None-Match的值就是第一次请求时返回的的etag的值，If-Modified-Since则是第一次请求时返回的Last-Modified的值

#### no-store

```
Cache-Control: no-store
```

no-store简单来说会禁用缓存，有设置no-store的请求在每次重新请求时，浏览器都会将他当作一个新的请求来处理，发起请求时不会带上If-None-Match和If-Modified-Since等和缓存相关的头

#### max-age

```
Cache-Control: max-age=31536000
```

cache-control的max-age属性可以设置一个过期时间的秒数，表示在多少秒后过期，在没有过期的时间范围内，再次刷新浏览器不会发起这个请求，直接使用本地的缓存，即强缓存；当然如果同时包含了no-cache或no-store的时候例外

### Last-Modified

文件的最后修改时间，理论上他的值可以是任意的，并不一定是个时间，只要发起请求时携带的Last-Modified与服务端根据某种规则计算出来的值是相同的，就可以认为这个文件没有被改变可以继续使用，这个时候服务端返回304即可，告诉发起端继续使用本地的缓存文件；如果需要更新，则返回200，然后给定新的内容与新的Last-Modified


```
Last-Modified: Wed, 20 Jun 2018 10:50:00 GMT
```

### ETag

文件标识

```
ETag: W/"1b77-apP1Gqm7H/wVnUafUMQTmXl4L3w"
```