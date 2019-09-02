---
title: Dva 学习笔记 (上)
date: 2018-07-06 17:45:00
tags: dva
categories: 
	- react相关
---
`dva` 学习笔记
===========

[`dva` 学习笔记 代码](https://github.com/LbhFront-end/learn-dva)

安装 `dva-cli`
----------------

> npm install dva-cli -g

创建新应用
---------------

> dva new dva-quickstart

这会创建 `dva-quickstart` 目录，包含项目初始化目录和文件，并提供开发服务器，构建脚本，数据 `mock` 服务，代理服务器等功能

然后我们 `cd` 进入 `dva-quickstart` 目录，并启动开发服务器：

> cd dva-quickstart  
npm start

使用 `antd`
----------------

通过 `npm` 安装 `antd` 和 `babel-plugin-import` 。`babel-plugin-import` 是用来按需加载 `antd` 的脚本和样式的

> npm install antd babel-plugin-import --save

编辑 `.webpackrc`，使 `babel-plugin-import` 插件生效。

```bash
{
+  "extraBabelPlugins": [
+    ["import", { "libraryName": "antd", "libraryDirectory": "es", "style": "css" }]
+  ]
}
```

定义路由
------------------

们要写个应用来先显示产品列表。首先第一步是创建路由，路由可以想象成是组成应用的不同页面。

新建 `route component routes` / `Products.js`  添加路由信息到路由表，编辑 `router.js`

`Products.js`

```react
import React from 'react';
const Products = (props) => <h2>List of Products</h2>
```

export default Products;

```react
`router.js`
import React from 'react';
import { Router, Route, Switch } from 'dva/router';
import IndexPage from './routes/IndexPage';
import Products from './routes/Products';
function RouterConfig({ history }) {
  return (
    <Router history={history}>
      <Switch>
        <Route path="/" exact component={IndexPage} />
        <Route path="/products" exact component={Products} />
      </Switch>
    </Router>
  );
}

export default RouterConfig;
```

编写 `UI Component`
--------------------

随着应用的发展，你会需要在多个页面分享 `UI` 元素 (或在一个页面使用多次)，在 `dva` 里你可以把这部分抽成 `component` 。

我们来编写一个 `ProductList component`，这样就能在不同的地方显示产品列表了。

新建 `components/ProductList.js` 文件：

```react
import React from 'react';
import PropTypes from 'prop-types';
import { Table, Popconfirm, Button } from 'antd';

const ProductList = ({ onDelete, products }) => {
  const columns = [{
    title: 'Name',
    dataIndex: 'name',
  }, {
    title: 'Actions',
    render: (text, record) => {
      return (
        <Popconfirm title="Delete?" onConfirm={() => onDelete(record.id)}>
          <Button>Delete</Button>
        </Popconfirm>
      );
    },
  }];
  return (
    <Table
      dataSource={products}
      columns={columns}
    />
  );
};

ProductList.propTypes = {
  onDelete: PropTypes.func.isRequired,
  products: PropTypes.array.isRequired,
};

export default ProductList;
```

定义 `Model`
----------------

完成 `UI` 后，现在开始处理数据和逻辑。

`dva` 通过 `model` 的概念把一个领域的模型管理起来，包含同步更新 `state` 的 `reducers`，处理异步逻辑的 `effects`，订阅数据源的 `subscriptions` 。

新建 `model models`/`products.js` ：

```react
export default {
  namespace: 'products',
  state: [],
  reducers: {
    'delete'(state, { payload: id }) {
      return state.filter(item => item.id !== id);
    },
  },
};
```

这个 `model` 里：

`namespace` 表示在全局 `state` 上的 `key`
`state` 是初始值，在这里是空数组
`reducers` 等同于 `redux` 里的 `reducer` 接收 `action`，同步更新 `state`
然后别忘记在 `index.js` 里载入他：

`connect` 起来
--------------

这里，我们已经单独完成了 `model` 和 `component`，那么他们如何串联起来呢?

`dva` 提供了 `connect` 方法。如果你熟悉 `redux`，这个 `connect` 就是 `react-redux` 的 `connect` 。

编辑 `routes`/`Products.js`，替换为以下内容：

```react
import React from 'react';
import { connect } from 'dva';
import ProductList from '../components/ProductList';

const Products = ({ dispatch, products }) => {
  function handleDelete(id) {
    dispatch({
      type: 'products/delete',
      payload: id,
    });
  }
  return (
    <div>
      <h2>List of Products</h2>
      <ProductList onDelete={handleDelete} products={products} />
    </div>
  );
};

// export default Products;
export default connect(({ products }) => ({
  products,
}))(Products);
```

最后，我们还需要一些初始数据让这个应用 `run` 起来。编辑 `index.js`：

```react
- const app = dva();
+ const app = dva({
+   initialState: {
+     products: [
+       { name: 'dva', id: 1 },
+       { name: 'antd', id: 2 },
+     ],
+   },
+ });
```

`Dva` 概念
-------------

数据的改变发生通常是通过用户交互行为或者浏览器行为（如路由跳转等）触发的，当此类行为会改变数据的时候可以通过 `dispath` 发起一个 `action` ，如果是同步行为就会直接通过 `Reducers` 改变 `state` ,如果是异步行为（副作用）会先触发 `Effects` 然后流向 `Reducers` 最终改变 `State` ，所以在 `dva` 中，数据流向非常清晰简明，并且思路基本跟开源社区保持一致  

`Models`
-----------------

### `State`

> type State = any

`State` 表示 `Model` 的状态数据，通常表现为一个 `javascript` 对象（可以为任何值），操作的时候每次都要当作不可变数据来对待，保证每次都是全新对象，没有引用关系，这样才能保证 `State` 的独立性，便于测试和追踪变化

在 `dva` 中可以通过 `dva` 的实例属性 `_store` 看到顶部的 `state` 数据，但是通常会很少用到

> const app = dva();  
console.log(app._store); // 顶部的 state 数据

### `Action`

> type AsyncAction = any

`Action` 是一个普通 `javascript` 对象，它是改变 `State` 的唯一途径。无论是从 `UI` 事件、网络回调还是 `WebSocket` 等数据源获得的数据，最终都会通过 `dispatch` 函数调用一个 `action`,从而改变对应的数据。 `action` 必须带有 `type` 属性指明具体的行为，其他字段可以自定义，如果要发起一个 `action` 需要使用 `dispatch` 函数，需要注意的是 `dispatch` 是在组件 `connect Models` 后，通过 `props` 传入的

> dispatch({ type : 'add' })

### `dispatch` 函数

> type dispatch = (a : Action) => Action

`dispatching function` 是一个用于触发 `action` 的函数， `action` 是改变 `State` 的唯一途径，但是它只描述了一个行为，而 `dispatch` 可以看做是触发这个行为的方式，而 `Reducer` 则是描述如何改变数据的

在 `dva` 中。 `connect Models` 的组件通过 `props` 可以访问到 `dispatch` ，可以调用 `Model` 中的 `Reducer` 或者 `Effect` ，常见的形式如下

> dispatch({ type : 'user/add', payload : {} //需要传递的信息 }) //如果在 `model` 外调用，需要添加 `namespace`

### `Reducer`

> type Reducer<S, A> = (state: S, action: A) => S

`Reducer` 也称为 `reducing function` 函数接受两个参数：之前已经积累运算的结果和当前要被积累的值，返回的是一个新的积累的结果。该函数把一个集合归并成一个单值。

`Reducer` 的概念来自是函数式编程，很多语言中都有 `reduce API`

```react
[{x:1},{y:2},{z:3}].reduce(function(prev, next){
  return Object.assign(prev, next);
})

//return {x:1, y:2, z:3}
```

在 `dva` 中， `reducers` 聚合积累的结果时间当前 `model` 的 `state` 对象。通过 `actions` 中传入的值，与当前 `reducers` 中的值进行运算获得新的值（也就是新的 `state`）。需要注意的是 `Reducer` 必须是纯函数，所以同样输入必然得到同样的输出，它们不应该产生任何副作用。并且， 每一次计算都应该使用 `immutable date`,这种特征简单理解就是每次操作都是返回一个全新的数据（独立、纯净），所以热重载和时间旅行这些功能才能够使用

### `Effect` 

`Effect` 被称为副作用，在我们的应用中，最常见的就是异步操作，它来自于函数编程的概念，之所以叫副作用的原因是它使得我们的函数变得不纯，同样的输入不一定是同样的输出

`dva` 为了控制副作用的操作，底层引入了 `redux-sagas` 做异步流程控制，由于采用了 `generator` 的相关概念，所以将异步转成同步写法，从而将 `effect` 转为了纯函数。

### `Subscirption` 

`Subscription` 是一种从源获取数据的方法，它来自于 `elm`

`Subscription` 语义是订阅，用于订阅一个数据源，然后根据条件 `dispatch` 需要的 `action` 。数据源可以当前的世界，服务器的 `websocket` 连接， `keyboard` 输入，`geolocation` 变化，`history` 路由变化等等

```react
import key from 'keymaster';
app.model({
  namespace:'count',
  subscriptions:{
    keyEvent({dispatch}){
      key('⌘+up, ctrl+up', () => { dispatch({type:'add'}) })
    }
  }
})
```

Router
---------------

这里的路由是指前端路由，由于我们的应用通常是单页应用，所以需要前端代码来控制路由逻辑，通过浏览器提供的 `History Api` 可以监听到浏览器 `url` 的变化，从而控制路由相关操作

`dva` 实例提供了 `router` 方法来控制路由，使用的是 `react-router` 

```react
import { Router, Route } from 'dva/router';
app.router( ({history}) =>
 <Router history = {history}>
    <Route path="/" componet = {HomePage}>
 </Router>
 );
```

### `Router Components`

`Container Components` 在 `dva` 中我们通常将其约束为 `Route Component` ,因为在 `dva` 中是以也页面维度来设计 `Components`

`dva` 中，通常需要 `connect Model` 的组件都是 `Route Components`,组织在 `/routers/` 目录下，而 `/compoents/` 目录下的则是纯组件