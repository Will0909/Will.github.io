---
title: global对象
tags: es6
categories: 
- es6


---

- 顶层对象，在浏览器环境指的是window对象，在Node指的是global对象。

- ES5的顶层对象，本身也是一个问题，因为它在各种实现里面是不统一的，(比如：1.览器里面，顶层对象是window，但Node没有window,;2.浏览器和 Web Worker 里面，self也指向顶层对象，但是Node没有self3:Node 里面，顶层对象是global，但其他环境都不支持)

- es6之前很难找到一种方法，可以在所有情况下，都取到顶层对象。

- 现在有一个提案，在语言标准的层面，引入global作为顶层对象。在所有环境下，都可以从它拿到顶层对象。

- <pre>
  // CommonJS的写法
  var global = require('system.global')();
  // ES6模块的写法
  import getGlobal from 'system.global';
  const global = getGlobal();
  </pre>

- ES5之中，顶层对象的属性与全局变量是等价的。

- 从ES6开始，全局变量将逐步与顶层对象的属性脱钩，一方面规定，为了保持兼容性，var命令和function命令声明的全局变量，依旧是顶层对象的属性；另一方面规定，let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性。