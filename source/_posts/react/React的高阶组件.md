---
title: React的高阶组件和组件复合
tags: react
categories:
- react
- react进阶
---

## 高阶组件 - Hoc

为了提高组件的复用率、可测试性就要保证组件功能的单一性；HOC(Higher-Order-Components)就可以达到，高阶组件是一个工厂函数，它接收一个组件并返回一个组件(增强后)。

### 基本使用 

如下案例可以为展示组件添加获取数据能力

```jsx
import React from 'react'

// 保证功能单一，不关心数据来源，只负责显示
function Lesson(props) {
    return (
        <div>
            { props.stage } : { props.title}
        </div>
    )
}

// 模拟数据
const lessons = [
    { stage: "React", title: "核心API" },
    { stage: "React", title: "组件化1" },
    { stage: "React", title: "组件化2" }
];

// 负责包装传入组件Comp的高阶组件，可以根据传入的索引号获取课程的数据
const WithContent = Comp => props => {
    const content = lessons[props.index]
    return <Comp { ...content }></Comp>
}

const LessionWithContent =  WithContent(Lesson)

export default function HocTest() {
    return (
        <div>
            { lessons.map((content, index) => {
                return (<LessionWithContent index= { index } key = { index }/>)
            }) }
        </div>
    )
}
```

### 链式调用

```jsx
// 高阶组件withLog负责包装传入组件Comp
// 包装后组件在挂载时可以输出日志记录
const withLog = Comp => {
    // 返回组件需要生命周期，因此声明为class组件
    return class extends React.Component {
        render() {
            return <Comp {...this.props} />
        }
        componentDidMount() {
            console.log("didMount", this.props)
        }
    }
}

const LessionWithContent =  withLog(WithContent(Lesson))
```

### 装饰器的使用

高阶组件本身就是装饰器的实际使用，ES7中支持装饰器写法，装饰器只能用再**class**上，执行顺序从下往上

```jsx
@withLog
@WithContent
class Lesson2 extends React.Component {
    render() {
        return (
            <div>
                {this.props.stage} - {this.props.title}
            </div>
        )
    }
}

export default function HocTest() {
    return (
        <div>
            { lessons.map((content, index) => {
                return (<Lesson2 index= { index } key = { index }/>)
            }) }
        </div>
    )
}
```
