---
title: vue的递归组件
tags: vue
categories: 
- vue
- 进阶
---

组件是可以在它们自己的模板中调用自身的，递归组件必须注意的两点是：

1. 要有名字，有名字组件才能识别本身
2. 要有结束的条件，才能避免导致无限循环

主要代码如下：

#### 递归组件Item创建

```js
<template>
  <li>
    <div @click="toggle">
      {{model.title}}
      <span v-if="isFolder">[{{open ? '-' : '+'}}]</span>
    </div>
    <ul v-show="open" v-if="isFolder">
      <item class="item" v-for="model in model.children" :model="model" :key="model.title"></item>
    </ul>
  </li>
</template>

<script>
export default {
  name: "Item",
  props: {
    model: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      open: false
    };
  },
  computed: {
    isFolder() {
      return this.model.children && this.model.children.length;
    }
  },
  methods: {
    toggle() {
      if (this.isFolder) {
        this.open = !this.open;
      }
    }
  }
};
</script>
```

#### 数据和使用

```js
<template>
  <div>
    <ul>
      <item class="item" :model="treeData"></item>
    </ul>
  </div>
</template>

<script>
import Item from "./Item";
export default {
  name: "app",
  data() {
    return {
      treeData: {
        title: "Web全栈架构师",
        children: [
          {
            title: "Java架构师"
          },
          {
            title: "JS高级",
            children: [
              {
                title: "ES6"
              },
              {
                title: "动效"
              }
            ]
          },
          {
            title: "Web全栈",
            children: [
              {
                title: "Vue训练营",
                expand: true,
                children: [
                  {
                    title: "组件化"
                  },
                  {
                    title: "源码"
                  },
                  {
                    title: "docker部署"
                  }
                ]
              },
              {
                title: "React",
                children: [
                  {
                    title: "JSX"
                  },
                  {
                    title: "虚拟DOM"
                  }
                ]
              },
              {
                title: "Node"
              }
            ]
          }
        ]
      }
    };
  },
  components: { Item }
};
</script>
```

