

#### nginx带参数做301/302跳转

正则匹配某个参数(js)
```js
var url = '/301test.html?b=2&a=1&c=3'
var reg = new RegExp("^/.*\\?.*a=([^&]*)")
console.log(url.match(reg))
```

正则匹配所有参数(js)
```js
var url = '/301test.html?b=2&a=1&c=3'
var reg = new RegExp("^/.*\\?(.*)")
console.log(url.match(reg))
```

通过以上两个正则，可以在nginx中这样写：
```
location /302test.html {
    rewrite ^/.*?(.*) http://www.baidu.com/?newparams=new$1 redirect;
}
```
如果要301的话，将redirect改为permanent

