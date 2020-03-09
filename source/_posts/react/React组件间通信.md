---
title: React组件间通信
tags: react
categories: 
- react
- react基础
---

## 组件通讯

### 通过`props`属性传值

props属性可以用于父子组件间的通信，父组件会将 JSX 所接收的**属性**（attributes）转换为单个对象传递给子组件，这个对象被称之为 “props”。

无论是函数组件还是Class组件都不可以修改自身的props，**所有 React 组件都必须像纯函数一样保护它们的 props 不被更改**。

**纯函数**：不会尝试更改入参，且多次调用下相同的入参始终返回相同的结果。

#### 父 => 子

直接在父组件中通过属性传递

```jsx
// Test2.jsx
<Test1 title= 'props组件通讯'></Test1>
// Test1.jsx
<h2>{ this.props.title></h2>
```

#### 子 => 父

父组件传入函数组件，子组件通过此函数属性将信息传入父组件中，这也称为**状态提升**

```jsx
// stateManager.jsx
<Clock change={ this.change }></Clock>

// Clock.jsx
this.timerID = setInterval(() => {
    this.setState({
        date: new Date()
    }, ()=>{
// 每次状态更新就通知父组件
        this.props.change(this.state.date);
    });
}, 1000);
```



## context

 Context 提供了一个无需为每层组件手动添加 props，就能在组件树间进行数据传递的方法。目的是为了共享一些类似于全局的数据，如：当前用户，主题，语言等。

### 如何使用？

1. 通过**`React.createContext`**创建Context对象

   ```js
   const myContext = React.createContext(defaultValue)
   ```

   **注意**：当没有匹配到Provider时，`defaultValue`才会生效，传递undefined给Provider也并不会生效。

2. **`Context.Provider`**接收value属性，传递给Consumer组件

   ```js
   <MyContext.Provider value={/* 某个值 */}>
   ```

   - 一个Provider组件可以对应多个Consumer组件，也可以嵌套使用。
   - 当value值变化时，它内部的所有Consumer组件都会重新渲染。
   - Provider及其内部的Consumer组件都不受限于`shouldComponentUpdate`函数，因此consumer组件在其祖先组件退出更新的情况下也能更新。

3. **`Context.consumer`**可以订阅到context的变更。

   ```js
   <MyContext.Consumer>
     {value => /* 基于 context 值进行渲染*/}
   </MyContext.Consumer>
   ```

   这个函数接收当前的 context 值，返回一个 React 节点。传递给函数的 `value` 值等同于往上组件树离这个 context 最近的 Provider 提供的 `value` 值。如果没有对应的 Provider，`value` 参数等同于传递给 `createContext()` 的 `defaultValue`。

### 案例

```jsx
import React, { Component } from 'react'

const Context = React.createContext()
const Provider = Context.Provider
const Consumer = Context.Consumer

function Child(props) {
return <div onClick={ () => props.add() }>{ props.counter }</div>
}

export default class ContextTest extends Component {
    constructor(props) {
        super(props)
        this.state = {
            counter: 0
        }
    }

    add = () => {
        this.setState(state => ({
            counter: state.counter + 1
        }))
    }

    render() {
        return (
            <div>
                <Provider value={ 
                    {
                        add: this.add,
                        counter: this.state.counter
                    }
                }>
                    {/* Consumer中内嵌函数，其参数是传递的数据，返回要渲染的组件 */}
                    {/* 把value展开传递给Child */}
                    <Consumer>{value => <Child { ...value }></Child>}</Consumer>
                    <Consumer>{value => <Child { ...value }></Child>}</Consumer>
                </Provider>
            </div>
        )
    }
}
```



**Class.contextType**

 挂载在 class 上的 `contextType` 属性会被重赋值为一个由 `React.createContext()` 创建的 Context 对象。这能让你使用 `this.context` 来消费最近 Context 上的那个值。你可以在任何生命周期中访问到它，包括 render 函数中。 

```jsx
class MyClass extends React.Component {
  // 静态写法
  // static contextType = MyContext;  
  componentDidMount() {
    let value = this.context;
    /* 在组件挂载完成后，使用 MyContext 组件的值来执行一些有副作用的操作 */
  }
  componentDidUpdate() {
    let value = this.context;
    /* ... */
  }
  componentWillUnmount() {
    let value = this.context;
    /* ... */
  }
  render() {
    let value = this.context;
    /* 基于 MyContext 组件的值进行渲染 */
  }
}
MyClass.contextType = MyContext;
```



## Redux

无明显关系的组件间通讯，具体见Redux的使用