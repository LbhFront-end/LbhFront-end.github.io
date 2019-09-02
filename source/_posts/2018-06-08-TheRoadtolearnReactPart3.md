---
title: The Road to learn React Part3
date: 2018-06-08 11:02:45
tags: react
categories: 
	- react相关
---
The Road to learn React书籍学习笔记(第三章)
===========

[代码详情](https://github.com/LbhFront-end/react-pratice/tree/master/my-first-react-app)

声明周期方法
----------------

通过之前的学习，可以了解到ES6 类组件中的生命周期方法 `constructor()` 和 `render()`  
`constructor()` 构造函数只有在组件实例化并插入到 `DOM` 中的时候才会被调用。组件实例化的过程称为组件的挂载 `mount`
`render()`方法也会在组件挂载过程中被调用，同时组件更新的时候也会被调用。每当组件的状态 `state` 和属性 `props` 改变的时候，组件的 `render()` 方法都会被调用

### 挂载过程中有四个生命周期方法，调用顺序是这样的  

`constructor()`  
`componentWillMount()`  
`render()`  
`componentDidMount()`

### 当组件的状态或者属性改变的时候用来更新的生命周期如下五个步骤  

`componentWillReceiveProps()`  
`shouldComponentUpdate()`  
`componentWillUpdate()`  
`render()`  
`componentDidUpdate()`  

### 组件卸载的生命周期只有一个

`componentWillUnmount()`

### 生命周期详解

`constructor(props)`  
在组件初始化的时被调用，在这个方法中，可以设置初始化状态以及绑定类方法

`componentWillMount()`  
在 `render()` 方法之前被调用，这就是为什么它可以用作去设置组件内部的状态，因为它不会触发组件的再次渲染，一般还是推荐在 `constructor()` 中去初始化状态

`componentDidMount()`  
仅在组件挂载后执行一次，是发起异步请求去API 获取数据的最好时期，获取到的数据将被保存在内部组件的状态中然后在 `render()` 生命周期方法中展示出来

`componentWillReceviceProps(nextProps)`  
这个方法在一个更新声明周（`update lifecycle`）中使用， 新的属性会作为它的输入，因此可以利用 `this.props` 来对比之后的属性和之前的属性，基于对比的结果去实现不同的行为，此外还可以基于新的属性来设置组件的状态

`shouldComponentUpdate(nextProps,nextState)`  
每次组件因为状态或者是属性更改而更新时，它都会被调用。在成熟的React应用中使用它来进行性能优化。在一个更新声明周期中，组件以及其组件将根据该方法返回的布尔值来决定是否重新渲染，这样就可以阻止组件的渲染声明周期方法，避免不必要的渲染

`componentWillUpdate(nextProps,nextState)`  
这个方法是 `render()`  方法执行之前的最后一个方法，此时拥有了下一个属性和状态，可以利用这个方法在渲染之前做最后的准备。注意这个声明周期不能再触发 `setState()` 如果想基于新的属性计算状态 ,必须使用 `componentWillReceiveProps()`

`componentDidUpdate(prevProps,prevState)`  
这个方法在 `render()` 之后调用，可以用它当成操作DOM 或者是 执行更多异步请求的机会

`componentWillUnmount()`  
它会在组件销毁之前被调用，可以利用这个声明周期方法去执行任何清理任务

`render()` 是必须有的，否则不会返回一个组件实例

### 还有一个生命周期方法

`componentDidCatch(error,info)`  
在 `React 16` 中引入，用来捕获组件的错误。  
举例来说，在你的应用中展示样本数据本来是没问题的。但是可能会有列表的本地状态被意外设置成 `null` 的情况发生（例如从外部 `API` 获取列表失败时，你把本地状态设置为空了）。然后它就不能像之前一样去过滤（`filter`）和映射（`map`）这个列表，因为它不是一个空列表（[]）而是 `null`。这时组件就会崩溃，然后整个应用就会挂掉。现在你可以用`componentDidCatch()` 来捕获错误，将它存在本地的状态中，然后像用户展示一条信息，说明应用发生了错误

获取数据
---------------

从 `Hacker News API` 获取数据。可以在 `componentDidMount()` 生命周期方法来获取数据,用JavaScript原生的 `fetch API` 来发起请求  
在那之前，先设置好 `URL`常量和默认参数，将 `API` 请求分解成几步

```react
import React,{ Component } from 'react';
import './App.css'

const DEFAULT_QUERY = 'redux';
const PATH_BASE = 'https://hn.algolia.com/api/v1';
const PATH_SEARCH = '/search';
const PARAM_SEARCH = 'query=';

在ES6 中 可以用模板字符串（`template strings`） 来连接字符串

//ES6 
const url = `${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${DEFAULT_QUERY}`;
//ES5 
var url = PATH_BASE + PATH_SEARCH +'?' + PARAM_SEARCH + DEFAULT_QUERY;
```

```react
...

class App extends Component {

constructor(props) {
  super(props);

  this.state = {
    result: null,
    searchTerm: DEFAULT_QUERY,
  };

  this.setSearchTopStories = this.setSearchTopStories.bind(this);
  this.fetchSearchTopStories = this.fetchSearchTopStories.bind(this);
  this.onSearchChange = this.onSearchChange.bind(this);
  this.onDismiss = this.onDismiss.bind(this);
}

setSearchTopStories(result) {
  this.setState({ result });
}

fetchSearchTopStories(searchTerm) {
  fetch(`${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${searchTerm}`)
    .then(response => response.json())
    .then(result => this.setSearchTopStories(result))
    .catch(e => e);
}

componentDidMount() {
  const { searchTerm } = this.state;
  this.fetchSearchTopStories(searchTerm);
}

...
}
```

从 `Hacker News API` 得到一个真实的列表。组件将一个空的列表结果以及一个默认的搜索词作为初始状态。这个默认搜索词也同样用在 `Search` 组件的输入字段和第一个 API 请求中。

其次，在组件挂载之后，它用了 `componentDidMount()`生命周期方法去获取数据。在第一次获取数据时，使用的是本地状态中的默认搜索词。它将获取与 “`redux`” 相关的资讯，因为它是默认的参数。

再次，这里使用的是原生的 `fetch API`。`JavaScript ES6` 模板字符串允许组件利用 `searchTerm` 来组成 URL。该 URL 是原生 `fetch API` 函数的参数。返回的响应需要被转化成 JSON 格式的数据结构。这是在处理 `JSON` 数据结构时，原生的 fetch API 中的强制步骤。最后将处理后的响应赋值给组件内部状态中的结果。此外，我们用一段 `catch` 代码来处理出错的情况。如果在发起请求时出现错误，这个函数会进入到 `catch` 中而不是 `then` 中。在本书之后的章节中，将涵盖错误处理的内容。

最后但同样重要的是，不要忘记在构造函数中绑定你的组件方法。

拓展操作符
---------------

`Dismiss` 按钮之所以不工作，是因为 `onDismiss()` 方法不能处理复杂的 `result` 对象。它现在还只能处理一个本地状态中的简单列表。但是现在这个列表已经不再是简单的平铺列表了。现在，让我们去操作这个 `result` 对象而不是去操作列表。

```react
onDismiss(id) {
const isNotId = item => item.objectID !== id;
const updatedHits = this.state.result.hits.filter(isNotId);
this.setState({
  ...
});
}

/ don`t do this
this.state.result.hits = updatedHits
```

React 拥护不可变的数据结构，不应该改变一个对象或者是直接改变状态。更好的做法是基于现在拥有的资源来创建一个新的对象。
`javaSrcipt ES6` 的 `Object.assign()` 函数可以达到这样的目的。它把接受的第一个参数作为目标对象，后面的所有参数作为源对象，然后把所有的源对象合并到目标对象中。只要将目标对象设置为空，就可以得到一个新的对象。

```react
const updatedHits  = {hits:updatedHits };
const updateList = Object.assign({},this.state.result,updateHits);
```

### ES6中数组的扩展运算符

```react
数组+字符串
const userList = ['Robin', 'Andrew', 'Dan'];
const additionalUser = 'Jordan';
const allUsers = [ ...userList, additionalUser ];

console.log(allUsers);
// output: ['Robin', 'Andrew', 'Dan', 'Jordan']

合并数组
onst oldUsers = ['Robin', 'Andrew'];
const newUsers = ['Dan', 'Jordan'];
const allUsers = [ ...oldUsers, ...newUsers ];

console.log(allUsers);
// output: ['Robin', 'Andrew', 'Dan', 'Jordan']
对象
const userNames = { firstname: 'Robin', lastname: 'Wieruch' };
const userAge = { age: 28 };
const user = { ...userNames, ...userAge };

console.log(user);
// output: { firstname: 'Robin', lastname: 'Wieruch', age: 28 }
```

重新改写

```react
onDismiss(id) {
    const isNotId = item => item.objectID !== id;
    const updatedHits = this.state.result.hits.filter(isNotId);
    this.setState({
      result: { ...this.state.result, hits: updatedHits }
    });
}
```

条件渲染
----------------

条件渲染用于需要决定渲染哪个元素的时候，就可以用 `JSX` 中 `if-else` 来实现
组件内部状态中的 `result` 对象初始值为空，当 `API` 结果还没有返回的时候，此时的主组件中没有任何的元素，这就是一个条件渲染，因为在某个特定的条件下， `render()` 方法提前返回了，根据条件， 主组件渲染它的元素或者什么都不渲染

`Table` 组件的渲染依赖于 `result`，所以将它包在一个独立的条件渲染中才比较合理。即使 `result` 为空，其它的所有组件还是应该被渲染。你只需要在 `JSX` 中加上一个三元运算符就可以达到这样的目的。

```react
return (
    <div className="page">
      <div className = "interactions">
        <Search
          value={searchText}
          onChange={this.onSearchChange} />
          {
            result?
            <Table
            list={result.hits}
            pattern={searchText}
            onDismiss={this.onDismiss}/>:null
          }
      </div>
    </div>
  )
```

这是实现条件渲染的第二种方式。  
第三种则是运用 `&&` 逻辑运算符。在JavaScript中， true && 'Hello World' 的值永远是 “Hello World”。而 false && 'Hello World' 的值则永远是 false

```react
  const result = true && 'Hello World';
  console.log(result);
  // output: Hello World

  const result = false && 'Hello World';
  console.log(result);
  // output: false
```

所以也可以这样改写

```react
{ result &&
  <Table
    list={result.hits}
    pattern={searchTerm}
    onDismiss={this.onDismiss}
  />
}
```

客户端或者服务端搜索
--------------------------

在使用 `Search` 组件的输入栏时，会在客户端过滤这个列表，所以要做的是使用 API 在服务端进行搜索。否则，就只能处理第一次从 `componentDidMount()` 拿到的默认搜索词的 API 响应  
可以在主组件中顶一个 `onSeachSubmit()` 方法，当 `Search` 组件进行搜索的时候，可以用这个方法来 API 获取结果，顺便在 `Search` 中增加一个新按钮，这个按钮可以触发搜索请求  

```react
  class FormP extends Component {

  constructor(props) {
    super(props);
    this.state = {
      result: null,
      searchText: DEFAULT_QUERY,
    };
    this.setSearchTopStories = this.setSearchTopStories.bind(this);
    this.fetchSearchTopStories = this.fetchSearchTopStories.bind(this);
    this.onSearchChange = this.onSearchChange.bind(this);
    this.onSearchSubmit = this.onSearchSubmit.bind(this);
    this.onDismiss = this.onDismiss.bind(this);
  }

  ....

  onSearchSubmit(e) {
    const { searchText } = this.state;
    // console.log( searchText);
    this.fetchSearchTopStories(searchText);
    e.preventDefault();
  }
}

const Search = ({ value, onChange, onSubmit, children }) =>
  <form onSubmit={onSubmit} >
    <input
      type="text"
      value={value}
      onChange={onChange}
    />
    <button type="submit">
      {children}
    </button>
  </form>
```

分页抓取
--------------------------

改造一下可组合 API 常量，以便于处理分页数据

```react
const DEFAULT_QUERY = 'redux';
const PATH_BASE = 'https://hn.algolia.com/api/v1';
const PATH_SEARCH = '/search';
const PARAM_SEARCH = 'query=';
const PARAM_PAGE = 'page=';
const url = `${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${searchTerm}&${PARAM_PAGE}`;
```

`fetchSearchTopStories()` 函数接收分页作为第二个参数。如果不提供第二个参数，它将使用 `0` 作为初始参数并发起请求。因此 `componentDidMount()` 和 `onSearchSubmit()` 方法在第一个请求中默认获取第一页。之后的请求将根据提供的第二个参数抓取下一个页面的数据。

```react
  class App extends Component {

  ...

  fetchSearchTopStories(searchTerm, page = 0) {
    fetch(`${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${searchTerm}&${PARAM_PAGE}\
${page}`)
      .then(response => response.json())
      .then(result => this.setSearchTopStories(result))
      .catch(e => e);
  }

  ...

}
```

客户端缓存
------------------------

[The Road to learn React 代码](https://github.com/LbhFront-end/react-pratice/tree/master/my-first-react-app)

错误处理
------------------------

[The Road to learn React 代码](https://github.com/LbhFront-end/react-pratice/tree/master/my-first-react-app)

代码组织和测试
------------------------

本章将专注在几个重要话题来保证在一个规模增长的应用中代码的可维护性。你将了解如何去组织代码，以便在构建你的工程目录和文件时时遵循最佳实践。本章你将学会的另外一个话题是测试，这对你的代码健壮性非常重要。本章也会结合之前的练习项目来为你介绍这几个话题。

ES6模块： `Import` 和 `Export`
----------------------------------

在 `JavaScript ES6` 中可以从模块中导入和导出某些功能，这些功能可以是函数、类、组件、常量等等。基本上可以将所有东西都赋值到一个变量上。模块可以是单个文件，或者一个带有入口文件的文件夹  

`import` 与 `export` 语句可以让我们在不同的文件共享代码，这些语言有利于代码的分割。代码风格就是将代码分配到多个文件夹中去，以保持代码的重用性和可维护性。前者得以成立时因为可以在不同的文件中导入相同的代码片段，而后者得以成立是因为维护的代码时唯一的代码源

最后一点是，它能帮助我们思考代码封装。不是所有的功能都需要从一个文件导出。其中一些功能应该只在定义它的文件夹中使用。一个文件导出的功能是这个文件公共 API ，只有导出的功能才能被其他地方重用。遵循了封装的最佳实践

可以导出一个或者多个变量，称为一个命名的导出  

file1.js
>const firstname = 'lai',  
>const lastname = 'bh',  
>`export` { firstname , lastname };

并在另外一个文件中引用  
file2.js
>`import` { firstname, lastname } from './file1.js';  
console.log(firstname); // lai

也可以用对象的方式导入另外文件的全部变量  
file2.js
>`import` * as person from './file1.js';  
console.log(person.firstname); // lai

导入可以有一个别名，可能发生在输出多个文件中有相同命名的导出的时候。  
file2.js
>`import` { firstname as foo } from './file1.js'  
console.log(foo); // lai

还有一种情况 `default` 语句，可以被用在以下情况  
为了导出和导入单一功能  
为了强调一个模块输出 API 中的主要功能  
这样可以向后兼容 ES5 只有一个导出物的功能

file1.js
>const lbh = { firstname:'lai', lastname:'bh'};  
`export default` lbh;  

可以在导入 `default` 输出时省略花括号  
file2.js
>`import` developer from './file1.js'  
console.log(developer);  // { firstname:'lai',lastname:'bh'}

另外，输入名称可以与导入的 `default` 名称不一样，也可以将其与命名的导出与导入语句使用同一个名称  
file1.js
>const firstname = 'lai',  
>const lastname = 'bh',  
>const lbh = { firstname, lastname};  
`export`{ firstname, lastname};  
`export default` lbh

file2.js  
>`import` lbh, { firstname, lastname } from './file1.js';  
console.log(lbh);  // { firstname:'lai',lastname:'bh'}  
console.log(firstname, lastname); // (lai,bh)  

在命名的导出中，可以省略多余行直接导出变量  
>`export default` firstname = 'lai';
>`export default` lastname = 'bh';

代码组织与ES6模块
----------------------------------

可能会用到的模块结构

```react
src/
  index.js
  index.css
  App.js
  App.test.js
  App.css
  Button.js
  Button.test.js
  Button.css
  Table.js
  Table.test.js
  Table.css
  Search.js
  Search.test.js
  Search.css
```

这里将组件封装到各自文件中，但是这看起来不是很好。你可以看到非常多的命名冗余，并且只有文件的扩展文字不同。另外一种模块的结构大概类似：

```react
src/
  index.js
  index.css
  App/
    index.js
    test.js
    index.css
  Button/
    index.js
    test.js
    index.css
  Table/
    index.js
    test.js
    index.css
  Search/
    index.js
    test.js
    index.css
```

这看起来比之前清晰多了。文件名中的 index 名称表示他是这个文件夹的入口文件。这仅仅是一个命名共识，你也可以使用你习惯的命名。在这个模块结构中，一个组件被 JavaScript 文件中组件声明，样式文件，测试共同定义。

另外一个步骤可能要将 App 组件中的变量抽出。这些变量用来组合出 Hacker News 的 API URL。

```react
src/
  index.js
  index.css
  constants/
    index.js
  components/
    App/
      index.js
      test.js
      index..css
    Button/
      index.js
      test.js
      index..css
    ...
```

自然这些模块会分割到 src/constants/ 和 src/components/ 中去。现在 src/constants/index.js 文件可能看起来类似下面这样：

Code Playground: src/constants/index.js

```react
export const DEFAULT_QUERY = 'redux';
export const DEFAULT_HPP = '100';
export const PATH_BASE = 'https://hn.algolia.com/api/v1';
export const PATH_SEARCH = '/search';
export const PARAM_SEARCH = 'query=';
export const PARAM_PAGE = 'page=';
export const PARAM_HPP = 'hitsPerPage=';
```

App/index.js 文件可以导入这些变量，以便使用。

Code Playground: src/components/App/index.js

```react
import {
DEFAULT_QUERY,
DEFAULT_HPP,
PATH_BASE,
PATH_SEARCH,
PARAM_SEARCH,
PARAM_PAGE,
PARAM_HPP,
} from '../constants/index.js';
```

当你使用 index.js 这个命名共识的时候，你可以在相对路径中省略文件名。

```react
Code Playground: src/components/App/index.js
import {
  DEFAULT_QUERY,
  DEFAULT_HPP,
  PATH_BASE,
  PATH_SEARCH,
  PARAM_SEARCH,
  PARAM_PAGE,
  PARAM_HPP,
} from '../constants';

...
```

但是 index.js 文件名称后面发生了什么？这个约定是在 node.js 世界里面被引入的。index 文件是一个模块的入口。它描述了一个模块的公共 API。外部模块只允许通过 index.js 文件导入模块中的共享代码。考虑用下面虚构的模块结构进行演示：

```react
src/
  index.js
  App/
    index.js
  Buttons/
    index.js
    SubmitButton.js
    SaveButton.js
    CancelButton.js
```

这个 Buttons/ 文件夹有多个按钮组件定义在了不同的文件中。每个文件都 export default 特定的组件，使组件能够被 Buttons/index.js 导入。Buttons/index.js 文件导入所有不同的表现的按钮，并将他们导出作为模块的公共 API。

Code Playground: src/Buttons/index.js

```react
import SubmitButton from './SubmitButton';
import SaveButton from './SaveButton';
import CancelButton from './CancelButton';

export {
  SubmitButton,
  SaveButton,
  CancelButton,
};
```

现在 src/App/index.js 可以通过定位在 index.js 文件模块的公共 API 导入这些按钮。
Code Playground: src/App/index.js

```react
import {
  SubmitButton,
  SaveButton,
  CancelButton
} from '../Buttons';
```

参考链接：https://leanpub.com/the-road-to-learn-react-chinese