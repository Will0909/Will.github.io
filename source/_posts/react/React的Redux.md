---
title: React的Redux
tags: react
categories: 
- react
- react进阶
---

## redux的使用

redux是JavaScript状态容器，提供可预测化的状态管理，本身是一个独立的库，不单单应用于React中, 使用redux可以使状态从组件中脱离出来单独管理。

### redux工作流

![image-20200303224717137](./images/image-20200303224717137.png)

1. 需要一个**store**来存储数据，可以通过`createStore`创建

1. 创建时传入自定义的**reducer**，reducer用于初始化state并定义state修改规则
2. **Actions Creators**通过`dispatch`一个action来提交对数据的修改
3. action提交到Reducer后，Reducer根据action的type做相应动作后返回**全新的state**，并不直接修改state
4. 获取state通过`getState`方法
5. **React Components**通过`store.subcribe`订阅状态变更，手动去重新渲染页面

举例如下：

```js
//store/counter.js
import { createStore } from 'redux'

const counterReducer = (state, action) => {
    switch (action.type) {
        case 'add':
            return state + 1
        case 'minus':
            return state - 1
        default:
            // 初始化state值
            return 0
    }
}

const store = createStore(counterReducer)

export default store
```

```jsx
import React, { Component } from 'react'
import store from '../store/counter'

export default class ReduxTest extends Component {
    componentDidMount() {
        // 订阅状态变更
        store.subscribe(() => {
            this.forceUpdate()
        })
    }

    render() {
        return (
            <div>
                { store.getState() }
                <button onClick={() => store.dispatch({ type: 'add'})}> + </button>
                <button onClick={() => store.dispatch({ type: 'minus'})}> - </button>
            </div>
        )
    }
}
```

## react-redux的使用

react的使用比较繁琐：

1. 每次都需要订阅变更状态去手动重新渲染页面
2. 每次都需要通过getState才能获取最新state值
3. 每个组件都需要引入store

所以，将redux整合到react中，需要引入react-redux

### 基本使用

- Provider为后代组件提供store
- connect为组件提供数据和变更方法

#### 1. 全局提供store

```jsx
// index.js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';
import { Provider } from 'react-redux'
import store from "./store"

ReactDOM.render(
    <Provider store= { store }>
        <App />
    </Provider>,
    document.getElementById('root')
);
```

#### 2. 获取状态数据

- connect可以自动渲染
- 第一个参数是一个函数，传入一个state，返回对象，实际上是将state属性合入props的属性
- 第二个参数是一个对象，对象的属性是一个函数返回action对象，实践上是把dispatch的action合入props属性
- 第二个参数如果未传入，则直接把dispatch合入props属性

```jsx
// ReactReduxTest.js
import React, { Component } from 'react'
import { connect } from 'react-redux'
// 参数1: mapStateToProps = state => return { num: state }
// 参数2: mapDispatchToProps = dispatch => { return {add: () => dispatch({ type: 'add'})}}
@connect(
    state => ({ num: state }), // 状态映射
    {
        add: () => ({ type: 'add' }), 
        minus: () => ({ type: 'minus' })
    },
    // 完整的写法如下：
    // dispatch => ({
  	// 	add: () => dispatch({ type: 'add'}), // action creator
  	// 	minus: () => dispatch({ type: 'minus'}), // action creator
    // })
)
class ReactReduxTest extends Component {
    render() {
        return (
            <div>
                { this.props.num }
                <button onClick={ this.props.add }> + </button>
                <button onClick={ this.props.minus }> - </button>
            </div>
        )
    }
}

export default ReactReduxTest
```

### 异步操作

react只默认支持同步操作，实现异步任务需要中间件的支持， 可以引入`redux-thunk`

#### 1. 在store中引入中间件

```javascript
// store/counter.js
import { createStore, applyMiddleware } from 'redux'
import logger from 'redux-logger'
import thunk from 'redux-thunk'

const store = createStore(counterReducer, applyMiddleware(logger, thunk))
```

#### 2.异步操作的使用

```jsx
// ReactReduxTest.js
@connect(
    state => ({ num: state }), // 状态映射
    {
      	// 返回的是函数
        asyncAdd: () => dispatch => {
            setTimeout(() => {
                // 异步结束后,手动执行dispatch
                dispatch({ type: 'add' })
            }, 1000)
        }
    }
)
class ReactReduxTest extends Component {
    render() {
        return (
            <div>
                { this.props.num }
                <button onClick={ this.props.asyncAdd }> + </button>
            </div>
        )
    }
}
```

#### 3.代码优化

1. 可以将action抽离到store中

```javascript
//store/counter.js
export const add = num => ({ type: 'add', payload: num })
export const minus = num => ({ type: 'minus', payload: num })
export const asyncAdd = num => dispatch => {
    setTimeout(() => {
        // 异步结束后,手动执行dispatch
        dispatch({ type: 'add', payload: num })
    }, 1000)
}
```

```jsx
// ReactReduxTest.js
@connect(
    state => ({ num: state }), // 状态映射
    {
        add,
        minus,
        asyncAdd
    }
)
class ReactReduxTest extends Component {
    render() {
        return (
            <div>
                { this.props.num }
                <button onClick={ () => this.props.add() }> + </button>
                <button onClick={ () => this.props.minus() }> - </button>
                <button onClick={ () => this.props.asyncAdd() }> + </button>
            </div>
        )
    }
}
```

2. 将reducer从store中抽离到index.js中

```javascript
// store/counter.js
export const counterReducer = (state, action) => {
    const num = action.payload || 1
    switch (action.type) {
        case 'add':
            return state + num
        case 'minus':
            return state - num
        default:
            // 初始化state值
            return 0
    }
}
// store/index.js
import { createStore, applyMiddleware } from 'redux'
import logger from 'redux-logger'
import thunk from 'redux-thunk'
import { counterReducer } from './counter'

const store = createStore(counterReducer, applyMiddleware(logger, thunk))
export default store
```

3. 模块化

通过`combineReducers`使reducer模块化

```javascript
// store/couter.js
import { createStore, applyMiddleware, combineReducers } from 'redux'
import logger from 'redux-logger'
import thunk from 'redux-thunk'
import { counterReducer } from './counter'

const store = createStore(combineReducers({ counter: counterReducer }), applyMiddleware(logger, thunk))
```

```jsx
// ReactReduxTest.js
@connect(
    // state => ({ num: state }), // 状态映射
    state => ({ num: state.counter }), // 模块化
    {
        add,
        minus,
        asyncAdd
    }
```

## Redux原理

### 核心功能实现

- 存储状态state
- 获取状态getState
- 更新状态dispatch
- 变更订阅subscribe

```javascript
// my-redux/index.js
export function createStore (reducer, enhancer ) {
    // 存在中间件，包装后返回
    if (enhancer) {
        return enhancer(createStore)(reducer)
    }

    let currentState = undefined
    let listeners = []

    function getState() {
        return currentState
    }

    function dispatch(action) {
        currentState = reducer(currentState, action)
        listeners.forEach(listener => {
            listener()
        })
    }

    function subscribe(listener) {
        listeners.push(listener)
    }
    // 初始化currentState
    dispatch({ type: '@my-redux init' })

    return { getState, dispatch, subscribe }
}
```

```javascript
// store.js
import { createStore } from './index'

const couterReducer = ( state = 0, action ) => {
    switch (action.type) {
        case 'add':
            return state + 1
        case 'minus':
            return state -1
        default:
            return 0
    }
}

const store = createStore(couterReducer)

export default store
```

```jsx
// MyRedux.js
import React, { Component } from 'react'
import store from './store'

export default class MyReduxTest extends Component {

    componentDidMount() {
        store.subscribe(() => this.forceUpdate())
    }

    render() {
        return (
            <div>
                { store.getState() }
                <button onClick = { () => store.dispatch({ type: 'add' })}> + </button>
                <button onClick = { () => store.dispatch({ type: 'minus' })}> - </button>
            </div>
        )
    }
}
```



### 中间件实现

dispatch调用时会先按一定顺序执行一系列的中间件再真正执行dispatch, 其实就是加强dispatch功能

#### 自定义中间件的编写

中间件入参可接收`getState`和`dispatch`

中间件返回中间件任务执行函数：`dispatch => action => dispatch(action)`

```javascript
// logger.js
export default function(() => {
	return dispatch => action => {
		console.log(action.type + ' 执行了！！！')
		return dispatch(action)
	}
})
```

#### 实现原理

利用高阶函数的特性、

##### compose的实现

利用数组的聚合函数reduce，将多个函数聚合成一个函数后返回

```javascript
function compose(...funcs) {
  if (funcs.length === 0) {
    // 返回一个无意义函数
    return arg => arg
  }
  if (funcs.length === 1) {
    return funcs[0]
  }
  // [func1, func2] => func2(func1(args))
  return funcs.reduce((left, right) => (...args) => right(left(...args)))
}
```

##### applyMiddleware的实现

应该返回一个高阶函数，起强化dispatch作用

```javascript
export function applyMiddleware(...middlewares) {
  return createStore => (...args) => {
    // 原本的createStore功能
    const store = createStore(...args)
    // middleware的入参，有些中间件可能需要状态state
    const midArgs = {
      dispatch = (...args) => store.dispatch,
      getState = store.getState
    }
  	// middleware的执行链(数组), [fn1, fn2] => [fn1(midApi), fn2(midApi)]
  	const middlewareChain = middlewares.map(middleware => middleware(midArgs))
    // 聚合操作,强化dispatch，让它可以按按顺序执行中间件函数
  	const dispatch = compose(...middlewareChain)(store.dispatch)
    // 返回原本的store和增强后的dispatch
    return {
      ...store,
      dispatch
    }
  }
}
```

### redux-thunk原理

判断action是否是函数，如果是函数则执行函数否则跳过

```javascript
export function thunk({ getState }) {
    return dispatch => action => {
        if (typeof(action) === 'function') {
            return action(dispatch, getState)
        }
        return dispatch(action)
    }
}
```

```jsx
// store.js
const store = createStore(counterReducer, applyMiddleware(logger,thunk));
// MyReduxTest.js
<button onClick={() => store.dispatch(() => {
    setTimeout(() => {
        store.dispatch({ type: "add" })
    }, 1000);
})}>+</button>
```

## react-redux原理

### 核心任务

- 实现一个高阶函数工厂connect，可以根据传入的状态映射规则函数和派发器映射规则函数映射需要的属性
- 可以处理变更检查和刷新任务
- 实现一个Provider组件可以传递store

### Provider组件的实现

```javascript
export class Provider extends Component {
    // 是为context中的字段设置类型检查，必须设置
    static childContextTypes = {
        store: PropTypes.object
    }

    // 用来设置组件的context
    getChildContext() {
        return {
            store: this.store
        }
    }

    constructor(props, context) {
        super(props, context)
        this.store = props.store
    }

    render() {
        return this.props.children
    }
}
```

### connect装饰器的实现

```javascript
function bindActionCreator(creator, dispatch) {
    return (...args) => dispatch(creator(...args))
}

function bindActionCreators(creators, dispatch) {
    return Object.keys(creators).reduce((ret, item) => {
        ret[item] = bindActionCreator(creators[item],dispatch)
        return ret
    }, {})
}

export function connect(mapStateToProps = state => state, mapDispatchToProps = {}) {
    return WrapComponent => class ConnectComponent extends Component {
        static contextTypes = {
            store: PropTypes.object
        }

        constructor(props, context) {
            super(props, context)
            this.state = {
                props: {}
            }
        }
        componentDidMount() {
            const { store } = this.context
            store.subscribe(() => this.update())
            this.update()
        }

        update() {
            const { store } = this.context
            const stateProps = mapStateToProps(store.getState())
            const dispatchProps = bindActionCreators(mapDispatchToProps, store.dispatch)

            this.setState({
                props: {
                    ...this.state.props,
                    ...stateProps,
                    ...dispatchProps
                }
            })
        }
        render() {
            return <WrapComponent {...this.state.props}></WrapComponent>
        }
    }
}
```

