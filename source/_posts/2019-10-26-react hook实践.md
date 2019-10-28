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

它将 `friendID`作为参数，并返回该好友是否在线，我们可以在两个组件中用到它：

```jsx
function FriendStatus(props){
    const isOnline = useFriendStatus(props.friend.id)
    if(isOnline === null){
        return 'Loading'
    }
    return isOnline ? 'Online' : 'Offline';
}

function FriendListItem(props){
    const isOnlie = useFriendStatus(props.friend.id)
    return(
        <li style={{color:isOnline?'green':'black'}}>
            {props.friend.name}
        </li>
    )
}
```

两个组件中的 state 是完全独立的。Hook 是一种复用状态逻辑的方式，不复用 state 本身，事实上，Hook 每次调用都有一个完全独立的 state,因此可以在单个组件中多次调用同一个自定义 Hook

### 其他`Hook`

还有一些使用频率较低的但很有用的 `Hook`，比如使用 `useContext`可以不使用组件嵌套订阅 React 的 Context

```jsx
function Example(){
    const locale = useContext(localeContext)
    const theme = useContext(ThemeContext)
    // ..
}
// useReducer 通过 reducer 来管理组件本地复杂的 state
function Todos(){
    const [todos,dispatch] = useReducer(todosReducer)
}
```



## 使用 `State Hook`

```jsx
// class 示例
class Example extends React.Component{
    constructor(props){
        super(props)
        // 1. 构造函数中设置来初始化 count
        this.state = {
            count:0
        }
    }
    render(){
        return(
            <div>
                <p>You click {this.state.count} times</p>
                <button onClick={()=>this.setState({count:this.state.count+1})}>
                    Click me
                </button>
            </div>
        )
    }
}
// hook示例
import React,{useState} from 'react'

function Example(){
    // 1. 函数组件中没有this,不能分配或者读取this.state,直接调用 useState
    // 2. useState定义了一个 state变量。需要为一个参数初始state，参数可以是数字字符串或者对象。返回值为当前state以及更新state的函数。
    const [count,setCount] = useState(0)
    return(
        <div>
            <p>You click {count} times</p>
            <button onClick={()=>setCount(count+1)}></button>
        </div>
    )
}
```

### `Hook`和函数组件

```jsx
const Example = (props)=>{
    // 在这里可以使用 Hook
	return <div />    
}
function Example(props){
    // 这里可以使用 Hook
    return <div />
}
```

Hook 在 class 内部是不起作用的，可以使用来替代 class

## 使用 `Effect Hook`

`Effect Hook`可以让你在函数组件中执行副作用操作

```jsx
import React,{useState,useEffect} from 'react'
function Example(){
    const [count,setCount] = useState(0)
    
    // 类似于 componentDidMount 和 componentDidUpdate、componentWillUnmount
    useEffect(()=>{
        // 用浏览器的api更新文档标题
        document.title = `You clicked ${count} times`
    })
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

数据获取，设置订阅以及手动更改 React 组件中的 DOM 都属于副作用。在 React 组件中有两种常见副作用操作：需要清除的和不需要清除的。

### 无需清除的 effect

有时候，我们只想在 React 更新DOM 之后运行一些额外的代码，比如网络请求、手动变更 DOM,记录日志，这些都是常见的无需清除的操作。因为我们在执行完这些操作之后，就可以忽略他们了。

#### 使用class

在 React 的 class 组件中，`render`函数是不应该有任何副作用的，一般来说，在这里执行操作太早了，我们都希望在 React 更新 DOM 之后才执行我们的操作

这也是为什么把副作用放在 `componentDidMount`和`componentDidUpdate`函数中。

下面的示例，React 计数器的 class 组件，在 React 对 DOM 进行操作后，立即更新了 document 的 title 属性

```jsx
class Example extends React.Component{
    constructor(props){
        super(props)
        this.state = {
            count:0
        }
    }
    
    componentDidMount(){
        document.title = `You clicked ${this.state.count} times`
    }
    
    componentDidUpdate(){
        document.title = `You clicked ${this.state.count} times`
    }
    
    render(){
        return(
            <div>
                <p>You clicked {this.state.count} times</p>
                <button onClick={()=>this.setState({count:this.state.count+1})}>
                    Click me 
                </button>
            </div>
        )
    }
}
// 在 class 中，我们需要在两个声明周期函数中编写重复代码，很多情况下，我们希望在组件加载和更新时执行同样的操作。从概念上，我们希望每次渲染后执行-但是React的class组件没有提供这样的方法，即使我们提取出来，还是要在两个地方调用它
```

#### 使用hook

```jsx
import React,{useState,useEffect} from 'react'

function Example(){
    const [count,setCount] = useState(0);
    
    useEffect(()=>{
        document.title = `You click ${count} times`
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

// 1. useEffect 告诉React 组件需要在渲染后执行某些操作。会保存传递的函数，并且在执行 DOM更新之后调用它。在这个 effect 中，我们设置了 document 的 title 属性，不过我们也可以执行数据获取或者调用其他命令的 API
// 2. 将 useEffect 放在组件内部让我们可以在 effect 中直接访问 count state 变量或者其他 props.我们不需要特殊的API来读取它，它已经保存在函数作用域中。Hook 使用了 js的闭包机制，而不用在js已经提供了解决方案的情况下，还引入特定的React API
// 3. useEffect会在每次渲染后都执行，默认情况下，它在第一次渲染之后和每次更新之后都会执行，不用去考虑挂载还是更新，React保证了每次运行 effect的同时，DOM都已经更新完毕了
```

与 componentDidMount 和 componentDidUpdate不同，使用 useEffect 调度的 effect 不会阻塞浏览器更新屏幕，让应用看起来响应很快。大多数情况下，effect不需要同步地执行，在个别情况下（例如测试布局），有单独的 useLayoutEffect Hook 使用

### 需要清除的 effect

订阅外部数据源等一些副作用是需要清除的，可以防止内存泄露。

#### 使用class

通常会在 `componentDidMount`中设置订阅，在 `componentWillMount`中清除它。假设我们有一个 `ChatApI`模块，运行我们订阅好友的在线状态

```jsx
class FriendStatus extends React.Component{
    constructor(props){
        this.state = {
            isOnline:null
        }
        this.handleStatusChange = this.handleStatusChange.bind(this)
    }
    
    componentDidMount(){
        ChatAPI.subsribeToFriendStatus(
            this.props.friend.id,
            this.handleStatusChange
        )
    }
    componentWillMount(){
        ChatAPI.unsubsribeToFriendStatue(
            this.props.friend.id,
            this.handleStatusChange
        )
    }
    handleStatusChange(status){
        this.setState({
            isOnline:status.isOnline
        })
    }
    render(){
        if(this.state.isOnline === null){
            return 'Loading...'
        }
        return this.state.isOnline ? 'ONline' : 'Offline'
    }
}
```

#### 使用hook

useEffect 设计在同一个地方执行添加和删除订阅，effect返回一个函数，React就会在指定清除的时候调用它

```jsx
import React,{useState,useEffect} from 'react'

function FriendStatus(props){
    const [isOnline,setIsOnline] = useState(null)
    
    useEffect(()=>{
        function handleStatusChange(status){
            setIsOnline(status.isOnline)
        }
        ChatAPI.subscribeToFriendStatus(props.friend.id,handleStatusChange)
        return function cleanup(){
            ChatAPI.unsubscribeFromFriendStatus(props.friend.id,handleStatuChange)
        }
    })
    
    if(isOnline === null){
        return 'Loading...'
    }
    return isOnline ? 'Online' : 'Offline'
}
// 1. effect返回一个函数，这是 effect 可选的清除机制，每个 effect 都可以返回一个清除函数。所以可以将添加和订阅的逻辑放在一起，都属于 effect的一部分
// 2. React 会在组件卸载的时候执行清除操作，effect在每次渲染的时候都会执行。这就是为什么 React 会在执行当前 effect 之前对上一个 effect 进行清除
// 3.并不是必须为 effect 返回的函数命名，上面命令是为了表明此函数的目的，可以返回一个箭头函数或者另一个名字
```

### 使用多个Effect 实现关注点分离

使用hook其中一个目的就是要解决class 中声明周期函数经常包含不相关的逻辑，但又把相关逻辑分离到了几个不同方法中的问题。上面代码是示例中计数器和好友状态指示器逻辑组合在一起的组件：

```jsx
class FriendStatusWithCounter extends React.Component{
    constructor(props){
        super(props);
        this.state = {
            count:0,
            isOnline:null
        }
        this.handleStatusChange = this.handleStatusChange.bind(this)
    }
    componentDidMount(){
        document.title = `You click ${this.state.count} times`
        ChatAPI.subscribeToFriendStatus(
            this.props.friend.id,
            this.handleStatusChange
        )
    }
    componentDidUpdate(){
        document.title = `You click ${this.state.count} times`
    }
    componentWillUnmount(){
        ChatAPI.unsubscibeFromFriendStatus(
            this.props.friend.id,
            this.handleStatusChange            
        )
    }
    handleStatusChange(status){
        this.setState({
            isOnline:status.inOnline
        })
    }
}

// 可以发现设置 documnet.title 的逻辑是如何被分割到 componentDidMount 和 componentDidUpdate中。订阅逻辑是被分割到 componentDidMount 和 componentWillUnmount 中。而且 componentDidMount 中包含了两个不同功能的代码
```

而使用 hook,跟使用多个 state 的 hook一样，可以使用多个 effect将不相关逻辑分离到不同的 effect中

```jsx
function FriendStatusWithCounter(props){
    const [count,setCount] = useState(0)
    useEffect(()=>{
        document.title = `You clicked ${count} times`
    })
    const [isOnline,setIsOnline] = useState(null)
    useEffect(()=>{
        function handleStatusChange(status){
            setIsOnline(status.isOnline)
        }
        ChatAPI.subscribeToFriendStatus(props.friend.id,handleStatusChange)
        return ()=>{
            ChatAPI.unsubcribeFromFriendStatus(props.friend.id,handleStatusChange)
        }
    })
}
// hook 允许我们按照代码的用途分离他们，而不是像生命周期函数那样，React 将按照 effect 声明的顺序依次调用组件中的每一个 effect
```

### 为什么每次更新的时候都要运行effect

为什么 effect 在每次重渲染都会执行，而不是在卸载组件的时候执行一次。

上述用于显示好友是否在线的 FriendStatus 组件，从 class 中 props 读取 friend.id，然后在组件挂载后订阅好友状态，并在卸载组件的时候取消订阅

```jsx
componentDidMount(){
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );        
}
componentWillUnmount(){
    ChatAPI.unsubscribeFromFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );    
}
// 这里会有一个问题，当组件已经显示在屏幕上时，friend props发生变化，我们的组件将继续展示原来的好友状态，这是一个 bug,而且我们还会因为取消订阅时错误使用错误的好友 ID,导致内存泄露或者奔溃的问题
// 所以通过添加 componentDidUpdate 来解决这个问题
componentDidUpdate(prevProps){
    // 取消之前的订阅的 friend.id
    ChatAPI.unsubscibeFromFriendStatus(
        prevProps.friend.id,
        this.handleStatusChange
    )
    // 订阅新的 friend.id
    ChatAPI.subscribeToFriendStatus(
        this.props.friend.id,
        this.handleStatusChange
    )
}
```

Hook版本

```jsx
function FriendStatus(props){
    useEffect(()=>{
        ChatAPI.subscribeToFriendStatus(props.friend.id,handleStatusChange)
        return ()=>{
            ChatAPI.unsubscibeFromFriendStatus(props.friend.id,handleStatusChange)
        }
    })
}
```

不需要特定的代码来处理更新逻辑，useEffect默认就会处理。它会在调用一个新的 effect 之前对前一个 effect 进行清理。下面按时间列出一个可能会产生的订阅和取消订阅调用序列

```jsx
// Mount with {friend:{id:100}} props
ChatAPI.subscibeToFriendStatus(100,handleStatusChange) // 运行第一个 effect
// Mount with {friend:{id:200}} props
ChatAPI.unsubscribeFromFriendStatus(100,handleStatusChange) // 清除上一个 effect
ChatAPI.subscibeToFriendStatus(200,handleStatusChange) // 运行下一个 effect
// Mount with {friend:{id:300}} props
ChatAPI.unsubscribeFromFriendStatus(200,handleStatusChange) // 清除上一个 effect
ChatAPI.subscibeToFriendStatus(300,handleStatusChange) // 运行下一个 effect
// unMount
ChatAPI.unsubscribeFromFriendStatus(300,handleStatusChange) // 清除上一个 effect
```

默认行为保证了一致性，避免了在 class 组件因为没有处理更新逻辑而导致常见 bug

### 通过跳过 effect 进行性能优化

在某些情况下，每次渲染后都会执行清理或者执行 effect 可能会导致性能问题。在 class 组件中，可以通过 componentDidUpate 中添加 prevProps 或者 prevState 的比较逻辑解决：

```jsx
componentDidUpdate(prevProps,prevState){
    if(prevState.count !== this.state.count){
        document.title = `You clicked ${this.state.count} times`
    }
}
```

这是很常见的需求，被内置到了 useEffect 的 hook api中，如果某些特定值在两次重渲染中没有发生变化，可以通过 React 跳过对 effect 的调用，只要传递数组作为 useEffect 的第二个参数即可：

```jsx
useEffect(()=>{
    document.title = `You clicked ${count} times`
},[count]) // 仅在 count 更改的时候更新
// 对于有清除操作的 effect同样适用
useEffect(()=>{
    function handleStatusChange(status){
        setIsOnline(status.isOnline)
    }
    ChatAPI.subscibeToFriendStatus(props.friend.id,handleStatusChange)
    return()=>{
        ChatAPI.unsubscibeFromFriendStatus(props.friend.id,handleStatusChange)
    }
},[props.friend.id]) // 仅在 props.friend.id 发生变化时，重新订阅
```

注意：

如果要使用这种优化方法，确保数组中包含了所有外部作用域中会随着时间变化并且在 effect 中使用的变量，否则代码会引用到先前渲染中的旧变量。

如果想只执行一次 effect 仅在组件挂载或者卸载时执行，可以传递一个空数组（[]），作为第二个参数，告诉 React 的 effect 不依赖于props 或者 state 中的任何值，所以它永远都不需要被重复执行，这不属于特殊情况，依然遵循数组的工作方式。

如果传入了一个空数组，effect 内部的 props 和 state 就会一直拥有其初始值。尽管传入的空数组作为第二个参数更加接近熟悉的 componentDidMount 和 componentWillUnMount 思维方式。React 会等待浏览器完成画面渲染后才会延迟调用 useEffect，因此会使得额外操作很方便。

 启用 [`eslint-plugin-react-hooks`](https://www.npmjs.com/package/eslint-plugin-react-hooks#installation) 中的 [`exhaustive-deps`](https://github.com/facebook/react/issues/14920) 规则。此规则会在添加错误依赖时发出警告并给出修复建议。 

## `Hook` 规则

Hook 本质就是 JS 函数，使用它需要遵循两条规则。 [linter 插件](https://www.npmjs.com/package/eslint-plugin-react-hooks)来强制执行这些规则：

### 只在顶层使用 `Hook`

不要在循环，条件或者嵌套函数中调用 Hook，确保总是在 React 函数的最顶层调用它们。遵循这条规则，就能确保 hook 在每一次渲染中都按照同样的顺序被执行.

### 只在 React 函数中使用 `Hook`

不要在普通的 JS 函数中调用 Hook，可以在React 函数组件中调用 Hook，在自定义 Hook 中调用其他 Hook

遵循以上规则，确保组件的状态逻辑在代码中清晰可见

### ESLINT 插件

  [`eslint-plugin-react-hooks`](https://www.npmjs.com/package/eslint-plugin-react-hooks) 的 ESLint 插件来强制执行这两条规则。如果你想尝试一下，可以将此插件添加到你的项目中： 

```shell
npm install eslint-plugins-react-hooks --save-dev
```

```json
// ESLint 的配置
{
    "plugins":[
        //...
        "react-hooks"
    ],
    "rules":{
        //...
        "react-hooks/rules-of-hooks":"error",// 检查 Hook 规则
        "react-hooks/exhaustive-deps":"warn" // 检查 effect 的依赖
    }
}
```

### 说明

单个组件中使用多个 State Hook或者 Effect Hook

```jsx
function Form(){
    const [name,setName] = useState('Mary')
    useEffect(function persisForm(){
        localStorage.setItem('formData',name)
    })
    
    const [surname,setSurname] = useState('Poppins')
    useEffect(function updateTitle(){
        document.title = name + ' ' +surname
    })
}
```

React 怎么知道哪个state 对应哪个 useState，答案是React靠Hook调用的顺序。

```jsx
// 首次渲染
useState('Mary') // 1.使用 Mary 初始化变量名为 name 的 state
useEffect(persistForm) // 2.添加 effect 以保存 form 操作
useState('Poppings') // 3.使用 Poppings 初始化变量名为 surname 的 state
useEffect(updateTitle) //4. 添加 effect 以更新标题

// 二次渲染
useState('Mary') // 5.读取变量名为 name 的 state (参数被忽略)
useEffect(persistForm) // 6.替换保存 form 的 effect
useState('Poppings') // 7.读取变量名为 surname 的 state(参数被忽略)
useEffect(updateTitle) //8. 替换更新标题的effect 
```

只要 Hook 的调用顺序在多次渲染中保持一致，React 就能正确将内部 state 和对应的 hook 进行关联。

```jsx
// 倘若将一个 hook 调用放入到一个条件语句中会发生什么
if(name !== ''){
    useEffect(function persistForm(){
        localStorage.setItem('formData',name)
    })
}
// 在第一次渲染中 name!== ''条件为true,所以会执行这个 hook，但是下一次渲染我们可能清空了表单，表达式为 false,此时渲染会跳过该hook，hook的调用顺讯发生了变化
useState('Mary') //1.读取变量名为 name 的 state(参数被忽略)
// useEffect(persistForm) // 此 hook 被忽略
useState('Poppins') // 2.(之前为3)。读取变量名为 surname的state 失败
useEffect(updateTitle) // 3.(之前为4)，替换更新标题的 effect失败
```

React 不知道第二个useState 的 Hook 应该返回什么，React 以为在该组件中第二个 Hook 的调用像上次渲染一样。对应的是 persistForm的 effect,但并非如此。从这里开始，后面的 Hook调用都被提前执行，导致了bug的产生。

这就是为什么 Hook 需要在我们组件的最顶层调用，如果要有条件地执行一个 effect，可以将判断放在 Hook 的内部

```jsx
useEffect(function persistForm(){
    // 将条件放置在 effect 中
    if(name === ''){
        localStorage.setItem('formData',name)
    }
})
```

## 自定义 `Hook`

通过自定义 Hook，可以将组件逻辑提取到可重用的函数中

```jsx
import React,{useState,useEffect} from 'react'

function FriendState(props){
    const [isOnline,setIsOnline] = useState(null)
    useEffect(()=>{
        function handleStatusChange(status){
            setIsOnline(status.isOnline)
        }
        ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
        return () => {
          ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
        };        
    })
    if(isOnline === null){
        return 'Loading...'
    }
    return isOnline ? 'Online' : 'Offline'
}
```

假设聊天应用中有一个联系人列表，当用户在线时需要把名字设置为 绿色，我们可以把上面类似的逻辑复制并粘贴到 FriendListItem 组件中，但这并不是理想的解决方案

```jsx
import React,{useState,useEffect} from 'react'

function FriendListItem(props){
    const [isOnline,setIsOnline] = useState(null)
    useEffect(()=>{
        function handleStatusChange(status){
            setIsOnline(status.isOnline)
        }
        ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
        return () => {
          ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
        };          
    })
    return(
        <li style={{color:isOnline?'green':'black'}}>
            {props.friend.name}
        </li>
    )
}
```

我们希望在FriendStatus以及FriendListItem 之间共享逻辑。

### 提取自定义 `Hook`

当我们想在两个函数之间共享逻辑时，会把它提取到第三个函数中，而组件和Hook都是函数，所以也使用这种方式

自定义Hook 是一个函数，名称以 use 开头，函数内部可以调用其他 hook，下面 useFriendStatus 就是定义的 Hook

```jsx
import React,{useState,useEffect} from 'react'

function useFriendStatus(friendID){
    const [isOnline,setIsOnline] = useState(null)
    useEffect(()=>{
        function handleStatusChange(status){
            setIsOnline(status.isOnline)
        }
        ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
        return () => {
          ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
        };          
    })
    return isOnline;
}
```

 此处 `useFriendStatus` 的 Hook 目的是订阅某个好友的在线状态。这就是我们需要将 `friendID` 作为参数，并返回这位好友的在线状态的原因。 



### 使用自定义 `Hook`

```jsx
function FriendStatus(props){
    const isOnline = useFriendStatus(props.friend.id)
    if(isOnline === null){
        return 'Loading...'
    }
    return isOnline ? 'Online' : 'Offline'
}

function FriendListItem(props){
    const isOnline = useFriendStatus(props.friend.id)
    return(
        <li style={{color:isOnline?'green':'black'}}>
            {props.friend.name}
        </li>
    )
}
```

  **代码等价于原来的示例代码** ,**自定义 Hook 是一种自然遵循 Hook 设计的约定，而并不是 React 的特性。** 

 **自定义 Hook 必须以 “`use`” 开头** 

 **在两个组件中使用相同的 Hook 不会共享 state** 

 **自定义 Hook  每次*调用*  都会获取独立的 state**

### 多个 `Hook`之间传递信息

由于 Hook 本身就是函数，因此我们可以在它们之间传递信息。

将使用聊天程序中的另一个组件来说明这一点，这是一个聊天消息接收者的选择器。会显示当前的好友是否在线

```jsx
const friendList = [
    {id:1,name:'Phoebe'},
    {id:2,name:'Rachel'},
    {id:3,name:'Ross'}
]
function ChatRecipientPicker(){
    const [recipientID,setRecipientID] = useState(1)
    const isRecipientOnline = useFriendStatus(recipientID)
    return(
        <>
        	<Circle color={isRecipientOnline ? 'green' : 'red'}/>
        	<Select 
                value={recipientID}
                onChange={e=>setRecipientID(Number(e.target.value))}
             >
            {friendList.map(friend=>(
                <option key={friend.id} value={friend.id}>
                    {friend.name}
                </option>
            ))}
        	</Select>
        </>
    )
}
```

将当前选择的好友ID保存在 recepientID 状态变量中，并在用户从 Select 中选择其他好友时更新这个state

由于 useState 提供了 recipientID 状态变量的最新值，我们可以将它作为参数传递给自定义的 useFriendStatus Hook

```jsx
const [recipientID,setRecipientID] = useState(1)
const isRecipientOnline = useFriendStatus(recipientID)
```

当我们选择不同的好友并更新 recipientID 状态变量时，useFriendStatusHook 将会取消订阅之前选中的好友，并订阅新选中的好友状态



## `Hook API`索引

### 基本`Hook`

**useState**

```jsx
const [state,setState] = useState(initialState)
```

返回一个state,以及更新 state 的函数。在初始渲染期间，返回的状态（state）与传入的第一个参数(initialState)值相同。

`setState`函数用于更新state,它接收一个新的state值并将组件的一次重新渲染加入队列

```jsx
setState(newState);
```

后续渲染中，useState 返回的第一个值始终是更新后最新的 state。React会确保 setState 函数的标识是稳定的，并且不会在组件重新渲染时发生变化，这就是为什么可以安全地从 useEffect 或 useCallback 的依赖列表中省略 setState



