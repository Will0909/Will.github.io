---
title: React组件
tags: react
categories:
- react
- react基础
---

React中包括函数组件和Class组件

### 函数组件

函数组件接收唯一带有数据的 “props”（代表属性）对象与并返回一个 React 元素。

函数组件通常**无状态**，仅关注内容展示，返回渲染结果即可

```js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

###Class组件

同时还可以使用 ES6 的**class**来定义组件，class组件通常拥有**状态**和**生命周期**，继承于Component，实现**render**方法

```js
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

####渲染组件

React 元素也可以是用户自定义的组件，当 React 元素为用户自定义组件时，它会将 JSX 所接收的**属性**（attributes）转换为单个对象传递给组件，这个对象被称之为 “props”。

```js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

**注意：组件名称必须以大写字母开头**

#### props 的只读性

无论是函数组件还是Class组件都不可以修改自身的props，**所有 React 组件都必须像纯函数一样保护它们的 props 不被更改**。

**纯函数**：不会尝试更改入参，且多次调用下相同的入参始终返回相同的结果。

###组件中的状态管理

#### 类组件中的状态管理



#### 函数组件中的状态管理

**Hook**是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。详见：[Hook](./Hook.md)

### 组件间通讯

#### props传递

Props属性传递可用于父子组件相互通信

如果父组件传递的是**函数**，则可以把**子组件信息传入父组件**，这个常称为**状态提升**

#### Context

跨层级组件之间通信，详见：[Context](./Context.md)

#### Redux

类似vuex，无明显关系的组件间通信，详见：[Redux](./Redux.md)