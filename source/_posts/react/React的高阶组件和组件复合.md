---
title: React的高阶组件和组件复合
tags: react
categories:
- react
- react进阶
---

## 高阶组件 - Hoc

返回组件的组件，强化组件功能，如下案例可以为展示组件添加获取数据能力

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

// 负责包装传入组件Comp的高级组件，可以根据传入的索引号获取课程的数据
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

```jsx
// 装饰器只能用再class上，执行顺序从上往下
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

## 组件复合 - Compositon

复合组件给你足够的敏捷去定义自定义的组件外观和行为

### 普通插槽

下面案例Dialog组件负责展示，内容从外部传入，类似于Vue的**普通插槽**

```jsx
import React from 'react'

// Dialog定义组件外观和行为
function Dialog(props) {
    return <div style={{ border: '1px solid blue'}}>{ props.children }</div>
}

export default function CompositionTest() {
    return (
        <div>
            <Dialog>
                {/* 传入显示内容 */}
                <h1>组件复合</h1>
                <p>复合组件给与你足够的敏捷去定义自定义组件的外观和行为</p>
            </Dialog>
        </div>
    )
}
```

### 具名插槽

传入对象，key则表示**具名插槽**

```jsx
// 获取相应部分内容展示在指定位置
function Dialog2(props) {
    return (<div style={{ border: '1px solid blue'}}>
            { props.children.default }
            <div> { props.children.footer }</div>
        </div>)
}
export default function CompositionTest() {
    return (
        <div>
            <h2>具名插槽：</h2>
            <Dialog2>
                {{ default: (
                    <div>
                        <p>default内容</p>
                    </div>
                ),
                footer: <button onClick={() => alert("这是footer")}>确定</button>
                }}
            </Dialog2>
        </div>
    )
}
```

### 作用域插槽



## 高阶组件和组件复合的区别

