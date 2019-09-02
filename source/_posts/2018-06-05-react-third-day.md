---
title: react-day3
date: 2018-06-05 18:00:00
tags: react
categories: 
	- react相关
---
react 学习笔记day-03
===========

 组成与继承`Composition` vs `Inheritance`
--------------------------------------

 React具有巨大的组合模型，推荐使用组件组合而不是用继承  

###  Containment

 很多组件一开始并不知道它们的子组件是什么，例如侧边栏，对话框等
 建议这样的组件用特殊的子类(`children`)将参数直接传到它们的输出

```react
function FancyBorder(props){
    return(
        <div className = {'FancyBoder FancyBorder-' +props.color}>
        {props.children}
        </div>
    )
}
```

###  让组件通过嵌套JSX直接传值给子类

```react
function WelcomeDialog(){
    return(
        <FancyBorder color = "blue">
        <h1 className = "Dialog-title">
            Hi!
        </h1>
        <p className = "Dialog-message">
            你好~
        </p>
        </FancyBorder>
    )
}
```

###  有时候你需要很多个地方插口在组件中，而不是只使用children

```react
function Contacts(){
        return <div className = "Contacts"/>;
    }
    function Chat(){
        return <div className = "Chat"/>;
    }
    function SplitPane(props){
        return(){
            <div className = "SplitPane">
            <div className = "SplitPane-left">
                {props.left}
            </div>
            <div className = "SpiltPane-right">
                {props.right}
            </div>
            </div>
        }
}
 
function App(){
    return(
        <SplitPane left = {<Contacts/>} right = {<Chat/>}>
    );
}
 
ReactDOM.render(){
    <App/>,
    document.getElementById()
}
```

 在React中，像`<Contanct/>`或者`<Chat/>`等元素可以看成一个对象，可以像传参数一样将这些数据传递。  

###  特殊情况

 有时候在众多子组件中，可能会有一个特殊的组件。下例中，`WelcomeDialog`就是`Dialog`中特殊的一个  


```react
funtion FancyBorder(props){
    return(
        <div className={'FancyBorder FancyBorder-' + props.color}>
        {props.children}
        </div>
    );
}
function Dialog(props){
    return(
        <FancyBorder color = "blue">
        <h1 className = "Dialog-title">
            {props.title}
        </h1>
        <p className = "Dialog-message">
            {props.message}
        </p>
        </FancyBorder>
    )
}
 
function WelcomeDialog(){
    return(
        <Dialog
        title = "Hi~"
        message = "你好"
        />
    );
}
 
ReactDOM.render(){
    <WelcomeDialog/>,
    document.getElementById('root')
}
```

###  组件对定于为类的同样适用

```react
function FancyBorder(props) {
    return (
        <div className={'FancyBorder FancyBorder-' + props.color}>
        {props.children}
        </div>
    )
}

function Dialog(props){
    return(
        <FancyBorder color = "blue">
            <div className = "Dialog-title">
                <h1>{props.title}</h1>
            </div> 
            <div className = "Dialog-message">
                <p>{props.message}</p>
            </div>
            <div>
                {props.children}
            </div>        
        </FancyBorder>
    )
}

class SignUpDialog extends React.Component{
    constructor(props){
    super(props);
    this.state = {signUpName:''};
    this.handleChange = this.handleChange.bind(this);
    this.handleShow = this.handleShow.bind(this);
}
handleChange(e){
    this.setState({signUpName:e.target.value});
}
handleShow(e){
    alert('你好 ' + this.state.signUpName);
    e.preventDefault();
}
render(){
        return(
            <Dialog
            title = "你好"
            message = "欢迎来到这里"
            >
            <input type = "text" value={this.state.signUpName} onChange = {this.handleChange}/>
            <button value = "Sign Me Up!" type = "Submit" onClick = {this.handleShow}>Sign Me Up!</button>
            </Dialog>
        );
    }
}

ReactDOM.render(
    <SignUpDialog/>,
    document.getElementById('root')
)
```

### 至于继承
就没有了，组件可以接受任意的`props`，包括`原始值`、`反应元素`或`函数`
如果希望在组件之间重用非ui功能，可以将其提取到单独的js模块中，组件可以导入并且使用该函数、对象、或者类，而不是进行扩展  


对应构建React项目的思考
------------------------------

React是使用js构建大型，快速web应用程序的主要方式。  
一般构建一个react应用程序的思考过程如下：   

### 第一步：将ui分解为组件层次结构 
结合设计稿来分层次命名  

### 第二步：在React中构建静态版本   
根据层次结构来构建静态版本的页面，不要有交互。需要构建可重用组件并使用props传递数据的组件。不要使用`state`来构建静态组件。`state`用于`交互性`，即随时间变化的数据            
构建分为`自上而下`跟`自下而上`。大型项目是自下而上，小型则反之。     

第三步：确定ui状态的最小但是完整表示形式

确定是否为`state`可以自问三个问题  
1.通过`props`从父母传入  
2.随着时间推移而保持不变  
3.基于组件中的任何`state`或者`props`来计算  
其中任何一个答案是肯定的则不是`state`  


### 第四步：确定`state`在哪里应该存在
对于应用程序中的每一个`state`  
1.确定每个基于该`state`呈现某些内容的组件  
2.找到一个共同的拥有者组件  
3.无论是公共所有者，还是等级较高的其他组成部分都应该拥有该`state ` 
4.如果无法找到一个有意识拥有这个state的组件，可以创建一个新的组件，只是为了保存`state`并将它添加到公共所有 者组件上方的层次结构中的某处  

### 第五步：添加反向数据流





参考链接：https://react.docschina.org/docs/hello-world.html