---
title: vue最佳实践
tags: vue
categories: 
- vue
- 项目实战
---

## vue-cli 3.x项目配置

[vue-cli 3.0](https://cli.vuejs.org/zh/guide/webpack.html)项目是基于webpack3.0构建，并**带有合理的默认配置**，和webpack2.0的主要区别是**隐藏了详细配置**。

### 1. 在根目录新建vue.config.js配置

可以通过在项目根目录新建vue.config进行配置

```javascript
// vue.config.js
const port = 7070;
const title = "vue项目最佳实践";
module.exports = {
    publicPath: '/best-practice', // 部署应用包时的基本 URL
    devServer: {
    	port: port,
    },
    configureWebpack: {
    // 向index.html注入标题
    name: title
  }
};
```

```html
<!--index.html--> 
<title><%= webpackConfig.name %></title> <!--webpack占位符--> 
```

**注意：需要vue-cli-service版本大于等于3.5**

### 2. 查看配置结果

#### (1). 可以通过命令查看配置结果

- vue inspect 全部配置
- vue inspect --rules 查看全部规则
- vue inspect --rule vue 查看指定规则
- vue inspect --plugins 查看全部插件
- vue inspect --plugin vue-plugin 查看指定插件
- vue inspect --mode development 指定模式

#### (2). 通过命令`vue ui`可视化查看

### 3. 高级链式操作

Vue CLI内部的webpack配置是通过-[**webpack-chain**](https://github.com/mozilla-neutrino/webpack-chain)来维护的，这个库提供了webpack原始配置的上层抽象，这样可以使用户更好的进行配置(可以使用具名loader规则和具名插件)

这里主要通过svg-sprit-loader的使用来演示webpack的高级链式操作配置，svg-sprite-loader可以以雪碧图的方式使用icon

- 安装svg-sprite-loader

  ```javascript
  npm i svg-sprite-loader -D
  ```

- 下载图标存入src/icons/svg中，可以在[iconfont](https://www.iconfont.cn/)中下载

- 在vue.config.js中修改规则，通过链式调用

  ```javascript
  // resolve定义一个绝对路径获取函数
  const path = require('path')
  function resolve(dir) {
  	return path.join(__dirname, dir)
  }
  
  module.exports = {
      chainWebpack(config) {
           // 1. 将src/icons目录中的svg从原来的svg rule中剔除
          config.module
              .rule("svg")
              .exclude.add(resolve("src/icons"))
              .end();
          // 2. 新增icons规则，配置loader，设置svg-sprite-loader处理icons目录中的svg
          config.module
              .rule("icons")
              .test(/\.svg$/)
              .include.add(resolve("src/icons"))
              .end()
              .use("svg-sprite-loader")
              .loader("svg-sprite-loader")
              .options({ symbolId: "icon-[name]"})// 使用时直接通过icon-[name]
              .end();
  	}
  }
  ```

  

- icons自动引入

  通过`require.context`进行全局引入，`require.context`生成一个上下文模块，包含目录下 的所有引用，此方法有三个参数

  参数一: 要查询的目录
  参数二:  是否要查询子孙目录,方法默认的值为false
  参数三: 要匹配的文件的后缀,是一个正则表达式

```javascript
// icons/index.js
const req = require.context('./svg', false, /\.svg$/)
req.keys().map(req)
// main.js
import './icons'
```

- 封装图标组件，控制样式，添加链接等

  自定义一个SvgIcon.vue组件

```vue
<template>
	<svg :class="svgClass" aria-hidden="true" v-on="$listeners">
    	<use :xlink:href="iconName">
    </svg>
</template>

<script>
	export default {
        name: 'SvgIcon',
        props: {
            iconClass: {
                type: String,
                required: true
            },
            className: {
                type: String,
                default: ''
            }
        },
        computed: {
            iconName() {
                return `#icon-${this.iconClass}`
            },
            svgClass() {
                if (this.className) {
                    return `svg-icon ${this.className}`
                } else {
                    return 'svg-icon'
                }
            }
        }
    }
</script>

<style scoped>
    .svg-icon {
        width: 1em;
        height: 1em;
        vertical-align: -0.15em;
        fill: currentColor;
        overflow: hidden;
    }
</style>
```

​	注册组件，icons/index.js

```javascript
import Vue from 'vue'
import SvgIcon from '@/components/SvgIcon.vue'

vue.component('svg-icon', SvgIcon)
```

使用

```vue
<svg-icon icon-class="qq"></svg-icon>
```



## 权限控制 + 动态路由

### 1. 路由定义

路由一般分为两种：constantRoutes和asyncRoutes

- **constantRoutes**:代表那些不需要动态判断权限的路由，如：登录页、404等通用页面。
- **asyncRoutes**:代表那些需要判断权限并通过`addRoutes`动态添加的页面。

### vuex相关模块实现

### 2. 大致过程

1. 客户端登录请求，从服务端获取token，可以存入cookie中(通过js-cookie)
2. 客户端发起获取用户信息的请求(请求中包含token)，从服务器中获取到用户角色(存入vuex)
3. 根据用户角色做路由表过滤，将当前角色允许访问的特殊页面通过addRoutes添加到accessRoutes(权限路由表)
   - 过滤思路：递归遍历完整路由表看当前角色是否存在各个路由的meta.roles(存取了当前路径所允许的角色数组)中，存在则添加至此角色的accessRoutes中.
4. 通过**`router.beforeEach`**添加全局路由守卫，根据是否登录(根据token)、无需token的白名单已经是否已添加accessRoutes等判断后续动作
   - 未登陆(是否有token)则登陆(步骤1)
   - 已登陆没角色, 则获取用户信息(如步骤2)，并添加至路由
   - 已登陆有角色，直接next()

### 3. 导航菜单的生成

导航菜单是根据路由信息并结合权限判断而动态生成的，它需要支持路由的多级嵌套，需要用到递归组件。

- 仅有一个可实现子路由并且该子路由没有子路由则直接显示父项
- 菜单样式可以用element的sidebar-item组件

### 4. 按钮权限

封装一个**[自定义指令](https://cn.vuejs.org/v2/guide/custom-directive.html)**v-permission，从而实现按钮级别权限控制。

指令传入当前按钮允许的角色数组，内部会判断当前角色如果不在此角色数组中则会删除按钮

```javascript
import store from "@/store"

export default {
    inserted(el, binding) {
        // 获取指令的值：按钮要求的角色数组
        const { value: pRoles } = binding
        // 获取用户角色
        const roles = store.getters && store.getters.roles
        
        if (pRoles && pRoles instanceOf Array && pRoles.length > 0) {
            // 判断用户角色中是否有按钮要求的角色
            const hasPermission = roles.some(role => pRoles.inculdes(role))
            // 如果没有权限则删除当前dom
            if (!hasPermission) {
                el.parentNode && el.parentNode.removeChild(el)
            }
        } else {
            throw new Error(`需要指定按钮要求角色数组，如v-permission="['admin', 'editor']"`)
        }
    }
}
```

注册指令， main.js

```javascript
import vPermission from "./directive/permission"

Vue.directive('permission', vPermission)
```

使用

```vue
// 添加权限按钮
<button v-permisson="['admin', 'editor']"></button>
```

**注意：该指令只能删除挂载指令的元素，对于额外生成的和指令无关的元素无能为力**，比如以下：

```html
<el-tabs>
    <el-tab-pane label="用户管理" name="first" v-permission="['admin', 'editor']">用户管
    理</el-tab-pane>
</el-tabs>
```

此种情况下只能删除`el-tab-pane`，但无法删除`el-tabs`，此时可以通过`v-if`来实现

```Vue
<template>
	<el-tab-pane v-if="checkPermission('admin')"></el-tab-pane>
</template>

<script>
export default {
methods: {
    checkPermission(permissionRoles) {
        return roles.some(role => {
            	return permissionRoles.includes(role);
            });
        }
    }
}
</script>
```



## 面包屑

显示当前页面的路径，快速返回之前的任意页面。面包屑导航是通过`$route.matched`数组动态生成的。

UI样式可以通过element-ui的`el-breadcrumb-item` 实现，代码如下：

```vue
<template>
	<el-breadcrumb class="app-breadcrumb" separator="/">
		<transition-group name="breadcrumb">
            <el-breadcrumb-item v-for="(item,index) in levelList" :key="item.path">
                <span
                    v-if="item.redirect==='noRedirect'||index==levelList.length-1"
                    class="no-redirect"
                >{{ item.meta.title }}</span>
                <a v-else @click.prevent="handleLink(item)">{{ item.meta.title }}</a>
            </el-breadcrumb-item>
    	</transition-group>
	</el-breadcrumb>
</template>

<script>
import pathToRegexp from "path-to-regexp";
export default {
    data() {
        return {
        	levelList: null
        };
    },
    watch: {
        $route: {
            handler(route) {
                this.getBreadcrumb();
            },
            immediate: true
        }
    },
    methods: {
        getBreadcrumb() {
            console.log(this.$route.matched);
            // 面包屑仅显示包含meta.title且item.meta.breadcrumb不为false的路由
            let matched = this.$route.matched.filter(
                item => item.meta && item.meta.title && item.meta.breadcrumb !== false
                );
            // 根路由
            const first = matched[0];
            // 根匹配只要不是home，就作为home下一级
            if (!this.isHome(first)) {
            	matched = [{ path: '/', redirect: "/home", meta: { title: "首页" }
				}].concat(matched);
            }
            // 处理完指定到levelList
            this.levelList = matched
        },
        isHome(route) {
            const name = route && route.name;
            if (!name) {
            	return false;
            }
            return name.trim().toLocaleLowerCase() === "home".toLocaleLowerCase();
        },
        pathCompile(path) {
            const { params } = this.$route;
            var toPath = pathToRegexp.compile(path);
                return toPath(params);
        },
        handleLink(item) {
            const { redirect, path } = item;
            // 若存在重定向，按重定向走
            if (redirect) {
            this.$router.push(redirect);
            return;
        	}
            // 编译path，避免存在路径参数
            this.$router.push(this.pathCompile(path));
        }
    }
};
</script>
<style scoped>
    .app-breadcrumb.el-breadcrumb {
        display: inline-block;
        font-size: 14px;
        line-height: 50px;
        margin-left: 8px;
    }
    .app-breadcrumb.el-breadcrumb .no-redirect {
        color: #97a8be;
        cursor: text;
    }
    /* breadcrumb transition */
    .breadcrumb-enter-active,
    .breadcrumb-leave-active {
    	transition: all .5s;
    }
    .breadcrumb-enter,
    .breadcrumb-leave-active {
        opacity: 0;
        transform: translateX(20px);
    }
    .breadcrumb-move {
    	transition: all .5s;
    }
    .breadcrumb-leave-active {
    	position: absolute;
    }
</style>
```



## 数据交互

数据交互流程：

api service => request => local mock/easy-mock/server api

主要问题分析：

1. 有时需要对请求头、响应进行统一的预处理
2. 请求不同的数据源时url会变化，需要能根据环境自动修改url
3. 可能出现跨越的问题

### 封装request，response

通过axios封装

通过设置baseURL解决不同数据源url变化问题

```javascript
const service = axios.create({
    baseURL: process.env.VUE_APP_BASE_API,
    timeout: 5000
})
```

其中VUE_APP_BASE_API为[环境变量]([https://cli.vuejs.org/zh/guide/mode-and-env.html#%E6%A8%A1%E5%BC%8F](https://cli.vuejs.org/zh/guide/mode-and-env.html#模式))，创建.env.development文件

```javascript
VUE_APP_BASE_API = '/dev-api'
```

通过拦截器对request和response进行请求和响应的预处理

```javascript
// 请求拦截
service.interceptors.request.use(
    config => {
        // do something
        if (store.getters.token) {
        // 设置令牌请求头
        config.headers["X-Token"] = getToken();
        }
        return config;
    },
    error => {
        // 请求错误预处理
        //console.log(error) // for debug
        return Promise.reject(error);
    }
);
// 响应拦截
service.interceptors.response.use(
    // 通过自定义code判定响应状态，也可以通过HTTP状态码判定
    response => {
        // 仅返回数据部分
        const res = response.data;
        // code不为1则判定为一个错误
        if (res.code !== 1) {
        Message({
        message: res.message || "Error",
        type: "error",
        duration: 5 * 1000
    });
    if (res.code === 10008 || res.code === 10012 || res.code === 10014) {
        // 重新登录
        MessageBox.confirm(
            "登录状态异常，请重新登录",
            "确认登录信息",
            {
            confirmButtonText: "重新登录",
            cancelButtonText: "取消",
            type: "warning"
            }
        ).then(() => {
            store.dispatch("user/resetToken").then(() => {
            	location.reload();
            });
        });
        }
        return Promise.reject(new Error(res.message || "Error"));
    } else {
    	return res;
    }
   },
   error => {
        //console.log("err" + error); // for debug
        Message({
        message: error.message,
        type: "error",
        duration: 5 * 1000
        });
        return Promise.reject(error);
   }
);
```

### 数据Mock

数据模拟有两种常见方式，本地mock和线上easy-mock

- 本地mock，修改vue.config.js，给devServer添加相关代码，大致如下：

```javascript
const bodyParser = require("body-parser");
module.exports = {
    devServer: {
        before: app => {
            app.use(bodyParser.json());
            app.use(
                bodyParser.urlencoded({
                extended: true
            })
    );
    app.post("/dev-api/user/login", (req, res) => {
        const { username } = req.body;
        if (username === "admin" || username === "jerry") {
            res.json({
            code: 1,
            data: username
            });
        } else {
            res.json({
                code: 10204,
                message: "用户名或密码错误"
            });
        }
    });
    app.get("/dev-api/user/info", (req, res) => {
        const roles = req.headers['x-token'] === "admin" ? ["admin"] : ["editor"];
        res.json({
            code: 1,
            data: roles
        });
        });
    }
}
```

- easy-mock

使用步骤：
1. 登录easy-mock网站
2. 创建一个项目
3. 创建需要的接口，遵从swagger规范
4. 调用：修改base_url，.env.development

```javascript
VUE_APP_BASE_API = 'https://easy-mock.com/mock/5cdcc3fdde625c6ccadfd70c/kkbcart'//在网站上拷贝
```

### 解决跨越

如果请求的接口在另一台服务器上，开发时可以设置代理避免跨越问题，注意：**服务器之间是不会有跨域问题的，只存在于浏览器像服务器发起请求时**。

- 添加代理配置， vue.config.js

```javascript
devServer: {
    port: port,
    proxy: {
        // 代理 /dev-api/user/login 到 http://127.0.0.1:3000/user/login
        [process.env.VUE_APP_BASE_API]: {
            target: `http://127.0.0.1:3000/`,
            changeOrigin: true,//需不需要变更origin头
            pathRewrite: {
            	["^" + process.env.VUE_APP_BASE_API]: ""
            }
    	}
    },
}
```

- 创建一个独立接口服务器，如： ~/test-server/index.js

```javascript
const express = require("express");
const app = express();
const bodyParser = require("body-parser");
app.use(bodyParser.json());
app.use(
    bodyParser.urlencoded({
    	extended: true
    })
);
app.post("/user/login", (req, res) => {
    const { username } = req.body;
    if (username === "admin" || username === "jerry") {
        res.json({
            code: 1,
            data: username
        });
    } else {
        res.json({
            code: 10204,
            message: "用户名或密码错误"
        });
    }
});
app.get("/user/info", (req, res) => {
    const roles = req.headers['x-token'] === "admin" ? ["admin"] : ["editor"];
    res.json({
        code: 1,
        data: roles
    });
});
app.listen(3000);
```

## 项目测试阶段

应该了解什么是**黑盒测试，E2E测试，集成测试，白盒测试，单元测试**

编写测试代码的好处

了解一些测试库，jest，mocha + chai, 什么是**测试框架** ，什么是**断言库**

vue中的组件怎么测试

怎么测试用户点击

测试覆盖率

详细可查看项目测试阶段的章节

## 项目部署阶段

部署只需要将最终生成的静态文件，通常是`dist`文件夹下的静态文件发布到cdn或者静态服务器即可，常见的是采用nginx作为web服务器

详细可查看详细的项目部署文章。