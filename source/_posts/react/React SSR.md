---
title: React SSR
tags: react
categories:
- react
- react进阶
---

## 同构应用架构

![image-20200223165432177](./images/image-20200223165432177.png)

1. Webpack将代码（component，store，router等）的入口(app.js)打包成Server bundle和Client bundle
2. Server bundle负责返回首页HTML（render html），此时不带js代码，只用于页面渲染
3. Client bundle负责处理逻辑的js代码(如负责事件点击)，**Hydrate(注入)**到服务端返回的html中

> react ssr同vue ssr有个区别是node本身不支持JSX，需要babel-loader的支持

## 实战 - 手写一个React SSR

### 新建工程

```javascript
mkdir react-ssr
npm init -y //初始化项目，加-y可以跳过需要所有确定步骤
```

### SSR编译环境搭建

#### 服务端环境

1. 新建server/index.js用于服务端代码的编写

2. 新建webpack.server.js

```javascript
const path = require('path')
// node环境中防止import时把node_modules引入编译
const nodeExternals = require('webpack-node-externals') 

// 服务端webpack
module.exports = {
    target: 'node', // 默认是web
    mode: 'development',
    entry: './server/index.js',
    externals: [nodeExternals()],
    output: {
        // 用patch.resolve处理成绝对路径
        path: path.resolve(__dirname, 'build'), 
        filename: 'bundle.js'
    },
    // babel
    module: {
        rules: [
            {
                test: /\.js$/,
                loader: 'babel-loader',
                exclude: /node_modules/,
                // 编译级别，@babel/preset-react支持JSX， @babel/preset-env支持ES6
                options: {
                    presets:['@babel/preset-react', ['@babel/preset-env']]
                }
            }
        ]
    }
}
```

### 客户端环境

1. 新建client/index.js用于客户端代码的编写
2. 新建webpack.client.js

```javascript
const path = require('path')

// 客户端webpack
module.exports = {
    mode: 'development',
    entry: './client/index.js',
    output: {
        path: path.resolve(__dirname, 'public'),
        filename: 'bundle.js'
    },
    // babel
    module: {
        rules: [
            {
                test: /\.js$/,
                loader: 'babel-loader',
                exclude: /node_modules/,
                // 编译级别
                options: {
                    presets:['@babel/preset-react', ['@babel/preset-env']]
                }
            }
        ]
    }
}
```

#### 安装依赖

`npm install webpack webpack-cli webpack-node-externals @babel/core @babel/preset-env  concurrently`

#### 增加执行脚本命令

```javascript
  "scripts": {
    "dev:server": "webpack --config webpack.server.js --watch",
    "dev:client": "webpack --config webpack.client.js --watch",
    "dev:start": "nodemon --watch build --exec node \"./build/bundle.js\"",
    "start": "concurrently \"npm run dev:client\" \"npm run dev:server\" \"npm run dev:start\""      
  },
```

- nodemon默认监听项目目录内的所有文件变动
- --watch 监听哪些文件的变化，当变化的时候自动重启
- --exec 配置运行命令
- 使用`concurrently`来同时跑三条命令


### 基础代码实现

#### 统一的入口组件(App)

```javascript
// App.js
import React, { useState } from 'react'

function App(props) {
	const [count, setCount] = useState(0)
    return (
        <div>
            <h1>Hi, { props.title } !  {count}</h1>
            <button onClick={() => setCount(count + 1)}>增加</button>
        </div>
    )
}

export default <App title="react ssr"></App>
```

#### 服务端基础代码实现

使用express搭建服务端

- 通过renderTostring转换react代码
- 定义静态路径
- 拼接字符串模板
- 将renderTostring放入字符串模板中
- 返回html字符串
- 监听端口

```javascript
// server/index.js
import React from 'react'
import { renderToString } from 'react-dom/server'
import express from 'express'
import App from '../src/App.js'

const app = express()
// 定义静态路径
app.use(express.static('public'))

app.get('/', (req, res) => {
    const content = renderToString(App)
    // 字符串模板拼接
    res.send(`
        <html>
            <head>
                <meta charset="utf-8" />
                <title>react ssr</title>
            </head>
            <body>
                <div id='root'>
                    ${content}
                </div>
            </body>
            <script src='/bundle.js'></script>
        </html>
    `)
})

app.listen(9090, () => console.log('监听完毕'))
```

**此时服务器返回html是不带js代码，需要通过客户端的注入**

#### 客户端基础代码实现

通过`react-dom`的hydrate注入，此时不用render

```javascript
import App from '../src/App'
import ReactDom from 'react-dom'

// 注水, 客户端入口
ReactDom.hydrate(App, document.getElementById('root'))
```

### 路由支持

- 安装依赖：

  `npm install react-router-dom -D`

- 添加两个组件Index和About

```jsx
import React, { useState } from 'react'

function Index(props) {
    const [count, setCount] = useState(0)
    return (
        <div>
            <h1>Hi, { props.title } !  {count}</h1>
            <button onClick={() => setCount(count + 1)}>增加</button>
        </div>
    )
}

export default Index
```

```jsx
import React from 'react'

function About(props) {
    return (
        <div>
            <h1>关于页面</h1>
        </div>
    )
}

export default About
```

- 改成App.js

```jsx
import React from 'react'
import { Route } from 'react-router-dom'
import Index from './container/Index'
import About from './container/About'


export default (
    <div>
        <Route path='/' exact component={Index}></Route>
        <Route path='/about' exact component={About}></Route>
    </div>
)
```

- 客户端用BrowserRouter，路由的跳转直接在js代码中执行即可

```javascript
import React from 'react'
import ReactDom from 'react-dom'
import App from '../src/App'
import { BrowserRouter } from 'react-router-dom'

// 注水, 客户端入口
const Page = <BrowserRouter>{App}</BrowserRouter>

ReactDom.hydrate(Page, document.getElementById('root'))
```

- 服务端用StaticRouter, 传入req.url用于跳转路由

```js
import React from 'react'
import { renderToString } from 'react-dom/server'
import express from 'express'
import App from '../src/App.js'
import { StaticRouter } from 'react-router-dom'

const app = express()
// 定义静态路径
app.use(express.static('public'))

app.get('*', (req, res) => {
    const content = renderToString(
        <StaticRouter location={req.url}>
            {App}
        </StaticRouter>
    )
    // 字符串模板拼接
    res.send(`
        <html>
            <head>
                <meta charset="utf-8" />
                <title>react ssr</title>
            </head>
            <body>
                <div id='root'>
                    ${content}
                </div>
            </body>
            <script src='/bundle.js'></script>
        </html>
    `)
})
```

### store支持

主要是异步数据的支持

