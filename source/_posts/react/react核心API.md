---
title: react核心API
tags: react
categories:
- react
- react基础
---

### JSX

JSX是一个JavaScript的语法扩展， 在 JavaScript 代码中将 JSX 和 UI 放在一起时，会在视觉上有辅助作用。它还可以使 React 显示更多有用的错误和警告消息。 

#### 在JSX中嵌入表达式

```js
const name = 'Josh Perez';
const element = <h1>Hello, {name}</h1>;

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

