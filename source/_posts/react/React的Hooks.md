---
title: React的Hooks
tags: react
categories:
- react
- react进阶
---

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

## useReducer

## useContext

