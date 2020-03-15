---
title: React的Hooks
tags: react
categories:
- react
- react进阶
---

Hook是React16.8一个新增项，它可以让你在不编写class的情况下使用state以及其他React特性。

特点：

- 是你无需修改组件结构的情况下附庸状态逻辑
- 可将组件中相互关联的部分拆成更小的函数，复杂组件将便的更容易理解。
- 更简洁、更易理解的代码

## state Hook

```jsx
import React, { useState } from 'react'

export default function HooksTest() {
    const [fruits, setFruits] = useState(['apple', 'banana'])

    return (
        <div>
            {/*添加列表组件*/}
            <FruitList fruits={ fruits } onSetFruit={ setFruits }/>
            {/*添加水果组件*/}
            <FruitAdd onAddFruit={pname => setFruits([...fruits, pname])} />
        </div>

    )
}

function FruitList({ fruits, onSetFruit }) {
    return (
        <div>
            <ul>
                {fruits.map(f => (
                    <li key={f} onClick={() => onSetFruit(f)}>
                        {f}
                    </li>
                ))}
            </ul>
        </div>
    )
}

function FruitAdd(props) {
    // 输入内容状态及设置内容状态的方法
    const [pname, setPname] = useState("");
    // 键盘事件处理
    const onAddFruit = e => {
        if (e.key === 'Enter') {
            props.onAddFruit(pname)
            setPname('');
        }
    }
        return (
            <div>
                <input
                type = 'text'
                value = {pname}
                onChange = {e => setPname(e.target.value)}
                onKeyDown = { onAddFruit }
                />
            </div>
        )
}
```

## Effect Hook

`useEffect `给函数组件增加了执行副作用操作的能力。
副作用`（Side Effect）`是指一个 function 做了和本身运算返回值无关的事，比如：修改了全局变量、修改了传入的参数、甚至是 `console.log()`，所以 `ajax` 操作、修改 `dom `都是算作副作用。

```jsx
import { useEffect } from "react";
useEffect(()=>{
    setTimeout(() => {
        setFruits(['香蕉','西瓜'])
    }, 1000);
},[])// 设置空数组意为没有依赖，则副作用操作仅执行一次


```

**注意**：第二个参数为依赖性，如果它改变则会再执行一次函数，依赖为空表示只执行一次。如果副作用操作对某状态有依赖，务必添加依赖选项

```javascript
useEffect(() => {
	document.title = fruit;
}, [fruit]);
```

清除工作：有一些副作用是需要清除的，清除工作非常重要的，可以防止引起内存泄露

```javascript
useEffect(() => {
    const timer = setInterval(() => {
        console.log('msg');
    }, 1000);
    return function(){
        clearInterval(timer);
    }
}, []);
```

## `useReducer`

`useReducer`是`useState`的可选项，常用于组件有复杂状态逻辑时，类似于`redux`中reducer概念

```jsx
export default function HooksTest() {
    // const [fruits, setFruits] = useState(['apple', 'banana'])
    const [fruits, dispatch ] = useReducer(fruitReducer, [])
    
    useEffect(() => {
        setTimeout(() => {
            // setFruits(["香蕉", "西瓜"]);
            // 变更状态，派发动作即可
            dispatch({ type: "init", payload: ["apple", "banana"] });
        }, 1000);
    }, []);

    return (
        <div>
            {/*添加列表组件*/}
            <FruitList fruits={ fruits } />
            {/*添加水果组件*/}
            {/* <FruitAdd onAddFruit={pname => setFruits([...fruits, pname])} /> */}
            <FruitAdd onAddFruit={ pname => dispatch({type: 'add', payload: pname}) } />
        </div>
    )
}

// 添加fruit状态维护fruitReducer
function fruitReducer(state, action) {
    switch (action.type) {
        case "init":
            return action.payload;
        case "add":
            return [...state, action.payload];
        default:
            return state;
    }
}   
```



## `useContext`

`useContext`用于在快速在函数组件中导入上下文。

```jsx
export default function HooksTest() {
// ...
return (
    {/* 提供上下文的值 */}
    <Context.Provider value={{fruits,dispatch}}>
        <div>
            {/* 这里不再需要给FruitAdd传递状态mutation函数，实现了解耦 */}
            <FruitAdd />
        </div>
    </Context.Provider>
);
}
function FruitAdd(props) {
    // 使用useContext获取上下文
    const {dispatch} = useContext(Context)
    const onAddFruit = e => {
        if (e.key === "Enter") {
            // 直接派发动作修改状态
            dispatch({ type: "add", payload: pname })
            setPname("");
        }
    };
    // ...
}
```

## 自定义Hooks

