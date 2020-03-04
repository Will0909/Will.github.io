---
title: React的递归组件
tags: react
categories:
- react
- react进阶
---

## 树型组件的实现

组件递归渲染即可

```jsx
//tree.js
import React, { Component } from 'react'
import TreeNode from './TreeNode'

export default class Tree extends Component {
    treeData = {
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
    render() {
        return (<TreeNode treeData= { this.treeData } />)
    }
}
```

```jsx
// TreeNode.js
import React, { Component } from 'react'

export default class TreeNode extends Component {
    constructor(props) {
        super(props)
        this.treeData = this.props.treeData
        const children = this.treeData.children
        this.hasFolder = (children && children.length > 0) ? true : false
        // 一开始是折叠状态
        this.state = {
            open: false
        }
    }

    handleClick = () => {
        this.setState((state) => ({
            open: !state.open
        }))
    }

    render() {
        const children = this.treeData.children
        return (
            <ul>
                <li>
                    <div onClick={ this.handleClick }>
                        { this.treeData.title } 
                        { this.hasFolder ? (<span>{ this.state.open ?  ' -'  :  ' +' }</span>) : null }
                    </div>
                    {this.hasFolder ? (
                        <ul  style={{ display:this.state.open ? 'block':'none' }}>
                            { children && children.map(treeData => {
                                return <TreeNode treeData={ treeData } key={ treeData.title } />
                            }) }
                        </ul>)
                    : null }

                </li>
            </ul>
        )
    }
}
```

