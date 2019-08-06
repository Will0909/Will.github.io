---
title: vue各版本区别
tags: vue
categories: 
- vue
- vue源码解析
---
在NPM包的dist/中有很多8各不同的vue.js的构建版本，主要区别如下图：

 | UMD | CommomJS | ES Modle
:-: | :-: | :-: | :-:
完整版 | vue.js | vue.common.js | vue.esm.js
只包含运行时版 | vue.runtime.js | vue.runtime.common.js | vue.runtime.esm.js 
完整版(生产环境) | vue.min.js | - | - 
只包含运行时版生产环境) | vue.runtime.min.js | - | - 

主要从以下方面理解这8个版本

- 根据是否需要编译器：运行时版本和完整版

  - 完整版本：包括编译器和运行时的版本
- 编译器：vue里用的<template></template>语法是需要被编译的
  - 
    运行时: 用来创建Vue实例、渲染、处理虚拟Dom,可以理解为除了编译器剩下的代码都属于运行时
- 根据引入方式：UMD/CommonJS/ES Modle
  - 当你通过script标签来引用vue源码时,用UMD版本
  - 当你通过低版本的打包工具,比如webpack1,用CommonJS版本
  - 当你通过现代打包工具比如 webpack 2 或 Rollup,用ES Module版本

- 根据使用环境是否为生产环境选择

  vue源码会根据process.env.NODE_ENV来判断是用生产还是开发环境的代码，webpack里可以有自带的 new webpack.DefinePlugin()来设置process.env.NODE_ENV，大致如下：

  ```js
   new webpack.DefinePlugin({
        'process.env': env
      }),
  ```

**注意：** 使用.vue文件情况下可直接使用运行时版本，因为vue-loader会将.vue文件内部预编译成JS，所以在最终打好的包中是不需要编译器的，此时使用运行时版本，体积大概会比完整版小30%左右

