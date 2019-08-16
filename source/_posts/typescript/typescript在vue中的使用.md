---
title: typescript在vue中的使用
tags: typescript
categories: 
- typescript
---

#### 属性,方法，钩子的添加

不加装饰器，则为正常属性，作为**data选项**

```typescript
features: string[]
```

声明方法将来会加入到methods里面，除了生命周期函数

```typescript
addFeature(e:any) {
        this.features.push({ 
            id: this.features.length + 1,
            name: e.target.value 
         })
        e.target.value = ""
    }
```

钩子函数的添加

```typescript
    async created() {
        const result = await getData<Feature>()
        this.features = result.data
    }
```

#### vuex的使用：vuex-class

##### 安装

```js
npm i vuex-class -S
```

##### 定义状态，store.js

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
    state: {
        features: [{
            id: 1, name: '类型检测'
        }]
    },
    mutations: {
        addFeature(state: any, featureName: string) {
            state.features.push({
                id: state.features.length + 1,
                name: featureName
            })
        }
    },
    actions: {
        addFeature({ commit }, featureName: string) {
            commit('addFeature', featureName)
        }
    }
})
```

##### 使用

```typescript
import { State, Action, Mutation } from "vuex-class";
@Component
export default class Feature extends Vue {
    // State映射状态
    // Getter、State添加到computed选项
    // Action/Mutation添加到methods选项
    @State('features') features!: Feature[]
    @Action('addFeature') addFeatureAction: any
    @Mutation('addFeature') addFeatureMutation: any
    private addFeature(event) {
        console.log(event);
        // this.features.push(event.target.value);
        this.addFeatureAction(event.target.value);
        // this.addFeaturMutation(event.target.value);
        event.target.value = "";
    }
}
```

#### 装饰器的使用

```typescript
import { Component, Prop, Vue, Emit, Watch  } from 'vue-property-decorator'
export default class Feature extends Vue {
	// 装饰器若不传参，则函数名就是事件名，转换为羊肉串写法
    // 装饰器若传参，则参数是事件名，写什么就是什么不转换
    // 返回值是自定义事件附加参数
    @Emit('fooBar')
    addFeature(e:any) {
        // this.features.push({ 
        //         id: this.features.length + 1,
        //         name: e.target.value 
        //     })
        // e.target.value = ""
        this.addFeatureAction(e.target.value)
        return e.target.value
    }

    @Watch('msg', {deep: true})
    onMsgChange(val: string, oldVal: any) {
        console.log('onMsgChange - ', val, oldVal)
    }

    @Watch('$route')
    onRouteChange(val: any) {
        console.log('route changed - ', val)
    }
}
```

#### 存取器实现计算属性

```typescript
    // 存取器实现计算属性
    get count() {
        return this.features.length
    }
```





