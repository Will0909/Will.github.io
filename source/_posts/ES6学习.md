---
title: es6学习
tags: es6
categories: 前端
---
# 简介
## ECMAScript和JavaScript
ECMAScript 和 JavaScript 的关系是，前者是后者的规格，后者是前者的一种实现（另外的 ECMAScript 方言还有 Jscript 和 ActionScript).
## ECMAScript2015和ECMAScript6
ES6 既是一个历史名词，也是一个泛指，含义是5.1版以后的 JavaScript 的下一代标准，涵盖了ES2015、ES2016、ES2017等等，而ES2015则是正式名称，特指该年发布的正式版本的语言标准。
## 部署进度
各大浏览器的最新版本，对 ES6 的支持可以查看<http://kangax.github.io/es5-compat-table/es6>。  
访问<http://ruanyf.github.io/es-checker>，可以看到您的浏览器支持 ES6 的程度。 
## ES6 => ES5
如果想用ES6方式编程，而又担心现有环境不支持，可以将ES6代码转换成ES5代码。
<pre>
// 转码前
input.map(item => item + 1);
// 转码后
input.map(function (item) {
  return item + 1;
});
</pre>
大致有如下方式：

- 利用转码器，如：Babel，Traceur
- 在线转码，Babel提供的[在线编译器](https://babeljs.io/repl/),Traceur 提供的[在线编译器](http://google.github.io/traceur-compiler/demo/repl.html)

# let命令
let命令用来声明变量，用法类似var，但只在所在的代码块内有效。详细见[案例](kata/let.js)  

# const命令
- const声明一个只读的常量。一旦声明，常量的值就不能改变。
- const一旦声明变量，就必须立即初始化，不能留到以后赋值。
- const的作用域与let命令相同：只在声明所在的块级作用域内有效。
- const命令声明的常量也是不提升，同样存在暂时性死区，只能在声明的位置后面使用。
- const声明的常量，也与let一样不可重复声明
- const实际上保存的，并不是变量的值不得改动，而是变量指向的那个内存地址不得改动。

<pre>
const foo = {};
// 为 foo 添加一个属性，可以成功
foo.prop = 123;
foo.prop // 123
// 将 foo 指向另一个对象，就会报错
foo = {}; // TypeError: "foo" is read-only
//常量foo储存的是一个地址，这个地址指向一个对象;
//不可变的只是这个地址，即不能把foo指向另一个地址;
//但对象本身是可变的，所以依然可以为其添加新属性。
</pre>

# global对象
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




# 变量的解析赋值
ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构（Destructuring）。
比如：
<pre>
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3
</pre>  
解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于undefined和null无法转为对象，所以对它们进行解构赋值，都会报错。  
本质上，这种写法属于“模式匹配”，只要等号两边的模式相同，左边的变量就会被赋予对应的值。  
如果解构不成功，变量的值就等于undefined。
可以进行数组、对象、字符串、数值、布尔值、函数等的解析，具体看[案例](kata/destructuring.js)
# 字符串/正则/数值/数组/函数/对象的扩展
这部分建议直接看案例  
[字符串的扩展](kata/extend/string.js)  
[正则的扩展](kata/extend/regex.js)  
[数值的扩展](kata/extend/number.js)  
[数组的扩展](kata/extend/array.js)  
[函数的扩展](kata/extend/function.js)  
[对象的扩展](kata/extend/object.js)

# Symbol
ES6 引入了一种新的原始数据类型Symbol，表示独一无二的值，Symbol 值通过Symbol函数生成, 可传参数，具体参见[案例](kata/symbol.js)

# Set和Map数据结构
ES6 提供了新的数据结构 Set，它类似于数组，但是成员的值都是唯一的，没有重复的值,具体看[案例](kata/set.js)  
ES6 提供了 Map 数据结构，它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键，具体看[案例](kata/map.js)
