---
title: react hook实践
date: 2019-10-26  12:00:00
tags: react
categories: 
	- react

---

# react hook实践

又到了跟着文档码字学习的阶段，`hook`从提案到现在已经很久了。在这之前但是还没有真正地去了解这个 react 新玩具。跟随[文档](https://react.docschina.org/docs/hooks-intro.html#gradual-adoption-strategy)学习，并尝试重构一些项目



## 简介

官方自带的视频已经很好地介绍了 `hook`



## 概览

Hook 是 React.16.8 新增特征，可以让你在不编写 class的情况下使用 state 以及其他 React 特性

### `State Hook`

简单的示例：

```jsx
import React,{useState} from 'react'

function Example(){
    const [count,setCount] = useState(0)
    return(
        <div>
            <p>You clicked {count} times</p>
            <button onClick={()=>setCount(count+1)}>
                Click me
            </button>
        </div>
    )
}
```

通过在函数组件里调用它来给组件添加一些内部的 `state`，React 会在渲染的时候保留这个 `state`。`useState`会返回一堆值：当前值和一个让你更新它的函数，可以在时间处理函数中或者其他一些地方调用这个函数。类似 `class`组件的 `this.setState`，但是它不会把新的 `state`和旧的 `state`进行合并。

`useState` 唯一的参数就是初始的`state`。上面的例子中，计数器从零开始的，所有初始 `state`就是0。注意不同于 `this.state`，这里的 `state`不一定要是一个对象。

#### 声明多个变量

在一个组件中声明多个变量

```jsx
function ExampleWithManyStates(){
    const [age,setAge] = useState(42)
    const [fruit,setFruit] = useState('banana')
    const [todos,setTodos] = useState({text:'Learn Hooks'})
}
```

#### Hook?

Hook 是一些可以让你在函数组件里 钩入 `React state`以及生命周期函数等特性的函数。Hook 不能在 class 组件中使用。使得不用 class 也可以使用 React.



### `Effect Hook`

React 组件中数据获取、订阅或者手动修改 DOM,都统称为副作用，或者称为作用

`useEffect`就是 Effect Hook，给函数组件增加了操作副作用的能力，跟 class 组件的 `componentDid`、`componentDidUpdate`、`componentWillUnmount`具有相同的用途，只不过被合并成了一个AOU



例子，在 React 更新 DOM 后设置一个页面的标题

```jsx
import React, { useState, useEffect } from 'react'

function Example(){
    const [count, setCount] = useState(0)
    
    // 相当于 componentDidMount 和 componentDidUpdate
    useEffect(()=>{
        document.title = `laibh.top you check ${count} times`
    })
    return(
        <div>
            <p>You click {count} times</p>
            <button onClick={()=>setCount(count+1)}>
                Click me
            </button>
        </div>
    )
}
```

当调用 `useEffect`，就是在告诉 React 在完成对 DOM 的更改后运行副作用函数，由于副作用函数是在组件内声明的，所有可以访问到组件的 `props`或者 `state`。默认情况下，React 会在每次渲染后调用副作用函数——包括第一次渲染的时候



副作用函数还可以通过返回一个函数来指定清除副作用。例如，在下面的组件中使用副作用函数来订阅好友的在线状态，并通过取消订阅来进行清除操作：

```jsx
import React,{useState,useEffect} from 'react'

function FriendStatus(props){
    const [isOnline,setIsOnline] = useState(null)
    function handleStatusChange(status){
        setIsOnline(status.isOnline)
    }
    useEffect(()=>{
        ChatAPI.subscribeToFrinedStatus(props.friend.id,handleStatusChange)
        return()=>{
            ChatAPI.unsubscribeFromFriendStatus(props.friend.id,handleStatusChange)
        }
    })
    if(isOnline === null){
    	return 'Loading ...'   
    }
    return isOnline ? 'Online' : 'Offline'
}
```

上面的实例中，React会在组件销毁或者后续渲染时重新执行副作用函数，取消对 `ChatAPI`的订阅。

跟 `useState`一样，可以在组件中多次使用 `useEffect`

```jsx
function FriendStatusWithCounter(props){
    const [count,setCount] = useState(0)
    useEffect(()=>{
        document.title = `You clicked ${count} times`
    })
    const [isOnline,setIsOnline] = useState(null)
    useEffect(()=>{
        ChatAPI.subscribeToFriendStatus(props.friend.id,handleStatusChange)
        return()=>{
            ChatAPI.subscribeFromFriendStatus(props.friend.id,handleStatusChange)
        }
    })
    function handleStatusChange(status){
        setIsOnline(status.isOnline)
    }
}
```

通过使用 Hook,可以把组件内相关的副作用组织在一起（例如创建订阅以及及时取消），而不要把它们拆分到不同的生命周期函数



### `Hook`使用规则

Hook 就是 javascript 函数，但是使用它们会有两个额外的规则：

只能在函数最外层调用Hook。不要在循环、条件判断或者子函数中调用

只能在React 的函数组件中调用Hook。



### 自定义 `Hook`

在之前，组件之间复用一些状态逻辑，有两种主流方案：高阶组件、`render props`。自定义 `Hook`可以在不增加组件的情况下达到相同的目的

`FriendStatus`组件，通过调用 `useState`和 `useEffect`的 Hook 来订阅一个好友的在线状态，假设我们想在另一个组件里复用这个订阅逻辑

首先，把逻辑提取到一个叫做 `useFriendStatus`的自定义 `Hook`里：

```jsx
import React,{useState,useEffect} from 'react'

function useFriendStatus(friendID){
    const [isOnline,setIsOnline] = useState(null)
    
    function handleStatusChange(status){
        setIsOnline(status.isOnline)
    }
    useEffect(()=>{
        ChatAPI.subscribeToFriendStatus(friendID,handleStatusChange)
        return()=>{
            ChatAPI.unsubscribeFromFriendStatus(friendID,handleStatusChange)
        }
    })
    return isOnline
}
```

