---
title: es6简介
tags: es6
categories: 
- es6
---
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

