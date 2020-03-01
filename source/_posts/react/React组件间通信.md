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

跨层级间通讯

## Redux

无明显关系的组件间通讯