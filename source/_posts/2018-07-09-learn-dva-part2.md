---
title: Dva 学习笔记 (中)
date: 2018-07-09 20:10:45
tags: dva
categories: 
	- react相关
---
`dva` 学习笔记
===========

[`dva` 学习笔记 代码](https://github.com/LbhFront-end/learn-dva)

3.入门课
--------------

## `React` 没有有解决的问题

`React` 本身只是一个 `DOM` 的抽象层，使用组件构建虚拟 `DOM`  
开发大应用要解决的问题  
通信：组件之间如何通信  
数据流：数据如何和视图串联起来？ 路由和数据如何绑定？ 如何编写异步逻辑？

## 通信问题

组件会发生三种通信  
想子组件发消息  
向父组件发消息  
向其他组件发消息  

`React` 只提供了一种通信手段：传参  

### 组件通信的例子

`step 1`  
父组件拿到子组件的值

```react
class Son extends React.Component {
  render(){
    return <input />;
  }
}
class Father extends React.Compoent {
  render() {
    return <div>
      <Son />
      <p> 这里显示 Son 组件的内容
    </div>；
  }
}
ReactDOM.render(<Father>,mountNode);
```

`step 2`  
子组件通过父组件传入的函数，将自己的值再传回父组件

```react
class Son extends React.Component {
  render() {
    return <input onChange = { this.props.onChange }>
}
class Father extends React.Component {
  constructor() {
    super();
    this.state = {
      son: ""
    }
    this.changeHandler = this.changeHandler.bind(this);
  }
  changeHandler(e){
    this.SetState({ son: e.target.value });
  }
  render() {
    return <div>
      <Son onChange = { changeHandler }>
      <p>这里展示 Son 组件的内容 { this.state.son }</p>
    </div>
  }
}
```

## 数据流问题

数据流方案  
`Flux` ，单项数据流方案， `Redux` 为代表  
`Reactive` ,响应式数据流方案 `Mobx` 为代表  

### 流行的数据流方案

路由： `React-Router`  
架构： `Redux`  
异步操作： `Readux-saga`  
缺点：引入多个库，项目结构复杂  

### `dva`  是什么

`dva` 是体验技术部开发的 `React` 应用框架，将上面三个 `React` 工具库包装在一起，简化了  `API` ,让开发 `React` 应用更加快捷和方便  
`dva = React-Router + Redux + Readux -saga

### `dva` 应用的最简结构

```react
import dva from 'dva';
const App = () => <div> Hello dva</div>;

//创建应用
const app = dva();

//注册视图
app.router( () => <App/>)

//启动应用
app.start('#root');
```

### 数据流图

`State` ===(**connect**)== `View` ==(**dispatch**)== `Action` == `State`

`State` : 一个对象，保存这个应用状态  
`View` : `React` 组件构成的视图层  
`Action` : 一个对象，描述事件  
`connect`方法 : 一个函数，绑定 `State` 到 `View`  
`dispatch` 方法 : 一个函数，发送 `Action` 到 `State` 

### `State` 和 `view` 

`State` 是存储数据的地方，收到 `Action` 后，就会更新数据  
`View` 是 `React` 组件构成的 `UI` 层，从 `State` 获取到数据后，就渲染成 `HTML` 代码。只要 `State` 有变化， `View` 就会自动更新

## Action

`Action` 是用来描述 `UI` 层事件的一个对象  

> { type:'click-submit-button', payload: this.form.data }

### `connect` 方法

`connect` 是一个函数，绑定 `State` 到 `View` 

```react
import { connect } from 'dva'

function mapStateToProps(state) {
  return { todos: state.todos };
}
connect(mapStateToProps)(App);
```

`connect` 方法返回的也是一个 `React` 组件，通常称为容器组件，因为它是原始的 `UI` 组件的容器，即在外面包了一层 `State`  
`connect` 方法传入的第一个参数是 `mapStateToProps` 函数， `mapStateToprops` 函数会返回一个对象，用于建立 `State` 到 `Props` 的映射关系

### `dispatch` 方法

`dispatch` 是一个函数方法，用来将 `Action` 发送给 `State`

```react
dispatch({ type:'click-submit', payload: this.form.data })
```

被 `connect` 的 `Component` 会自动在 `props` 中拥有 `dispatch` 方法

`dva` 应用的最简结构（带 `model`）
-------------------------------

```react
// 创建应用
const app = dva();

// 注册 Model
app.model({
  namespace: 'count',
  state: 0,
  reducers: {
    add(state) { return state + 1},
  },
  effects: {
    *addAfterSecond(action, (call, put)){
      yield call(delay,1000);
      yield put({ type: 'add'});
    }
  }
});

// 注册视图
app.router( () => <ConnectedApp />);

// 启动应用
app.start('#root');
```

### `app.model`

`dva` 提供 `app.model` 这个对象，所有应用逻辑都定义在它上面

```react
const app = dva();

// 新增这一行
app.model({ /**/});

app.router( () => <App/>);
app.start('#root');
```

### `Model` 对象的例子

```react
{
  namespace: 'count',
  state: 0,
  reducers: {
    add(state) { return state + 1 },
  },
  effects: {
    *addAfter1Second(action, { call,put }) {
      yield call(delay,1000);
      yield put({ type: 'add'});
    }
  }
}
```

### `Model` 对象的属性

`namespace`: 当前 `Model` 的名称，整个应用的 `State` ，由多个小的 `Model` 的 `State` 以 `namespace` 为 `key` 合成  
`state`: 该 `Model` 当前的状态。数据保存在这里，直接决定了视图层的输出  
`reducers`: `Action` 处理器，处理同步动作，用来算出最新的 `state`  
`effects`: `Action` 处理器，处理异步动作

#### `Reducer` 
`Reducer` 是 `Action` 处理器，用来处理同步操作，可以看做是 `state` 的计算器，它的作用是根据 `Action` ，从上一个 `State` 算出当前的 `State`  
例子：

```react
// count + 1
function add(state) { return state + 1; }

//往 [] 里添加一个新的 todo
function addTodo(state, action){ return [...state, action.payload]; }

//往 { todos: [], loading: true } 里添加一个新的 todo,并标记 loading 为 false
function addTodo(state, action){
  return {
    ...state,
    todos: state.todos.concat(action.payload),
    loading: false
  }
}
```

#### `Effect` 

`Action` 处理器，处理异步动作，基于 `Redux-sga` 实现。 `Effect` 指的是副作用。根据函数式编程，计算以外的操作都属于 `Effect` ，典型的就是 `I/O` 操作，数据库读写

```react
function *addAfter1Second(action. { put, all}) {
  yield(delay, 1000);
  yield put({ type: 'add'});
}
```

### `Generator` 函数

`Effect` 是一个 `Generator` 函数，内部使用 `yield` 关键字，标识每一步的操作 （无论同步或者是异步）

### `call` 和 `put`

`dva` 提供多个 `effect` 函数内部的处理函数，比较常用的是 `call` 和 `put`

 总例子
 -----------

```react
app.model({
 namespace: 'count';
 state: {
 record: 0,
 current: 0,
 },
 reducers: {
  add(state) {
    const newCurrent = state.current + 1;
    return {
      ...state,
      record: newCurrent > state.record ? newCurrent: state.record,
      current: newCurrent,
    };
  },
  minus(state) {
    return { ...state, current: state.current - 1};
  },
 },
 effects: {
  *add(action ,{ call ,put}){
    yield call( delay ,1000);
    yield put({ type: 'minus'});
  },
 },
 subscriptions: {
  keyboardWatcher({ dispatch }) {
    key( '⌘+up, ctrl+up', () => { dispatch({ type:'add'})});
  }
 }
})
```
