---
title: Dva 学习笔记 (下)
date: 2018-07-10 22:17:12
tags: dva
categories: 
	- react相关
---
`dva` 学习笔记
===========

[`dva` 学习笔记 代码](https://github.com/LbhFront-end/learn-dva)

使用 `Dva` 开发复杂 `SPA`
-------------------------

动态加载 `model`
-------------------

利用 `webpack` 的 `require.ensure` 来实现 `model` 的动态加载

```react
function RouterConfig({ history, app}) {
  const routes = [
    {
      path: '/',
      name: 'IndexPage',
      getComponent(nextState, cb) {
        require.ensure( [], (require) => {
          registerModel(app,require('./models/dashboard'));
          cb(null, require('./routers/IndexPage'));
        })
      }
    },
    {
      path:'/users',
      name:'UsersPage',
      getComponent(nextState, cb) {
        require.ensure(nextState, cb) {
          require.ensure( [],(require) =>{
            registerModel(app, require('./models/users'));
            cb(null, require('./routes/Users'))
          })
        }
      }
    }
  ];
  return <Router history = { history } routes = { routes } />;
}
```

这样，在视图切换到这个路由的时候，对应的 `model` 就会被加载。同理，也可以做 `model` 的动态移除，不过，一般情况下是不需要移除的

使用 `model` 共享全局信息
------------------------

`model` 可以动态加载，也可以移除。从这个角度看，`model` 是可以有不同生命周期的，有些可以与功能视图伴随，而有些可以贯穿整个应用的生命周期。

从业务逻辑看，不少场景是可以做全局的 `model` ，例如我们在路由之间的前进后退， `model` 可以用于在路由间共享数据，比较典型的，像列表页和详情页的互相转换，可以用同一份 `model` 去共享他们的数据

注意，如果当前应用中记载了不止一个 `model` ，在其中一个 `effect` 里做 `select` 操作，是可以获取另外一个 `state`的

```react
*foo(action, { select }) {
  const { a, b} = yield select();
}
```

`model` 的复用
--------------

有时候，业务上可能会遇到期望把一些外部关联比较少的 `model` 拆出来的需求，可能会拆出来一个 `model` ，然后用不同的视图容器去 `connect` 它

```react
export default {
  namespace: 'reusable',
  state: {},
  reducers: {},
  effrcts: {}
}
```

所以，在业务上，可能出现的使用情况就是：

```react
              ContainerA <-- ModelA
                   |
    ------------------------------
    |                            |
ContainerB <-- reusable     ContainerC <-- reusable
```

这里面，`ContainerB` 和 `ContainerC` 是 `ContainerA` 的下属，它们的逻辑结构一致，只是展现不同。我们可以让它们分别 `connect` 同一个 `model` ，注意，这个时候，`model` 的修改会同时影响到两个视图，因为 `model` 在 `state` 中是直接以 `namespace` 作 `key` 存放的，实际上只有一份实例

动态扩展 `model`
-------------------

可能会遇到的业务逻辑： 几个业务视图长得差不多， `model` 也存在少量的差别

在这种情况下可以复用同一个 `model` ，但这么做，对于维护是一种挑战，很可能改变其中一个，对另外一个造成了影响，所以在这种情况下，扩展是比较好的选择

扩展要做的事情：  
新增一些东西  
覆盖一些原来的东西  
根据条件动态创建一些东西  

`dva` 中的每个 `model` ，实际上都是普通的 `JavaScript` 对象，包含 

`namespace`  
`state`  
`reducers`  
`effects`  
`subscription`

从这个角度上看，我们要新增或者覆盖的一些东西，都会比较容易。比如使用 `Object.assign` 来进行对象的复制属性，就可以把新的内容添加或者覆盖到原有的对象上。

注意这里有两级，`model` 结构中的 `state`，`reducers`，`effects`，`subscriptions`都是对象结构，需要分别在这一级去做 `assign`。

```react
function reacteModel(options) {
  const { namespace, param } = options;
  return {
    namespace: `demo${namespace}`,
    states: {},
    reducers: {},
    effects: {
      *foo() {
        // 这里可以根据param来确定下面这个call的参数
        yield call()
      }
    }
  }
}

const modelA = createModel({ namespace: 'A', param :{ type: 'A'}});
const modelA = createModel({ namespace: 'B', param :{ type: 'B'}});
```

长流程的业务逻辑
----------------

例如复杂的表单提交，中间会需要去发起多种对视图状态的操作

```react
*submit(action, {put,call,select}) {
  const formData = yield select(state => {
    const buyModel = state.buy;
    const context = state.context;
    const { stock } = buyModel;
    return {
      uuid: context.uuid,
      market: stock && stock.code,
      stockName: stock && stock.name,
      price: String(buyModel.price),

      //委托数量
      entrustAmount: String(buyModel.count),
      totalBalance: buyModel.totalBalance,
      availableTzbBanlance: buyModel.availableTzbBalance,
      availableDepositBalance: buyModel.availableDepositBalance,
    }
  });

  const result = yield call(post, '/h5/entrust_buy',formDate, {loading: true});

  if(result.success){
    toast({
      type: 'success',
      content: '委托已受理',
    });

    // 成功之后再获取一次现价，并填入
    // yield put({type: 'fetchQuotation', payload: stock});
    yield put({ type: 'entrustNoChange', payload: result.result && result.result.entrustNo });

    // 清空输入框内容
    yield put({ type: 'searchQueryChange', value: '' });

    // 403时，需要验证密码再重新提交
    if (!result.success && result.resultCode === 403) {
      yield put({ type: 'checkPassword', payload: {} });
      return;
    }

    // 失败之后也需要更新投资宝和保证金金额
    if (result.result) {
      yield put({ type: 'balanceChange', payload: result.result });
    }

    // 重新获取最新可撤单列表
    yield put({ type: 'fetchRevockList' });

    // 返回的结果里面如果有uuid, 用新的uuid替换
    if (result.uuid) {
      yield put({ type: 'context/updateUuid', payload: result.uuid });
    }

  }
}
```

在一个 `effect` 中可以使用多个 `put` 来分别调用 `reducer` 来更新状态  
存在另外一些流程，在 `effect` 中可能会存在多个异步的服务调用，比如说，要调用一次服务端的验证，成功之后再去提交数据，这时候在一个 `effect` 中就会存在多个 `call` 操作了

使用 `take` 操作进行事件监听
---------------------------

一个流程的变动，需要扩散到若干其他 `model` 中，在 `redux-sage` 中提供了 `take` 和 `takeLatest` 这两个操作， `dva` 是 `redux-saga` 的封装，也是可以用这种操作

假设我们有一个事件处理的代码  

> someSource.on( 'click', event => doSomething(event))

这段代码转成 `generator` 来表达，就是下面的这个形式

```react
function* sage() {
  while(true) {
    const event = yield take('click');
    doSomething(event)
  }
}
```

我们 可以在 `dva` 中使用 `take` 操作来监听 `action`

多任务调度
--------------

上述大多都是串行执行方式，这是业务中最常见的多任务执行方式，只需逐个 `yield call` 就可以了

有时候我们可能希望多个任务以外一些方式执行

并行： 若干个任务之间不存在依赖关系，并且后续操作对它们的结果无依赖  
竞争： 若干个任务之间，只要有一个执行完成，就进入下一个环节  
子任务： 若干个任务，并行执行，但必须全部做完之后，下一个环节才继续执行

任务的并执行
---------------

可以通过以下方式

```react
const [result1, result2] = yield [
  call(service1, param1),
  call(service2, param2)
]
```

把多个要并行执行的东西放在一个数组里，就可以并行执行，等所有的都结束后进行下一个环节，类似 `promise.all` 操作，一般有些集成界面，比如 `dashboard` ，其中各组件之间业务的管理较小，就可以用这种方式去分别加载数据，此时，整体加载时间只取决于时间最长的那个

如果把 `yield []` 误写成 `yield* []` 就会顺序执行

任务的竞争
-------------

多任务的竞争关系，可以通过以下方式

```react
const { data, timeout } = yield race({
  data: call(server, 'some data'),
  timeout: call(delay, 1000)
});

if(data){
  put({ type:'DATA_RECEIVED', data});
}else{
  put({ type:'TIMEOUT_ERROR'});
}
```

跨 `model` 的通信
-----------------

在业务复杂的情况下，我们可能会对 `model` 进行拆分，往往又会遇到一些比较复杂的事情  

一个流程贯穿整个 `model` 

父容器A，子容器B，二者各自 `connect` 了不同的 `model A` 和 `B`  
父容器中有一个操作，分三个步骤：  
`model A` 中某个 `effect` 处理第一步  
`call model B` 中的某个 `effect` 去处理第二步  
第二步结束后，再返回 `model A` 中做第三步  
在 `dva` 中，可以用 `namespace` 去指定接受 `action` 的 `model ，所以可以通过类似这样的方式去组合：

```react
yield call({ type: 'a/foo' });
yield call({ type: 'b/foo' });
yield call({ type: 'a/bar' });
```

甚至，还可以利用 `take` 命令，在另外一个 `model` 的某个 `effect` 中插入逻辑：

```react
*effectA() {
  yield call(service1);
  yield put({ type: 'service1Success' });
  // 如果我们复用这个effect，但要在这里加一件事，怎么办？
  yield call(service2);
  yield put({ type: 'service2Success' });
}
```

可以利用之前我们说的 `take` 命令：

> yield take('a/service1Success');

这样，可以在外部往里面添加一个并行操作，通过这样的组合可以处理一些组合流程。但实际情况下，我们可能要处理的不仅仅是 `effect` ，很可能视图组件中还存在后续逻辑，在某个 `action` 执行之后，还需要再做某些事情。

比如：

```react
yield call({ type: 'a/foo' });
yield call({ type: 'b/foo' });
// 如果这里是要在组件里面做某些事情，怎么办？
```

可以利用一些特殊手段把流程延伸出来到组件里。比如说，我们通常在组件中 `dispatch` 一个 `action` 的时候，不会处理后续事情，但可以修改这个过程：

```react
new Promise((resolve, reject) => {
  dispatch({ type: 'reusable/addLog', payload: { data: 9527, resolve, reject } });
})
.then((data) => {
  console.log(`after a long time, ${data} returns`);
});
```

注意这里，我们是把 `resolve` 和 `reject` 传到 `action` 里面了，所以，只需在 `effect` 里面这样处理：

```react
try {
  const result = yield call(service1);
  yield put({ type: 'service1Success', payload: result });
  resolve(result);
}
catch (error) {
  yield put({ type: 'service1Fail', error });
  reject(ex);
}
```

这样，就实现了跨越组件、模型的复杂的长流程的调用。