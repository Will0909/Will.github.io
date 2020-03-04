---
title: React核心基础
tags: react
categories: 
- react
- react基础
---

## create-react-app的使用

1. 安装官方脚手架: `npm install -g create-react-app `
2. 创建项目: `create-react-app react-study`
3. 启动项目: `npm start `

### 文件结构

项目初始创建后的文件结构如下：

```javascript
├── README.md	文档					
├── public	静态资源
│   ├── favicon.ico							
│   ├── index.html
│   └── manifest.json
└── src	源码
    ├── App.css
    ├── App.js	根组件
    ├── App.test.js
    ├── index.css	全局样式
    ├── index.js	入口文件
    ├── logo.svg
    └── serviceWorker.js	pwa支持
├── package.json	npm依赖
```

### 项目详细内容

通过`npm run eject`查看项目的详细内容，此操作为不可逆操作，执行后不可恢复。

打开后多出两个目录，`config`和`scripts`

```javascript
├── config
  ├── env.js 处理.env环境变量配置文件
  ├── paths.js 提供各种路径
  ├── webpack.config.js webpack配置文件
  └── webpackDevServer.config.js 测试服务器配置文件
└── scripts 启动、打包和测试脚本 
  ├── build.js 打包脚本、
  ├── start.js 启动脚本
  └── test.js 测试脚本
```

#### env.js

env.js用于处理.env环境变量配置文件, 例如可以通过新建.env文件来配置相关

```javascript
// .env
PORT=8888 // 修改默认配置端口
```

#### webpack.config.js

此文件是Webpack的配置文件

## React, ReactDOM

- React负责逻辑控制，数据 => VDOM, 使用jsx来描述UI

- ReactDOM负责渲染实际的DOM, VDOM => DOM, 通过不同的渲染库可以实现跨平台，如：移动端通过移动端的渲染库

## JSX

### 什么是JSX？

JSX是JavaScript的一种语法扩展，格式像一种模板语言，但是事实上完全是JavaScript内部实现的

JSX可以很好的描述UI，有效的提高开发效率

### JSX的使用

#### 表达式{}的使用

```jsx
const name = 'Will'
const jsx = <h2>{ name }</h2>
```

#### 函数也是合法表达式

```jsx
const user = {
  firstName: 'Weicai',
  lastName: 'Huang'
}
function formatName(user) {
  return user.firstName + ' ' + user.lastName
}
const jsx = <h2>{ formatName(user) }</h2>
```

#### JSX也是合法表达式

```jsx
const greet = <p>hello JSX</p>
const jsx = <h2>{ greet }</h2>
```

#### JSX表示对象

Babel 会把 JSX 转译成一个名为 **`React.createElement()`** 函数调用。

以下两种示例代码完全等效：

```js
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

`React.createElement()` 会预先执行一些检查，以帮助你编写无错代码，但实际上它创建了一个这样的对象：

```js
// 注意：这是简化过的结构
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
```

这些对象被称为 “React 元素”。它们描述了你希望在屏幕上看到的内容。React 通过读取这些对象，然后使用它们来构建 DOM 以及保持随时更新。

#### 条件语句的使用

```jsx
const title = name ? <h2>{ name }</h2> : null
```

#### 数组的使用

数组会被当做一组子元素，例如数组中存放一组jsx则可以用于显示列表数据

```jsx
const fruits = ['apple', 'orange', 'banana'].map(fruit => <li key={index}>{ fruit }</li>)
                             
const jsx = <div><ul>{ fruits }</ul></div>                                              
```

#### 属性的使用

静态值用双引号，动态值用花括号

JSX 实际上是JS，对象语法上更接近 **JavaScript** 而不是 HTML，所以 React DOM 使用 `camelCase`（小驼峰命名）来定义属性的名称，而不使用 HTML 属性名称的命名约定, 如: class要变成className。(class为保留字)

```jsx
const jsx = <div style={{ width: 100px }} className="title"></div>
```

#### css模块化

css的模块化引入，可以将css文件名改成带`module`，如: `index.module.css`

```jsx
import style from 'index.module.css'
const jsx = <div className={ style.img }></div>
```

## 元素的渲染

通过**ReactDOM.render**渲染

```js
const element = <h1>Hello, world</h1>;
ReactDOM.render(element, document.getElementById('root'));
```

### 更新已渲染元素

React 元素是**不可变对象**。一旦被创建，你就无法更改它的子元素或者属性,更新 UI **唯一**的方式是创建一个全新的元素，并将其传入 `ReactDOM.render()`。

**React只更新它需要更新的部分**，React DOM 会将元素和它的子元素与它们之前的状态进行比较，并只会进行必要的更新来使 DOM 达到预期的状态。

## React组件

React组件分成类组件和函数组件，**注意：组件名称必须以大写字母开头**

### 类组件

同时还可以使用 ES6 的**class**来定义组件，类组件必须继承于`Component`，实现`render`函数, 类组件拥有**状态**，有**生命周期**

```jsx
export default class test extends Component {
  render() {
    const name = 'Will'
    return <h2> { name } </h2>
  }
}
```

### 函数组件

函数组件接收唯一带有数据的 “props”（代表属性）对象与并返回一个 React 元素。

函数组件**无状态无生命周期，注重内容的展示**，直接返回渲染结果即可

```jsx
export default function test(props) {
	const name = 'Will'
  return <h2> { name } </h2>
}
```

### 组件状态管理

#### 类组件状态管理

使用`state`属性维护，可以在构造函数中初始化状态，使用`setState`方法更新状态

`state`和`props`类似，但是`state`是私有的，并且完全受控于当前控件。

```jsx
import React, { Component } from "react"

class Clock extends Component {
  constructor(props) {
    this.state = {
      date: new Date()
    }
  }
  
  componentDidMount() {
    this.timerID = setInternal(() => {
      this.setState({
        date: new Date()
      })
    }, 1000)
  }
  
  componentWillUnMount() {
    // 注意组件卸载时应该停止timerID
    clearInternal(this.timerID)
  }
  
  render() {
    return <div>{this.state.date.toLocalTimeString()}</div>
  }
}
```

##### setState特性

- state状态不能直接更改，要通过setState更改，构造函数中是唯一可以直接给state赋值的地方

- setState是批量执行的，连续对同一个状态执行状态更新，只会生效一次

- setState通常是异步的，要执行最新状态值，可以传递函数给setState方法，函数传递state，也可以传入props值

  1. ```javascript
     this.setState((state, props) => ({
       couter: state.couter + 1
     })
     ```

#### 函数组件中状态管理

函数组件中的状态可以通过引入react中的`useState`和`useEffect`来管理

- useState可以用来创建一个状态和一个修改状态的函数

- useEffect可以用于编写副作用, 第一个参数传递函数，可以用来做一些副作用比如异步请求等行为，而第二个参数是个数组，如果数组中的值才会触发 useEffect 第一个参数中的函数。如果有返回值则在组件销毁或者调用函数前调用，可做一些类似清除定时器等操作。

```jsx
import { useState, useEffect } from "react"

export default funciton ClockFunc() {
  // 通过useState来创建一个状态和一个修改状态的函数
  const [date, setDate] = useState(new Date())
  useEffect(() => {
    const timerId = setInternal(() => {
      setDate(new Date())
    })
    // 返回清理函数
    return () => clearInternal(timerId)
  })
  return <div>{this.state.date.toLocalTimeString()}</div>
}
```

### 事件处理

react中通过onXXX写法来监听事件，注意此时的this指向可以通过三种方式：

1. 在构造函数中通过binder直接指定this
2. 函数直接使用箭头函数写法
3. 调用函数时通过箭头符号调用

```jsx
import React, { Component } from "react"

export default class EventHandle extends Component {
	constructor(props) {
    this.state = {
      name: ''
    }
    // 通过binder直接绑定this指向
    this.handelChange = this.handleChange.binder(this)
  }
  
  //handleChange(e) {
  //  this.setState({
  //    name: e.target.value
  //  })
  //}
  
  // 使用箭头函数明确this指向
  handleChange = e => {
    this.setState({
      name: e.target.value
    })
  }
  
  render() {
    return (
      <div>
        {/* 使用箭头函数，不需要指定回调函数this，且便于传递参数 */}
        {/* <input
         type = "text"
         value = { this.state.name }
         onChange= e => this.handleChange(e)
        /> */}
        
        <input
         type = "text"
         value = { this.state.name }
         onChange={this.handleChange}
        />
        <p>{ this.state.name }</p>
      </div>)
  }
} 
```
