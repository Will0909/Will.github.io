---
title: Vue插槽
tags: vue
top: 1
categories: 
- vue
- 基础知识
---


Slot(插槽)实现了内容的分发， Vue 2.6.0之后采用全新的v-slot语法取代了之前的slot、slot-scope

#### 匿名插槽

```html
// comp
<div>
	<slot></slot>
</div>

// parent
<comp>hello</comp>
```

#### 编译作用域

父级模板里的所有内容都是在父级作用域中编译的；子模板里的所有内容都是在子作用域中编译的

```html
<navigation-link url="/profile">
  Clicking here will send you to: {{ url }}
</navigation-link>
  <!--这里的 `url` 会是 undefined，因为 "/profile" 是传递给<navigation-link> 的而不是
  在 <navigation-link> 组件内部定义的。-->
```

#### 具名插槽

有时我们需要多个插槽，此时可以使用具名插槽

一个不带 `name` 的 `<slot>` 出口会带有隐含的名字“default”。

在向具名插槽提供内容的时候，我们可以在一个 `<template>` 元素上使用 `v-slot` 指令，并以 `v-slot` 的参数的形式提供其名称，v-slot **只能**添加在一个 <template> 上（**独占默认插槽**除外）

```html
// comp
<div>
    <slot></slot>
    <slot name="content"></slot>
</div>

// parent
<comp>
	<!-- 默认插槽用default做参数 -->
    <template v-slot:default>具名插槽</template>
    <!-- 具名插槽用插槽名做参数 -->
    <template v-slot:content>内容...</template>
</comp>
```

`v-slot` 也有缩写，即把参数之前的所有内容 (`v-slot:`) 替换为字符 `#`。例如 `v-slot:content` 可以被重写为 `#content`，注意`#=`无效，必须写成`#default=`

#### 作用域插槽

```html
// Comp3
<div>
	<slot :foo="foo"></slot>
</div>

// parent
<Comp3>
    <!-- 把v-slot的值指定为作用域上下文对象, ctx可以随便取 -->
    <template v-slot:default="ctx">
    来自子组件数据：{{ctx.foo}}
    </template>
</Comp3>
```

#### 独占默认插槽

当被提供的内容**只有**默认插槽时，组件的标签才可以被当作插槽的模板来使用。这样我们就可以把 `v-slot` 直接用在组件上

```html
<current-user v-slot="slotProps">
  {{ slotProps.user.firstName }}
</current-user>

// 可使用解构方式
<current-user v-slot="{ user }">
  {{ user.firstName }}
</current-user>
```

#### 解构插槽 Prop

作用域插槽的内部工作原理是将你的插槽内容包括在一个传入单个参数的函数里，所以 `v-slot` 的值实际上可以是任何能够作为函数定义中的参数的 JavaScript 表达式。所以使用 [ES2015 解构](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#解构对象)来传入具体的插槽 prop，如下：

```html
<current-user v-slot="{ user }">
  {{ user.firstName }}
</current-user>

// 别名
<current-user v-slot="{ user: person }">
  {{ person.firstName }}
</current-user>

// 带默认值
<current-user v-slot="{ user = { firstName: 'Guest' } }">
  {{ user.firstName }}
</current-user>
```

