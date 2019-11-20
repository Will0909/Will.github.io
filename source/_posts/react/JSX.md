---
title: JSX
tags: react
categories:
- react
- react基础
---

### JSX

JSX是一个JavaScript的语法扩展， JSX可以很好地描述UI，能够有效提高开发效率。 

#### JSX的使用

你可以通过使用引号，来将属性值指定为字符串字面量：

```js
const element = <div tabIndex="0"></div>;
```

也可以使用大括号，来在属性值中插入一个 JavaScript 表达式：

```js
const element = <img src={user.avatarUrl}></img>;
```

#### 在JSX中嵌入表达式

```js
const name = 'Josh Perez';
const element = <h1>Hello, {name}</h1>;
```

##### 函数也是表达式

```js
const formatName = (user) => {
  return user.firstName + ' ' + user.lastName
}
const jsx = <h2>{ formatName(user) }</h2>
```

##### JSX也是表达式

JSX实际上也是一个js对象

```js
const jsx1 = <p>jsx也是表达式</p>
const jsx = <h1>表达式{ jsx }</h1>
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

#### 条件语句

```js
const isShow = true
const name = 'Tom'
const title = isShow ? <h3>{ name }</h3> : ''
```

#### 数组在JSX中的使用

数组会被作为一组子元素对待，数组中存放一组jsx可用于显示列表数据 

```js
const arr = [1,2,3].map(num => <li key={num}>{num}</li>)
const jsx = (
<div>
    <ul>{arr}</ul>
  </div>
);
```

#### 属性的使用

静态值用双括号，动态值用单括号

JSX 实际上是JS，对象语法上更接近 **JavaScript** 而不是 HTML，所以 React DOM 使用 `camelCase`（小驼峰命名）来定义属性的名称，而不使用 HTML 属性名称的命名约定, 如: class要变成className。(class为保留字)

```js
import logo from "./logo.svg";
const jsx = (
  <div>
    <img src={logo} style={{ width: 100 }} className="img" />
  </div>
)
```

####CSS模块化

```js
import style from "./index.module.css";
<img className={style.img} />
```

#### 元素的渲染

通过**ReactDOM.render**渲染

```js
const element = <h1>Hello, world</h1>;
ReactDOM.render(element, document.getElementById('root'));
```

####更新已渲染元素

React 元素是**不可变对象**。一旦被创建，你就无法更改它的子元素或者属性,更新 UI **唯一**的方式是创建一个全新的元素，并将其传入 `ReactDOM.render()`。

**React只更新它需要更新的部分**，React DOM 会将元素和它的子元素与它们之前的状态进行比较，并只会进行必要的更新来使 DOM 达到预期的状态。