---
title: vue组件间通信
tags: vue
categories: 前端
---
### 父组件 => 子组件

#### 通过属性props传入

大致使用如下:

```js
// child
props: { msg: String }
// parent
   <Helloworld msg = 'welcome to vue study/>
```

##### 	prop的大小写

```js
props: ['postTitle'] //camelCase (驼峰命名法)

<Helloworld post-title="hello!"></Helloworld> // kebab-case (短横线分隔命名)
```

#####	 props的类型

1. props以字符串形式出现的数组

   ```js
   props: ['title', 'likes', 'isPublished']
   ```

2. 对象形式，属性为键值对，key和value分别为prop各自的名称和类型

   ```js
   props: {
     title: String,
     likes: Number,
     isPublished: Boolean
   }
   ```
   

##### props的校验

为了定制 prop 的验证方式，你可以为 `props` 中的值提供一个带有验证需求的对象

```js
   props: {
       // 基础的类型检查 (`null` 和 `undefined` 会通过任何类型验证)
       propA: Number,
       // 多个可能的类型
       propB: [String, Number],
       // 必填的字符串
       propC: {
         type: String,
         required: true
       },
       // 带有默认值的数字
       propD: {
         type: Number,
         default: 100
       }
         // 带有默认值的对象
       propE: {
         type: Object,
         // 对象或数组默认值必须从一个工厂函数获取
         default: function () {
           return { message: 'hello' }
         }
       },
       // 自定义验证函数
       propF: {
         validator: function (value) {
           // 这个值必须匹配下列字符串中的一个
           return ['success', 'warning', 'danger'].indexOf(value) !== -1
         }
       }
   }
```

#### 引用refs

```js
// parent
<HelloWorld ref = 'hw'/>
// child
this.$refs.hw
```

### 子组件 => 父组件

通过自定义事件

```js
// child
this.$emit('add', good)
// parent
<Cart @add="cartAdd()"></Cart>
```

### 兄弟间通讯

通过通过共同的祖辈组件搭桥，$parent或$root

```js
// brother1 
this.$parent.$on('foo', handle)
// brother2
this.$parent.$emit('foo')
```

### 祖先和后代之间

#### 祖先 => 后代

通过provide/inject

```js
// ancestor
provide() { // provide为函数，通过返回对象传值给后代
	return {
    foo: 'foo'
  }
}
// descendant
inject: ['foo'] // 通过数组取出
```

#### 后代 => 祖先

一层一层网上传递，相当于多个子传父

```js
// 定义一个dispatch方法，指定要派发事件名称和数据 
function dispatch(eventName, data) {
	let parent = this.$parent
	// 只要还存在父元素就继续往上查找
  while (parent) {
	// 父元素用$emit触发 
    parent.$emit(eventName,data) // 递归查找父元素
		parent = parent.$parent
	} 
}
// 使用，HelloWorld.vue
<h1 @click="dispatch('hello', 'hello,world')">{{ msg }}</h1>
// App.vue
this.$on('hello', this.sayHello)
```

### 任意组件通讯

通过中央事件：创建一个Bus类负责事件派发、监听和回调管理

```js
// Bus:事件派发、监听和回调管理 
class Bus{
  constructor(){
    // {
    //   eventName1:[fn1,fn2],
    //   eventName2:[fn3,fn4],
    // }
    this.callbacks = {}
  }
  $on(name, fn){
    this.callbacks[name] = this.callbacks[name] || []
    this.callbacks[name].push(fn)
  }
  $emit(name, args){
    if(this.callbacks[name]){
      this.callbacks[name].forEach(cb => cb(args))
    }
}
// main.js
Vue.prototype.$bus = new Bus()
// child1
this.$bus.$on('foo', handle)
// child2
this.$bus.$emit('foo')
```

### Vuex

创建唯一的全局数据管理者store，通过它管理数据并通知组件状态变更