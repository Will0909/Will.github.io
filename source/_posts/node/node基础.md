---
title:node基础
tags: node
categories:
- node
- node基础
---

### 什么是Node.JS

node.js是一个异步的事件驱动的JavaScript运行时

node.js的特性其实就是js的特性:

- 异步I/O

- 事件驱动

node历史 - 为性能而生

#### 并发处理

- 多进程 - C Apache
- 多线程 - Java
- 异步IO - js
- 协程 - lua openresty go deno-go TS

#### 和前端的异同

- js核心语法不变
- 前端是BOM DOM
- 后端 fs http buffer event ps

#### 运行程序

如执行run.js, 直接node run.js即可

但是，每次修改js文件都需要重新执行命令，所以可以通过安装**nodemon**来监视文件改动，自动重启

#### 调试node程序

Debug - Start Debugging

### 模块

- node内建模块，Node提供的模块称为**核心模块**
- 第三方模块，由用户编写的模块称为**文件模块**
- CommomJS, AMD, CMD,UMD

### 核心API

- fs - 文件系统
- http - 用于创建web服务的模块
- Buffer

