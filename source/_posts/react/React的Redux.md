---
title: React的Redux
tags: react
categories: 
- react
- react进阶
---

## redux

redux是JavaScript状态容器，提供可预测化的状态管理，本身是一个独立的库，不单单应用于React中, 使用redux可以使状态从组件中脱离出来单独管理。

### redux工作流

![image-20200303224717137](./images/image-20200303224717137.png)

1. 需要一个**store**来存储数据，可以通过`createStore`创建

1. 创建时传入自定义的**reducer**，reducer用于初始化state并定义state修改规则
2. 通过`dispatch`一个action来提交对数据的修改
3. action提交到Reducer后，Reducer根据action的type返回**全新的state**，并不直接修改state
4. 获取state通过`getState`方法
5. 通过`store.subcribe`订阅状态变更，手动去重新渲染页面

举例如下：

```js
//store.js
import { createStore } from 'redux'

const counterReducer = (state, action) => {
    switch (action.type) {
        case 'plus':
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
import store from './store'

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
                <button onClick={() => store.dispatch({ type: 'plus'})}> + </button>
                <button onClick={() => store.dispatch({ type: 'minus'})}> - </button>
            </div>
        )
    }
}
```

## react-redux

react的使用比较繁琐：

1. 每次都需要订阅变更状态去手动重新渲染页面
2. 每次都需要通过getState才能获取最新state值
3. 每个组件都需要引入store

所以，我们需要引入react-redux

