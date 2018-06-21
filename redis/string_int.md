# string/int类型

该类型是最常见的一种类型，一个key对应一个value；可以简单的理解为有点像localStorage或是cookie这些；如果value是个int类型的话，他可以做一些增加或减少的操作

## 存

```
set key value
```

## 取

```
get key
```

## 先获取再存值

先返回值然后再设置值

```
getset key value
```

## 删除

返回删除掉的个数

```
del key
```

### 数值增减

#### 增加1

如果存的值为数字类型，则增加1，否则将报错，如果值不存在，将视为0，执行之后返回1

```
incr key
```

#### 增加指定数字

与incr类似，不同的是要传第2个参数，是个int类型，表示要增加多少

```
incrby key number
```

#### 减少1

如果存的值为数字类型，则减少1，否则将报错，如果值不存在，将视为0，执行之后返回-1

```
decr key
```

#### 减少指定数字

与decr类似，不同的是要传第2个参数，是个int类型，表示要减少多少

```
incrby key number
```

### 追加

相当于一个拼接工作，需要注意的是拼接完后返回的是个string类型的，如果两数字相加后，即使看起来像个数字，实际上他已经不能再做incr/decr/incrby/decrby之类的操作了

```js
// key = 3

append key 1

get key // -> 31
```

如果当这个key不存在，则相当于set

