# Redux

Redux是FB推出的一个状态管理的库，是在Flux的基础上改善和优化而来的，它的风格是函数式编程、单向数据流、无状态编程的。

### 基础及原理

1. 它在全局只维护一个顶层的状态容器，称为`store`。

1. 它主要由`reducer`、`action`、`dispatch`构成。

1. `action`包含`type`与`payload`，`type`指要做的事情，`payload`是携带的数据。

1. `reducer`是一个方法，传递`state`与`action`，根据`action.type`做相应的业务逻辑上的处理，并返回一个新的`state`，需要注意的是，任何情况下，`reducer`总要返回一个`state`，可以是处理过的新的`state`、也可以是原来的。

1. `dispatch`用于发送`action`并调用`reducer`方法。

1. `store`可以由多个`reducer`组成，在有多个时，使用`combineReducers`将他们合并成一个使用。

1. `store`用redux的`createStore`方法初始化，并且可以传入初始化的`state`，它可以覆盖对应`reducer`的`state`。

1. 基本的用法是`dispatch`一个`action`，`action`根据`type`调用相对应的`reducer`的方法并把`payload`参数传给它，然后`reducer`能拿到旧的`state`、`payload`并做业务逻辑处理，之后`return`一个新的`state`。

1. `store`的`subscribe`方法可以监听上一条发生的事件，在`state`发生变化后，可以在该方法中执行`store.getState()`拿到最新的内容。

例子：
```js
// reducer
const initialState = {
	value: 1
}

const reducer = (state = initialState, action) => {
	switch (action.type) {
		case 'ADD': 
			return {
				...state,
				value: state.value + action.payload
			}
		break

		default: return state
	}
}

// store
import {combineReducers, createStore} from 'redux'

const somePreloadState = {}
// 如果要覆盖reducer中的值，可以这样做
// somePreloadState = { reducerA: { value: 20 } }


const reducers = combineReducers({
	reducerA: reducer,
	reducerB: other // other...
})

const store = createStore(reducers, somePreloadState)

const unsubscribe = store.subscribe(() => {
	console.log(store.getState()) // -> { reducerA: { value: 11 } }
})

// action
const todo = value => {
	return {
		type: 'ADD',
		payload: value
	}
}
store.dispatch(todo(10))
```

# 结合React

本质上redux与react并无直接关系，他们都是独立存在的，甚至你可以将redux用于vue、jquery等其他框架。

redux结合react主要依赖于`react-redux`这个库，它提供一个非常有用的方法`connect`，并配合它的`Provider`组件，将redux的store与react的组件相连接。

这里还可以用到`redux`的一个方法`bindActionCreators`，例子中会用到。

### connect的使用方式
```
const configureConnect = connect([mapStateToProps], [mapDispatchToProps])
const CompnentWithStore = configureConnect(Compnent)
```

##### 它在这里完成了两件事

1. 传入`state`与`dispatch`返回一个配置好的方法，这个新的方法又接收一个组件，返回一个新的组件。

1. 将`store`的`state`捆绑到新组件的`props`上，并`state`一旦变化，组件即更新。

1. 将`store`的`dispatch`捆绑到新组件的`props`上，方便我们调起`action`。

大致代码（继前面的例子）：
```js

// connect
import { bindActionCreators } from 'redux'
import { connect } from 'react-redux'

const connectComponent = Component => {

	const mapStateToProps = state => {
		return {
			reducerA: state.reducerA
		}

		// 你也可以一股脑的将所有的state全捆绑上去，但要注意命名，不要与组件的props的属性重叠了
		return {
			...state
		}
	}

	const mapDispatchToProps = dispatch => {
		return {
			// 这样写的好处就是直接调用todo(10)就可以了，不需要写dispatch(todo(10))
			todo: bindActionCreators(todo, dispatch)
		}
	}

	return connect(
		mapStateToProps,
		mapDispatchToProps,
	)(Component)
	
}

// some component
import React, { Component } from 'react'

class SomeComponent extends Component {
	constructor(props) {
		super(props)
	}

	componentDidMount() {
		this.props.todo(10) // -> dispatch一个action
	}

	render() {
		return (<div>{ this.props.reducerA.value }</div>)
	}
}

const SomeComponentWithStore = connectComponent(SomeComponent) // -> 关键调用

// 挂载
import { render } from 'react-dom'
import { Provider } from 'react-redux'

render(
	<Provider store={store}>
		<SomeComponentWithStore />
	</Provider>,
	document.getElementById('root')
)
```

# 中间件

redux有不少的中间件可供选择。

- `redux-logger`在dispatch时将action信息打印出来
- `redux-promise`能让action是个promise对象
- `redux-thunk`允许action是个异步方法
- `redux-saga`允许action是个generator方法

`createStore`方法的第三个参数允许传递中间件，中间件需要`redux`的`applyMiddleware`方法包裹一层，需要注意添加多个中间件时，顺序是有讲究的，比如logger就必须要在最后。

例子：
```js
const middleares = applyMiddleware(promise, thunk, saga)
const store = createStore(reducers, somePreloadState, middleares)
```

也可以使用`redux`提供的`compose`方法

使用方式：
```js
const middleares = applyMiddleware(promise, thunk, saga)
const composedCreateStore = compose(middleares)(createStore)

const store = composedCreateStore(reducers, somePreloadState)
```











