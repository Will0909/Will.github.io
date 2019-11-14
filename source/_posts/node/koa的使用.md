---
title: koa的使用
tags: node
categories: 
- node
- koa
---



### Koa是什么

Koa是一个基于Node.js的Web框架，致力于成为web应用和API开发领域中的一个更小、更富表现性、更健壮的基石。

Koa有以下特点：

- 轻量，无捆绑：Koa体积小，因为它没有捆绑任何中间件。
- 中间件架构：包含一组中间件函数，是按照类似**堆栈**的方式组织和执行的，当一个中间件调用next()，则该函数暂停并将控制传递给下一个中间件，当无更多中间件后，堆栈将展开并且由下至上每个中间件恢复执行其上游行为。(洋葱圈模型)
- 优雅的API设计，如：Koa可以使用async功能，解决了Node中使用不友好的回调。
- 增强的错误处理。

#### 常见的中间件

- 静态服务，一些静态文件的处理，省去需要从本地目录读取文件的很多步骤。

  ```javascript
  app.use(require('koa-static')(__dirname + '/static'))
  ```

  这样便完成一个静态服务器的搭建，static文件夹里面的文件可以通过路径访问。

- 路由

  ```javascript
  const router = require('koa-router')()
  router.get('/string', async (ctx, next) => {
    ctx.body = 'koa string'
  })
  app.use(router.routes())
  ```

- 日志

	```javascript
app.use(async(ctx, next) => {
	  const start = new Date().getTime()
	  console.log(`start: ${ctx.url}`)
	  await next()
	  const end = new Date().getTime()
	  console.log(`请求${ctx.url}耗时${parseInt(end-start)}ms`)
	})
	```



#### Koa原理

