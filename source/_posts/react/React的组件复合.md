---
title: React的组件复合
tags: react
categories:
- react
- react进阶
---

## 组件复合 - Compositon

复合组件给你足够的敏捷去定义自定义的组件外观和行为，通过props.children，children其实就是一个合法的JS表达式

类似Vue中的插槽概念

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

父组件中可以直接使用子组件的数据，父组件调用子组件时组件的`children`写成函数，函数的参数在子组件中传入

```jsx
function Dialog3(props) {
    const messages = {
        'foo': { title: 'foo', content: 'foo~~'},
        'bar': { title: 'bar', content: 'bar~~'}
    }
    const { body, footer } = props.children(messages[props.msg])

    return (<div style={{ border: '1px solid blue'}}>
            { body }
            <div> { footer }</div>
        </div>)
}

export default function CompositionTest() {
    return (
        <div>
            <h2>作用域插槽：</h2>
            <Dialog3 msg='foo'>
                {
                    // 函数形式，根据传入值生成最终内容。title和content是由子组件中决定
                    ({ title, content }) => ({
                        body:(
                            <div>
                                <h1>{ title }</h1>
                                <p>{ content }</p>
                            </div>
                        ),
                        footer: <button onClick={ () => alert('footer')}>确定</button>
                    })
                }
            </Dialog3>
        </div>
    )
}
```

### 如果`props.children`是`jsx`，此时它是不能修改的

```jsx
function RadioGroup(props) {
    // 不可行,
    // React.Children.forEach(props.children, child => {
    // child.props.name = props.name;
    // });
    return (
        <div>
            {React.Children.map(props.children, child => {
                // 要修改child属性必须先克隆它，参数一为克隆对象，参数二为需要更新的属性
                return React.cloneElement(child, { name: props.name });
            })}
        </div>
    );
}

// Radio传入value,name和children，注意区分
function Radio({ children, ...rest }) {
    return (
        <label>
            <input type="radio" {...rest} />
            {children}
        </label>
    );
}

export default function CompositionTest() {
    return (
            <div>
                {/* 执行显示消息的key */}
                <RadioGroup name="mvvm">
                    <Radio value="vue">vue</Radio>
                    <Radio value="react">react</Radio>
                    <Radio value="ng">angular</Radio>
                </RadioGroup>
            </div>
        </div>
    )
}
```
