---
title: The Road to learn React Part2
date: 2018-06-07 18:00:45
tags: react
categories: 
	- react相关
---
The Road to learn React书籍学习笔记(第二章)
===========

组件的内部状态
-------------------------

组件的内部状态也称为局部状态，允许保存、修改和删除在组件内部的属性，使用ES6类组件可以在构造函数中初始化组件的状态。构造函数只会在组件初始化的时候调用一次

类构造函数

```react
class App extends Component{
  constructor(props){
    super(props);
  }
}
```

使用ES6编写的组件有一个构造函数时，需要强制地使用 `super()` 方法， 因为这个 `App组件` 是 `Component` 的子类，因为需要在 `App组件` 声明 `extends Component`
也可以调用 `super(props)`，它会在构造函数中设置 `this.props` 以供构造函数中访问。否则在构造函数中访问 `this.props` ，会得到 `undefined`

例子，组件的初始状态是一个列表

```react
const list = [
 {
  title:'React',
  url: 'https://facebook.github.io/react/',
  author: 'Jordan Walke',
  num_comments: 3,
  points: 4,
  objectID: 0,
 }
];

class App extends Component{
  constructor(props){
    super(props);
    this.state = {
      list : list,
    }
  }
}
```

`state` 通过使用 `this` 绑定在类上，因此可以在整个组件中访问到 `state`。通过 `render()` 方法可以映射一个在组件外定义静态列表

```react
class App extends Component{
  render(){
    return(
      <div className = "App">
        {this.state.list.map(item =>
          <div key = {item.objectID}>
            <span><a href = {item.url}>{item.title}</a></span>
            <span>{item.author}</span>
            <span>{item.num_comments}</span>
            <span>{item.points}</span>
          </div>
          )
        }
      </div>
    );
  }
}
```

现在list是㢟的一部分，它在组件的 `state` 中，可以从list添加、修改、删除列表项。组件的 `render` 会再次运行，可以简单修改组件内部状态，确保组件重新渲染并且展示从内部状态获取到的正确数据
修改 `state` 可以使用 `setState()` 方法来修改   

ES6 对象初始化
--------------------

初始化例子

```react
const name = 'Laibh';
const user = {
  name ： name
};
```

当对象中属性名与变量名相同时可以如下操作

```react
const name = 'Laibh'; 
const user = {
  name
};
```

在应用程序中，列表变量名与状态属性名称共享同一名称

```react
//ES5 
this.state = {
  list:list
}

//ES6
this.state = {
  list
};
```

简写方法名

```react
//ES5
var userService = {
  getUserName :function(user){
    return user.firstname + ' ' + user.lastname;
  }
}

//ES6
const userService = {
  getUserName(user){
    return user.firstname + ' ' + user.lastname;
  }
}
```

计算属性名

```react
//ES5
var user = {
  name:'Laibh'
}

//ES6
const key = 'name';
const user = {
  [key] :'Laibh'
}
```


单向数据流
----------------

组件中有一些内部的 `state`，练习 `state` 操作的好方式增加一些组件的互动
为列表增加一个删除按钮

```react
  class App extends Component {

  render() {
    return (
      <div className="App">
        {this.state.list.map(item =>
          <div key={item.objectID}>
            <span>
              <a href={item.url}>{item.title}</a>
            </span>
            <span>{item.author}</span>
            <span>{item.num_comments}</span>
            <span>{item.points}</span>
            <span>
              <button
                onClick={() => this.onDismiss(item.objectID)}
                type="button"
              >
                Dismiss
              </button>
            </span>
          </div>
        )}
      </div>
    );
  }
}
```

上面类中，`onDismiss()` 还没有被定义，它通过id来标识哪个应该被删除，此函数绑定到类，就成为了类方法，所以访问它的时候要用 `this.onDismiss()`  而不是用 `onDismiss()` 。`this` 对象是类的实例，为了将 `onDismiss()`  定义为类方法，需要在构造函数中绑定它。并定义它的逻辑功能

```react
  class App extends Component {

  constructor(props) {
    super(props);

    this.state = {
      list,
    };

    this.onDismiss = this.onDismiss.bind(this);
  }
   onDismiss(id) {
  ...
  }

  render() {
    ...
  }
}
```

可以使用JavaScript内置的 `filter` 方法来删除列表的一项，它会遍历整个列表，通过条件来过滤，匹配的返回 `true` 并留在列表中

```react
onDismiss(id){
  const updateList = this.state.list.filter(function isNotId(item){
      return item.objectID !== id
    });
}
```

或者一行箭头函数

```react
onDismiss(id){
  const updateList = this.state.list.filter(item => item.objectID !== id);
}
```

接着要更新数据

```react
this.setState({list:updateList});
```


绑定
------------------

类不会自动绑定 `this` 到实例上 
需要自己绑定

```react
constructor() {
    super();

    this.onClickMe = this.onClickMe.bind(this);
  }
```

类的绑定方法也有人写在其他地方，例如render()函数中

```react
render(){
  return(
    <button onClick = {this.onClickMe.bind(this)}>Click Me</button>
  )
}
```

但是应该避免这样使用，因为它会在每次 `render()`  方法执行的时绑定类方法。组件每次更新都会导致性能消耗，当在构造函数中绑定的时候，绑定只会在组件实例化时运行一次，这样是一个更好的方式

另外有一些人剔除在构造函数中定义业务逻辑类方法

```react
constructor(){
  super();
  this.onClick = () =>{
    conlose.log(this);
  }
}
```

这样随着时间推移会让构造函数变得混乱，避免使用。构造函数的目的只是实例化类以及所有的属性

事件处理
--------------------

```react
<button
  onClick={() => this.onDismiss(item.objectID)}
  type="button">
  Dismiss
</button>
```

当传递一个参数到类的方法，需要将它封装到另一个（箭头）函数中，由于要传递给事件处理器使用，因为它必须是一个函数，而下面的这个代码不会工作。因为类方法会在浏览器中打开程序时候立即执行

```react
<button onClick = {this.onDismiss(item.objectID)}>
    Dismiss
</button>
```

倘若写成  

```react
<button onClick = {onDismiss}>
    Dismiss
</button>
```

就不会立即执行，但是需要传参数，所以就不这么用

另外的一个解决方案是在外部定义一个包装函数，并且只将定义的函数传递给处理程序。因为需要访问特定的列表项，所以它必须位于 `map` 函数块的内部

```react
class App extends Component{
  render(){
    return(
      <div className = "App">
        {this.state.list.map(item =>
          const onHandldDismiss = () =>
            this.onDismiss(item.objectID);

            return(
              <div key={item.objectID}>
                <span>
                  <a href={item.url}>{item.title}</a>
                </span>
                <span>{item.author}</span>
                <span>{item.num_comments}</span>
                <span>{item.points}</span>
                <span>
                  <button
                    onClick={onHandleDismiss}
                    type="button"
                  >
                    Dismiss
                  </button>
                </span>
              </div>
            )
        )}
      </div>
    )
  }
}
```

在事件处理程序中使用箭头函数对性能会有影响

和表单互动
---------------------

```react
const list = [{
  title: 'React',
  url: 'https://facebook.github.io/react',
  author: 'Jordan Walke',
  num_comments: 3,
  points: 4,
  objectID: 0,
}, {
  title: 'Redux',
  url: 'https://github.com/reactjs/redux',
  author: 'Dan Abramov, Andrew Clark',
  num_comments: 2,
  points: 5,
  objectID: 1,  
}];
```


```react
const isSearched = searchText => item => item.title.toLowerCase().includes(searchText.toLowerCase());
class FormP extends Component{
  constructor(props){
    super(props);
    this.state = {list,searchText:''};
    this.onSearchChange = this.onSearchChange.bind(this);
    this.onDismiss = this.onDismiss.bind(this);
  }
  onSearchChange(e){
    this.setState({searchText:e.target.value});
  }
  onDismiss(id){
    console.log(this);
    const updateList = this.state.list.filter(item =>
      item.objectID !== id
    );
    this.setState({list:updateList});
  }
  render(){
    return(
      <div className = "FormP">
        <form>
          <input type="text" onChange = {this.onSearchChange}/>
        </form>
        {this.state.list.filter(isSearched(this.state.searchText)).map(
          item =>
          <div key = {item.objectID}>
          <span>
            <a href= {item.url}>{item.title}</a>
          </span>
          <span>{item.author}</span>
          <span>{item.num_comments}</span>
          <span>{item.points}</span>
          <span>
            <button onClick = {()=>this.onDismiss(item.objectID)}>Dismiss</button>
          </span>
        </div>  
        )}
      </div>
    )
  }
}

export default FormP;
```

ES6 解构  
--------------------  

在JavaScript ES6 中有一个更方便的方法来访问对象和数组的属性，叫做解构。

```react
  const user = {
    firsname : 'L',
    lastname : 'binhong'
  }

  //ES5
  var firstname = user.firstname;
  var lastname = user.lastname;

  console.log(firstname + '' +lastname);

  //ES6
  const {firstname, lastname} = user;
  conlose.log(firstname + '' +lastname);
```

在JavaScript ES5中每次访问对象的属性都需要额外添加一行代码，但是ES6中就可以在一行中进行，可读性最好的方法就是将对象解构成多个属性时使用多行
对于数组也可以使用解构，可以保持代码的可读性

```react
const user = ['1','2','3'];
const [
  a,
  b,
  c
] = user;

console.log(a,b,c); //1,2,3
```

受控组件
----------------------

表单元素 `<input>`/`<select>`/`<textarea>` 会以元素HTML的形式保存它们自己的状态，一旦有人从外部做了修改，就会修改内部的值，在React中这被称为不受控组件，因为它们自己处理状态，在React中，我们得把它们变成 **受控元素**

```react
  class App extends Componet{
    render(){
      const {searchText,list} = this.state;
      return(
        <div className = "App">
          <form>
            <input
              type = "text"
              value = {searchText}
              onChange = {this.onSearchChange}
            ></input>
          </form>
        </div>
      )
    }
  }
```

现在输入框的单项数据流循环是自包含的，组件内部状态是输入框的唯一数据来源

拆分组件
-------------------

用于搜索的输入组件和一个用于展示的列表组件

```react
  class App extends Componet{
    render(){
      const {searchText,list} = this.state;
      return(
        <div className = "App">
          <Search />
          <Table />
        </div>
      )
    }
  }
```

在组件传递属性并在组件中使用，App组件需要传递本地状态 `state` 托管的属性和它自己的类方法

```react
  class App extends Component{
    render(){
      const {searchText,list} = this.state;
      return(
        <div>
          <Search 
            value = {searchText}
            onChange = {this.onSearchChange} />
          <Table 
            list = {list}
            pattern = {searchText}
            onDismiss = {this.onDismiss} />
        </div>
      )
    }
  }
```

Search组件

```react
  class Search extends Component{
    render(){
      const {value,onChange} = this.props;
      return(
        <form>
          <input
            type = "text"
            value = {value}
            onChange = {onChange} />
        </form>
      )
    }
  }
```

Table组件

```react
  class Table extends Component{
    render(){
      const {list,pattern.onDismiss} = this.props;
      return(
        <div>
          {list.filter(isSearched(pattern)).map(item =>
            <div key = {item.objectID}>
              <span><a href = {item.url}>{item.title}</a></span>
              <span>{item.title}</span>
              <soan>{item.points}</span>
              <span>{item.num_comments}</span>
              <button onClick ={() => onDismiss(item.objectID)}>
                Dismiss
              </button>
            </div>
          )}
        </div>
      )
    }
  }
```

可组合组件
--------------------

在 `props` 对象中还有一个小小的属性可以供使用： `children` 属性.通过这个属性可以将元素从上层传递到组件中，这些元素对组件来说是未知的，但是却为组件互相结合提供了可能性、
下例中，将一个文本作为子元素传递到Search组件中

```react
  class App extends Component{
    render(){
      const {searchText,list} = user;
      return(
        <div>
          <Search 
            value = {searchText}
            onChange = {this.onSearchChange} />
      )
    }
  }
```

在 `Search` 组件可以从 `props` 对象中解构出 `children` 属性，就可以指定它应该在哪里显示

```react
class Search extends Component{
  render(){
    const {value,onChange,children} = this.props;
    return(
      <form>
        {children}
        <input
          type = "text"
          value = {value}
          onChange = {onChange} />
      }
      </form>
    )
  }
}
```

可复用组件
----------------------

可复用和可组合组件帮助理解合理的组件分层，它们是React视图层的基础

```react
  class Button extends Component{
    render(){
      const {onClick,className,children} = this.props;
      return(
        <button 
          onClick = {onClick}
          className = {className}
          type = "button">
          {children}
        </button>
      )
    }
  }
```

Button组件拥有单一可信数据源，一个Button组件可以立即重构所有button。一个Button组件统治了所有button

>`<Button onclick = {() => onDismiss(item.objectID)}>Dissmiss</Button>`

函数式无状态组件(function stateless componenet)
-------------------------------------------------

这类组件就是函数，它们接受一个输入并返回一个输出。输入时props，输出就是一个普通的JSX组件实例。函数式无状态组件是函数，并且它们没有本地状态。不能通过 `this.state` 或者是 `this.setState()` 来访问或者更新状态，因为这里没有 `this` 对象，此外，它也没有生命周期方法。 `constructor()` 与 `render()` 就是其中两个。 `constructor` 在一个组件的生命周期只执行一次，而 `render()` 方法只会在最开始执行一次

### ES6 类组件

在类的定义中，它们继承自 `React` 组件。 `extend` 会注册所有的生命周期方法，只要在 `React component API`中，都可以在组件中使用。通过这种方式，可以使用 `render()` 方法，此外还可以通过使用 `this.state` 和 `this.setState()` 来存储和操作 `state`

把上例的Search组件重构为一个函数式无状态组件

```react
function Search(props){
  const {value,onChange,children} = props;
  return{
    <form>
      {children}<input
        type = "text"
        value = {value}
        onChange = {onChange} />
    </form>
  }
}
```

或者将参数放在函数里面

```react
function Search({value,onChange,children}){
  return{
    <form>
      {children}<input
        type = "text"
        value = {value}
        onChange = {onChange} />
    </form>
  }
}
```

然后ES6 箭头函数一波

```react
const Search = ({value,onChange,children}) =>
  <form>
    {children}<input
      type = "text"
      value = {value}
      onChange = {onChange} />
  </form>  
```

如果想在ES6箭头函数写法中做些事情的话 可以这样写

```react
const Search = ({value,onChange,children}) => {

  //do something
  return(
    <form>
      {children}<input
        type = "text"
        value = {value}
        onChange = {onChange} />
    </form>
  )
}
```

给组件声明样式
----------------------

可以复用 `src/App.css` 或者 `src/index.css` 文件



参考链接：https://leanpub.com/the-road-to-learn-react-chinese