---
title: The Road to learn React Part1
date: 2018-06-06 14:00:12
tags: react
categories: 
	- react相关
---
The Road to learn React书籍学习笔记(第一章)
===========

[![The Road to learn React 代码](https://img.shields.io/badge/%E4%BB%A3%E7%A0%81%E8%AF%A6%E6%83%85%E8%A7%81%E6%88%91%E7%9A%84github-%E7%82%B9%E6%88%91-blue.svg)](https://github.com/LbhFront-end/react-pratice/tree/master/my-first-react-app)

react灵活的生态圈
--------------------


### Small Application
Boilerplate: create-react-app  
Utility: JavaScript ES6 and beyond  
Styling: plain CSS and inline style  
Asynchronous Requests: fetch  
Higher Order Components: optional  
Formatting: none  
Type Checking: none or PropTypes  
State Management: local state  
Routing: none or conditional rendering  
Authentication: Firebase  
Database: Firebase  
UI Components: none  
Time: moment or date-fns  
Testing: Jest

### Medium Application
Boilerplate: create-react-app with eject  
Utility: JavaScript ES6 + Lodash or Ramda  
Styling: CSS modules or Styled Components  
Asynchronous Requests: fetch or axios  
Higher Order Components: maybe + optional recompose  
Formatting: Prettier  
Type Checking: none or Flow  
State Management: local state and very optional Redux  
Routing: React Router  
Authentication: Firebase  
Database: Firebase  
UI Components: none or Semantic UI  
Time: moment or date-fns  
Testing: Jest with Enzyme  

### Large Application
Boilerplate: create-react-app with eject or own boilerplate project  
Utility: JavaScript ES6 + Lodash or Ramda  
Styling: CSS modules or Styled Components  
Asynchronous Requests: axios  
Higher Order Components: maybe + optional recompose   
Formatting: Prettier  
Type Checking: Flow   
State Management: local state and Redux or MobX   
Routing: React Router   
Authentication: Solution with an own Express/Hapi/Koa Node.js Server with Passport.js  
Database: Solution with an own Express/Hapi/Koa Node.js Server with a SQL or NoSQL Database  
UI Components: Semantic UI or own implementation of UI components  
Time: moment or date-fns  
Testing: Jest with Enzyme  

基本要求npm/node
-----------------------
Node包管理器（npm/node/package/manager）可以让你通过命令行安装第三方node包。这些包可能是一系列的工具函数、库、或者是集成的框架  

零配置搭载react
-------------------
>`npm install -g react-reate-app `

运行

>npm start

运行测试

>npm test

构建项目产品文件

>npm run build

ES6中的`const`与`let`
-----------------------------
被`const`声明的变量不能被重新赋值或者是重新声明。可以使用它创建不可变数据结构。但如果创建的这个变量是数组或者是对象的时候，里面持有的内容可以被更新。  
当一个变量需要被重新赋值的时候，应该使用`let`去声明它

ReactDOM
------------------
简单地说，`ReactDOM.render()`会使用`JSX`替换`HTML`中一个`DOM`节点，这样可以很容易地`React`集成到每一个其他的应用中。`ReactDOM.render()` 有两个传入参数，第一个是准备渲染的`JSX`,第二个参数指定了`React`应用在`HTML`中放置的位置。

模块热替换
------------------------
模块热替换（HMR）是一个帮助你在浏览器中重新加载应用的工具，并且无需让浏览器刷新页面。
在`src/index.js`中添加一些配置代码

  >`if（module.hot）{module.hot.accept();}`

代码改变后，浏览器就不会刷新页面，但是应用还是会重新加载并且正确的输出

JSX中复杂的JavaScript
----------------------------
定义一个列表

```react
import React, { Component } from 'react';
// import logo from './logo.svg';
import './App.css';
```


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

在JSX中使用HTML中的JavaScript是强大的，可以使用`map`来将一个列表转换成另外一个列表。记得在React中添加一个辅助属性，给列表的每个成员加上一个关键字（`key`）属性，这样就可以在列表发生变化的时候识别其中成员的添加、更改和删除的状态。不要错误地使用列表成员在数组的索引作为关键字，列表的成员索引是完全不稳定的

```react
class App extends Component {
    render() {
    return (      
        <div className = "App">
        {list.map(function(item){
            return(
            <div key = {item.objectID}>
            <span>
                <a href= {item.url}>{item.title}</a>
            </span>
            <span>{item.author}</span>
            <span>{item.num_comments}</span>
            <span>{item.points}</span>
            </div>
            );
        })}        
        </div>
    );
    }
}

export default App;
```


ES6 箭头函数
---------------------
箭头函数表达式比普通的函数表达式更加简洁

```react
//函数表示式
function(){...}
//箭头函数
（） => {...}
```

注意：如果函数只有一个参数，就可以移除参数的括号，但是如果有多个参数，就必须保留这个括号

```react
//允许
item =>{...}
(item) =>{...}
(item,key) =>{...}
//不允许
item,key =>{...}
```

上例可以改写为

```react
class App extends Component {
    render() {
        return (      
            <div className = "App">
            {list.map(item =>{
                return(
                <div key = {item.objectID}>
                <span>
                    <a href= {item.url}>{item.title}</a>
                </span>
                <span>{item.author}</span>
                <span>{item.num_comments}</span>
                <span>{item.points}</span>
                </div>
                );          
            })}      
            </div>
        );
    }
}
```

另外在ES6的箭头函数中，可以简洁函数体来替换块状函数体（用花括号`{}`表示），简洁函数体的返回不用显示声明，这样就可以移除掉return表示式，那再改简洁

```react
class App extends Component {
    render() {
        return (      
            <div className = "App">
            {list.map(item =>
                <div key = {item.objectID}>
                <span>
                <a href= {item.url}>{item.title}</a>
                </span>
                <span>{item.author}</span>
                <span>{item.num_comments}</span>
                <span>{item.points}</span>
            </div>         
            )}      
            </div>
        );
    }
}
```


ES6类
----------------------
javaScript ES6中引入了类的概念。类通常在面向对象编程语言中被使用，可以根据使用情况一边使用函数式编程一边使用面向对象编程   
尽管React为了例如不可变数据结构等的特征而拥抱函数式编程，但是它还是使用类来声明组件，这些组件就被称为ES6组件，React混合使用了两种编程范式中的有益部分

例如下面这个Developer类

```react
class Developer{
        constructor(firstname,lastname){
            this.firstname = firstname;
            this.lastname = lastname;      
        }

        getName(){
        return this.firstname + '' +this.lastname;
    }
}
```

类都有一个用来实例化自己的构造函数，这个构造函数可以用来传入参数来赋予给类的实例。此外，类可定义函数，因为这个函数被关联给了类，所有它被称为方法，通常它被当称为类的方法。  
实例化上面的Develper类，以及使用它的方法   

>const Lbh = new Developer('Lai','binhong');  
console.log(Lbh.getName());


React 使用JavaScript ES6类来实现 ES6组件  

```react
import React, {Component} from 'react';
    class App extends Component {
    render(){
    ...
    }
}
```

`App类`继承自`Component`，你可以声明App组件，但是这个组件需要继承自另一个组件，继承的意思就是在一个面向对象编程的语言中，你要需要遵循继承原则， 它可以把功能从一个类传递到另一个类

这个App类就从Component类中继承了它的功能，这个Component类是从一个基本ES6类中继承来的ES6组件类。它 有一个React组件所需要的所有功能。渲染（`render`）方法就是其中可以使用的一个功能。
这个Component 类封装了所有React类需要的细节。
`React Component`类暴露出来的方法都是公共的接口，这些方法有一个方法必须被重写，其他的则不一定要被重写。`render()`方法是必须要被重写的方法，因为你定义了一个React组件的输出，它必须被定义

参考链接：https://leanpub.com/the-road-to-learn-react-chinese