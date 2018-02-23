# React中常用的ES6语法（未写完）

## const与let
`const`和`let`是es6语法中新出的用于声明变量的关键字，他们和之前的`var`还是有微妙的区别的。但是需要注意的是目前因为浏览器支持度的原因，多数情况下我们会使用像`webpack`这类打包工具对这些语法做转译，所以目前来说，他们最终都会转成`var`，不过在书写过程中我们还是建议优先使用`const`和`let`来取代`var`。

#### 与var的区别

var的最小作用域范围是函数作用域，比如
```js
function foo () {
	var a = 1

	console.log(a) // 正常拿到a的值

	if (true) {
		var b = 2
	}

	console.log(b) // 可以拿到b的值
}
console.log(a) // 报错，因为拿不到a
```

而`const`与`let`的最小作用域是块级作用域。
```js
function foo () {
	if (true) {
		let a = 1
		const b = 1
		var c = 1
	}
	console.log(a) // 拿不到a的值
	console.log(b) // 拿不到b的值
	console.log(c) // 正常拿到b的值
}
```
另外还有个区别就是`const`一旦声明就不可更改，并且在使用`const`声明的时候必须要赋值。
```js
const a // 这样会报错
const b = undefined // 这样允许
```

## 箭头函数

## bind

## import与export

## 解构

## class类

## promise

## async/await

## 注意事项

### Polyfill

### Babel
