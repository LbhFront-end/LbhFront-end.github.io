---
title: react-day1
date: 2018-06-02 15:00:45
tags: react
categories: 
	- react相关
---

react 学习笔记day-01
===========

介绍JSX  
--------------

>`const element = <h1>Hello, world!</h1>`  

它称为JSX，是JavaScript的语法的扩展。  
### 选择JSX的理由：  
React拥抱渲染逻辑与其他UI逻辑固有耦合的事实：事件如何处理，状态如何随事件变化以及数据如何准备显示。    
通过将标记和逻辑放入单独的文件中，React不再人为地分离技术，而是将问题与称为“组件”的松散耦合单元分隔开来。  

在JSX中嵌入表达式  

下面例子中，声明一个变量`name`,然后在JSX中使用它包装在大括号中：  

```react
const name  = 'Josn Lai';
const elenment = <h1>Hello , {name}</h1>;

ReactDOM.render(
    element,
    document.getElementById('root')
);
```

可以在JSX的大括号中（`{}`）放入任何有效的JavaScript表达式。  
下例中，将调用JavaScript函数的结果嵌入到`formatName（user）`到` <h1>`元素中  

```react
function formatName(user){
    return user.firstName + ' ' + user.lastName;
}
const user = {
    firstName: 'lai',
    lastName: 'binghong'
};

const element = (
    <h1>
        Hello,{formatName(user)}!
    </h1>
);

ReactDOM.render(
    elememt,
    document.getElementById('root');
);
```

### JSX也是一个表达式  
编译之后，JSX表达式变成常规的JavaScript函数调用并评估为JavaScript对象。这意味着可以在if语句和for循环中是用JSX将其分配给变量，将其作为参数接受，并从函数中返回：  

```react
function getGreeting(user){
    if(user){
        return <h1>Hello，{formatName(user)}!</h1>
    }
    return <h1>Hello, Stranger.</h1>
}
```

使用JSX指定属性  

可以使用引号将字符串文字指定为属性  
>`const element = <div tabIndex = “0”></div>`;  

也可以使用大括号将javascript表达式嵌入到属性中  
>`const element = <img src={user.avatarUrl}></img>`; 

在属性中嵌入javascript表达式时，请勿将大括号括起来，应该使用引号（用于字符串值）或大括号（用于表达式），但是不可以同时使用同一个属性  
**JSX的命名一般用camelCase，驼峰式命名。class => className ,tabindex => tabIndex.**

用JSX可以包含子项，单标签用闭合  

>`const element = <img src = {user.avatarUrl}/>;`

JSX标签可能包含子项：  

```react
const element = (
    <div>
        <h1>Hello!</h1>
        <h2>xxx</h2>
        <div>
        ccc
        </div>
    </div>
);
```

### JSX防止注入攻击
默认情况下，react DOM 在渲染之前转义嵌入在JSX中的任何值，因此它确保不会注入任何未明确写入应用程序的内容，在呈现前，所有内容都会转换为字符串，有助于防止XSS攻击

### JSX表示对象
Babel将JSX编译为 `React.createElement()`调用。
下方两个例子是相同的：

```react
const element = (
    <h1 className = "show">
        Hello !
    </h1>
);
 
const element  = React.createElement(
    'h1',
    {className: 'show'},
    'Hello!'
);
```

`React.createElement()`执行一些检查以帮助编写无措代码，但本质上它会创建一个如下对象：

```react
const element = {
    type: 'h1',
    props: {
        className :'show',
        children: 'Hello!'
    }
}
```

渲染元素
----------------
元素时React 应用程序的最小构建块   
一个元素描述了你想在屏幕上看到的内容   
与浏览器DOM元素不同，React元素时普通元素，创建起来很便宜，React DOM负责更新DOM以匹配React元素   

### 将元素渲染到DOM中
假设div存在于html某个文件中的某个地方

>`<div id="root"></div>`  

我们称之为“根”DOM节点，因为它内容部的所有内容都将由React DOM管理  
仅使用React 构建的应用程序通常具有单个根DOM节点，如果将React集成到现有的应用程序中，则可以根据需要拥有尽可能多的独立根DOM节点。  
要将React元素渲染到根DOM节点中，请将两者传递给`ReactDOM.render()`;  

>`const element = <h1>Hello,world</h1>;`  
>`ReactDOM.render(element,documentById('root'));`

更新呈现的元素  
React元素时不可变的，一旦创建了一个元素，就不能更改其子元素或者属性。  
更新Ui的唯一方法是创建一个新元素并将其传递给`ReactDOM.reader()`

时钟例子  

```react
function tick(){
    const element = (
        <div>
        <h1>1</h1>
        <h1>2</h1>
        </div>
    );
    ReactDOM.render(element,document.getElementById('root'));
}

setInterval(tick, 1000);
```

### 组件和道具(props)
组件是讲用户界面独立分开，可重复使用的部分，并且可独立思考每一部分

### 功能和类组件   
定义组件的最简单方法是编写一个javascript函数   

```react
function Welcome(x){
    return <h1>你好，{x.name}</h1>;
}
```

上面的javascript函数一个有效的React组件，因为它接受一个包含数据的‘x’（代表属性）对象参数并返回一个React元素，我们称这些组件为‘功能性’，因为它们实际上是JavaScript功能  

也可以使用ES6类来定义组件：   

```react
class Welcome extends React.Compoent{
    render(){
        return <h1>hello, {this.x.name}</h1>
    }
}
```

渲染组件

React元素可以表示DOM标签

>`const element = <div/>;`

也可以表示用户定义的组件

>`const element =<Welcome name="Lbh">;`

当React查看表示用户定义组件的元素时，它会将JSX属性作为单个对象传递给此组件，我们称这个组件为道具  

```react
function Welcome(props){
    return <h1>hello!{props.name}</h1>;
    }
    const element = <Welcome name="Lbh"/>;
    ReactDOM.render(
    element,
    documemt.getElementById('root')
);
```

**注意：始终使用大写字母开始组件名称。**

构成组件

组件可以在其输出中引用其他组件，一个按钮、一个窗体、一个对话框、一个屏幕，在React应用程序中，所有的这些通常都表示为组件  
例如：创建一个App呈现Welcome多次的组件  

```react
function Welcome(x){
    return <h1>hello! {x.name}</h1>;
}
function App(){
    return (
        <div>
        <Welcome name ="1"/>
        <Welcome name ="2"/>
        <Welcome name ="3"/>
        </div>
    );
}

ReactDOM.render(
    <App/>,
    document.getElementById('root')
);
```

### 提取组件
不要害怕将组件分解成更小的组件

道具是只读的   
无论将一个组件声明为一个函数还是一个类，它都不能修改它自己的道具。   

状态和生命周期
-----------------------
更新UI的方法：使用它`ReactDOM.render()`改变呈现的输出：  \

```react
function tick(){
    const element = (
        <div>
        <h1>1</h1>
        <h2>It is {new Date().toLocaleTimeString()}.</h2>
        </div>
    );
    ReactDOM.render(
        element,
        document.getElementById('root')
    );
}

setInterval(tick,1000);
```

封装时钟:  

```react
function Clock(props){
    return(
        <div>
            <h1>1</h1>
            <h2>It is {props.date.toLocaleTimeString()}</h2>
        </div>
    );
    
    function tick(){
        ReactDOM.render(
            <Clock date={new Date()}/>,
            document.getElementById('root')
        );
    }
}

setInterval(tick,1000);
```

给组件添加“状态”，状态类似于道具，是私人的并且是完全由组件控制的。  

### 将函数转换为类  
1.创建一个扩展名为ES6的类，名称相同的React.Component   
2.为它添加一个空的方法render()  
3.将函数的主体移动到render方法中  
4.更换props用this.props的render身体  
5.删除多余的空函数声明  

```react
class Clock extends React.Component{
    render(){
        return(
            <div>
                <h1>1</h1>
                <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
            </div>
            )
    }
}
```

将本地状态添加到类  

```react
class Clock extends React.Component{
    constuctor(props){
        super(props);
        this.state = {date: new Date()};
    }
    render(){
        return(
            <div>
            <h1>2</h1>
            <h2> It is {this.state.date.toLocaleTimeString()}.</h2>
            </div>
        );
    }
}

ReactDOM.render(
<Clock />,
document.getElementById('root')
);
```

接下来设置计时器每秒更新一次

### 将生命周期方法添加到类中

一个组件完整的生命周期(`lifecycle hooks`)包括实例化阶段、活动阶段、销毁阶段，每个阶段又由相应的方法管理   
`mounting` :表示正在挂载虚拟DOM到真实DOM  
`updating`  :表示正被重新渲染  
`unmounting`:表示正在将虚拟DOM移除真实DOM

每个过程提供了两个函数

`componentWillMount()`     
`componentDidMount()` 
`componentWillUpdate(object nextProps,object nextState)`  
`componentDidUpdate(object nextProps,object nextState)`   
`componentWillUnmount()` 

`componentDidMount()`  在组件输出呈现给DOM后，该钩子运行，是设置定时器的好地方  

```react
componentDidMount(){
    this.timerID = setInterval(
    () => this.tick(),1000
    );
}

compoentWillUnmount(){
    clearInterval(this.timerID);
}
```

最终：  

```react
class Clock extends React.Component{
    constuctor(props){
        super(props);
        this.state = {date: new Date()};
    }
    
    componentDidMount(){
        this.timerID = setInterval(
        () => this.tick(),1000
        );
    }

    compoentWillUnmount(){
        clearInterval(this.timerID);
    }
    
    tick(){
        this.setState({
        date:new Date()
        });
    }
    
    render(){
        return(
            <div>
            <h1>2</h1>
            <h2> It is {this.state.date.toLocaleTimeString()}.</h2>
            </div>
        );
    }
}

ReactDOM.render(
    <Clock />,
    document.getElementById('root')
);
```

### 正确使用状态（State）

setState()来改变状态，状态的更新可能是异步的

```react
this.setState(function(prevState,props){
    return{
        counter:pervState.counter + props.increment
    };
});
```



参考链接：https://react.docschina.org/docs/hello-world.html