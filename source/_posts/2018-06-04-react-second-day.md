---
title: react-day2
date: 2018-06-04 16:00:45
tags: react
categories: 
	- react相关
---
react 学习笔记day-02
===========

处理事件
-------------------------
使用React元素处理事件与处理DOM元素上的事件非常相似，有一些语法差异  
React事件使用camelCase命名，而不是小写  
使用JSX，将传递函数作为事件处理函数，而不是字符串  

HTML：

>`<button onclick = "activateLasers()">Activate Lasers</button>`

React:
>`<button onClick={activateLasers}>Activate Lasers</button>`

另外一个区别是，无法返回`false`以防`React`的默认行为，必须使用`preventDefault`明确调用。

HTML:  

>`<a href="#" onclick="conslog.log('The link was clicked'); return false">Click me</a>`

React：  

```react
function ActionLink(){
    function handleClick(e){
        e.preventDefault();
        console.log('The link was clicked.');
    }
    return(
        <a href="#" onClick={handleClick}>
        Click me 
        </a>
    );
}
```

使用React，通常不需要调用addEventListener添加侦听器到DOM元素后创建，相反，只需在元素初始呈现时提供侦听器  
Toggle组件  

```react
class Toggle extends React.Component{
    constructor(props){
        super(props);
        this.state = {isTogglenOn:true};
        
        this.handleClick = this.handleClick.bind(this);
    }
    handleClick(){
        this.setState(prevState => ({
        isToggleOn: !prevState.isToggleOn
        }));
    }
    render(){
        return(
        <button onClick={this.handleClick}>
        {this.state.isToggleOn?'ON'?'OFF'}
        </button>
        );
    }
}

React.render(
    <Toggle/>,
    document.getElementById('root');
);
```

如果引用一个没有`（）`后面的方法，比如`onClick={this.handleClick}`你应该绑定那个方法

```react
class LoggingButton extends React.component{
    handleClick = () =>{
        console.log('this is ',this);
    }
    render(){
        return(
        <button onclick={this.handleClick}>
            Click me
        </button>
        )
    }
}
```

### 将参数传递给事件处理程序   
在循环内部，通常需要将一个额外的参数传递给事件处理程序。下例，`id`是行`ID`  
>`<button onClick={(e) => this.deleteRow(id,e)}>Delete Row</button>`  
>`<button onClcik={this.deleteRow.bind(this,id)}>Delete Row</button>`

`e`代表React事件的参数都将作为ID之后的第二个参数传递，使用箭头函数，我们必须明确传递它，但是bind任何更多的参数都会自动转发  


有条件的渲染
---------------
React中，可以创建封装所需行为的独特组件，可以仅渲染其中的一部分，具体取决于应用程序的状态  
React中的条件呈现与条件跟js的工作方式相同，使用js的if运算符，或者是条件运算符创建表示当前状态的元素，并让React更新UI来匹配它们  

```react
function UserGreeting(props){
    return <h1>Welcome back!</h1>;
}
function GuestGreeting(props){
    return <h1>Please sign up.</h1>
}

function Greeting(props){
    const isLoggedIn = props.isLoggedIn;
    if(isLoggedIn){ 
        return <UserGreeting/>;
    }
    return <GuserGreeting/>;
}
React.render(
    <GuestGreeting isLoggedIn={false}/>,
    documenet.getElementById('root');
);
```

### 元素变量
可以使用变量来存储元素，有条件地渲染组件的一部分，而其余输出不会更改  

代表注销和登录按钮的新组件

```react
function LoginButton(props){
    return(
        <button onClick = {props.onClick}>
        Login
        </button>
    );
}
function LogoutButton(props){
    return(
        <button onClick = {props.onClick}>
        Logout
        </button>
    );
}
```

创建有状态的组件`LoginControl`,呈现`<LoginButton/>`或`<LogoutButton/>`根据其当前状态，它也会呈现一个`<Greeting/>`  

```react
class LoginControl extends React.Component{

    constructor(props){
        super(props);
        this.handleLoginClick = this.handleLoginClick.bind(this);
        this.handleLogoutClick = this.handleLogoutClick.bind(this);
        this.state = {isLoggedIn : false};
    }

    handleLoginClick(){
        this.setState({isLoggedIn:true});
    }

    handleLogoutClick({
        this.setState({isLoggedIn:false});
    });

    render(){
        const isLoggedIn = this.state.isLoggedIn;
        let button = null;
        if (isLoggedIn) {
            button = <LogoutButton onClick={this.handleLogoutClick} />;
        } else {
            button = <LoginButton onClick={this.handleLoginClick} />;
        }
        return(
            <div>
            <Greeting isLoggedIn = {isLoggedIn} />
            {button}
            </div>
        );
    }
}

function UserGreeting(props){
    return <h1>Welcome back!</h1>
}

function GuestGreeting(props){
    return <h1>Please sign up.</h1>
}

function Greeting(props){
    const isLoggedIn = props.isLoggedIn;
    if(isLoggedIn){
        return <UserGreeting />;
    }
    return <GusetGreeting />;
}

function LoginButton(props){
    return(
        <button onClick = {props.onClick}>
        Login
        </button>
    );
}

function LogoutButton(){
    return(
        <button onClick = {props.onClick}>
        Logout
        </button>
    )
}

ReactDOM.render(
    <LoginControl />,
    document.getElementById('root');
);
```

### 内联使用逻辑&&运算符
包裹在花括号中嵌入JSX的任何表达式中  

```react
function Mailbox(props){
    const unreadMessage = props.unreadMessage;
    return(
        <div>
        <h1>Hello</h1>
        {unreadMessage.length > 0 && 
        <h2> You have {unreadMessage.length } unread message</h2>
        }
        </div>
    );
}

const message = ['React','Re:React','Re:Re:React'];
    ReactDOM.render(
    <Mail unreadMessage = {message} />,
    document.getElementById('root');
);
```

### 与条件元素内联使用if-else
eg1:  

```react
render(){
    const isloggedIn = this.state.isLoggedIn;
    return(
        <div>
        The user is <b> {isLoggIn ? 'currently' : 'not'}</b> logged in
        </div>
    );
}
```

eg2:

```react
render(){
    const isLoggedIn = this.state.isLoggedIn;
    return(
        <div>
        {
            isLoggedIn ? (
            <LogoutButton onClick = {this.handleLogoutClick /}> 
            ):(
            <LoginButton onClick = {this.handleLoginClick} />
            )
        }
        </div>
    );
}
```
### 防止组件呈现
组件可以隐藏自身，`null  `

```react
function WarningBanner(props){
    if(!props.warn){
        return null;
    }
    return(
        <div className="warning">
        Warning!
        </div>
    );
}

class Page extends React.Component{
    constructor(props){
        super(props);
        this.state = {showWarning: true};
        this.handleToggleClick = this.handleToggleClick.bind(this);
    }
}

handleToggleClick(){
    this.setState(prevState =>({
        showWarning : !prevState.showWarning
    }));
}

render(){
    return(
        <div>
        <WarningBanner warn = {this.state.showWarning} />
        <button onClick = {this.handleToggleClick}>
            {this.state.showWarning ? 'Hide': 'Show'}
        </button>
        </div>
    );
}

ReactDOM.render(
    <Page/>,
    document.getElementById('root')
);
```

`null`从组件的`render`方法返回不会影响声明周期方法的触发

列表和键
-------------------------
js中转换列表，`map`函数来获取numbers并将其值加倍

```react
const numbers = [1,2,3,4,5];
const doubled = numbers.map((number) => number*2);
console.log(doubled);
```

而在React中，将数组转换为元素列表几乎完全一样  

### 渲染多个组件
可以构建元素集合，并使用大括号将其包含在JSX中 `{}  `
numbers使用js map函数遍历数组，`li`为每个项目返回一个元素，最后将得到的元素数组分配给`listItems  ` 

```react
const numbers = [1,2,3,4,5];
const listItems = number.map((number) => <li>{number}</li>);
```

再将整个`listItems`数组包含在一个`ul`元素中，并将其呈现给`DOM`

```react
ReactDOM.render(
    <ul>{listItems}</ul>,
    document.getElementById('root')
);
```

基本列表组件

组件中渲染列表  
可以将前面的例子重构为一个组件，该组件接受一个数组numbers并输出一个无序的元素列表  

```react
function NumberList(props){
    const number = props.number;
    const listItems = number.map((number) => <li>{number}</li>);
    return(
        <ul> {listItems} </ul>
    );
}

const number = []1,2,3,4,5];
ReactDOM.render(
    <NumberList numbers = {numbers} />,
    document.getElementById('root')
);
```

**运行这段代码时，会给你一个警告，即应该为列表项提供一个密钥。“键”是在创建元素列表时需要包含的特殊字符串属性**


```react
function NumberList(props){
    const numbers = props.numbers;
    const listItems = number.map((number) => 
        <li key={number.toString()}>
        {number}
        </li>
    );
    return(
        <ul>{listItems}</ul>
    );
}
const numbers = [1,2,3,4,5];
ReactDOM.render(
    <NumberList number = {numbers} />,
    document.getElementById('root')
);
```

### 键
可以帮助React识别哪些项目已经更改，添加，删除。键应该赋予数组内稳定的元素   

```react
const numbers - [1,2,3,4,5];
const listItems = number.map((number) => 
<li key = {number.toString()}>
    {number}
</li>
);
```

### 选择id作为标志关键字比较多

```react
const todoItems = todo.map(
(todo) => <li key = {todo.id}>
    {todo.text}
</li>
);
```

如果没有稳定id，可以使用索引

```react
const todoItems = todos.map((todo,index) =>
<li key = {index}>
    {todo.text}
</li>
);
```

如果项目的顺序会改变的话，则不建议使用索引，这可能会对性能造成负面影响，并可能带来组件状态问题。

### 用键提取组件

提取listItem组件，保留数组中的元素而不是本身li元素  

```react
function ListItem(props){
    return <li>{props.value}</li>
}
function NumberList(props){
    const numbers = props.number;
    const listItems = numbers.map((number) =>
        <ListItem key = {number.toString()} value = {number} />
    );
    return(
        <ul>{listItems}</ul>
    );
}
const numbers = [1,2,3,4,5];
ReactDOM.render(
    <NumberList numbers = {numbers} />,
    document.getElementById('root')
);
```

key在兄弟姐妹中必须是唯一的  
在我们生成两个不同数组的时候，我们可以用相同的键  

```react
function Blog(props){
    const sidebar = (
        <ul>
        {props.posts.map(post) =>{
            <li key = {post.id}>{post.title}</li>
        }}
        </ul>
    );
    const content = props.posts.map((post) =>
        <div key = {post.id}>
        <h3>{post.title}</h3>
        <p>{post.content}</p>
        </div>
    );
    return (
        <div>
        {sidebar}
        <hr/>
        {content}
        </div>
    );
}

const posts = [{id;1,title:'title1',content:'content1'},{id;2,title:'title2',content:'content2'}];
ReactDOM.render(
    <Blog posts = {posts}>,
    document.getElementById('root')
);
```

### 在JSX嵌入map()

```react
function NumberList(props){  
    const numbers = props.number;   
    return(  
        <ul>  
        {number.map(number) =>  
            <ListItem key = {number.toString()} value = {number} />  
        }  
        </ul>  
    );
}
```

这样嵌套使用有可能提高了可读性，但是如果map嵌套起来太复杂的话就可以考虑提取组件出来  

形式forms
----------------------
### 受控组件
表单元素（Input/textarea/select）通常保持自己的状态并根据用户输入更新，在React中，可变状态通常保存在组件的状态属性中，并且更新只能使用setState()  
可以通过使React状态成为单一真相源来结合着两者，然后呈现表单的React组件也控制后续用户输入中以该表单发生的事情。输入表单元素的值由React以这种方式控制，叫做‘受控组件’   

```react
class NameForm extends React.Component{
    constructor(props){
        super(props);
        this.state = {value:''};
        this.handleChange = this.handleChange.bind(this);
        this.handleSubmit = this.handleSubmit.bind(this);
    }
}

handleChange(event){
    this.setState({value:event.targer.value});
}

handleSubmit(event){
    alert('提交的值为： ' +this.state.value);
    event.preventDefault();
}

render(){
    return(
        <form onSubmit = {this.handleSubmit}>
        <label>
            Name:
            <input type="text" value = {this.state.value} onChange = {this.handleChange} />
        </label>
        <input type="submit" value="Submit"/>
        </form>
    );
}

ReactDOM.render(){
    <NameForm />,
    document.getElementById('root')
}
```

由于value属性设置在表单元素上，因此显示的值将始终为this.state.value,使React状态成为真值的来源，由于handleChange每次击键时都会运行以更新React状态，因此显示的值将随用户键入而更新  

对于受控组件，每个状态变异都会有一个关联的处理函数，这使修改或验证用户输入变得非常简单，例如我们想强制全部大写来编写名称   

```react
handleChange(event){
    this.setState({value:event.target.value.toUpperCase()});
}
```

### textarea标签

HTML中：
>`<textarea>textarea</textarea>`  

React中，`value`属性代表textarea的`值`

```react
class EssayForm extends React.Component{

    construct(props){
        super(props);
        this.state = {
        value:'textarea 123321'
        };
        this.handleChange = this.handleChange.bind(this);
        this.handleSubmit = this.handleSubmit.bind(this);
    }

    handleChange(){
        this.setState({value:event.target.value});
    }

    handleSubmit(){
        alert('new textarea content is' +this.state);
        event.preventDefault();
    }

    render(){
        return(
        <form onSubmit = {this.handleSubmit}>
            <label>
            textarea:
            <textarea value = {this.state.value} onChange = {this.handleChange} />
            </label>
            <input type="submit" value="Submit">
        </form>
        );
    }
};

ReactDOM.render(
    < EssayForm/>,
    document.getElementById('root')
);
```

### 选择标签
HTML中：

```react
<select>
    <option value="1">1</option>
    <option value="2">2</option>
    <option value="3" selected>3</option>
    <option value="4">4</option>
</select>
```

React中：

```react
class FlavorForm extends React.Component{

    constructor(props){
        super(props);
        this.state = {value:'2'}
        this.handleChange = this.handleChange.bind(this);
        this.handleSubmit = this.handleSubmit.bind(this);
    }

    handleChange(event){
        this.setState({value:event.target.value});
    }

    this.handleSubmit(event){
        alert('选中’+this.target.value);
        event.preventDefault();
    }

    render(){
        return(
        <form onSubmit = {this.handleSubmit}>
            <select onChange = {this.handleChange} value={this.state.value}>
            <option value="1">1</option>
            <option value="2">2</option>
            <option value="3">3</option>
            <option value="4">4</option>
            </select>
            <input type="submit" value="Submit"/>
        <form/>
        );
    }
}

ReactDOM.render(
    <FlavorFrom />,
    document.getElementById('root')
);
```

总的来说，`<input type="text">`,`<textarea>`和`<select>`所有的工作过程都比较相似，都可以接受`value`，可以通过它实现控制组件的属性  

**注意：**   
>
>可以将数组传递给value属性，从而在select标签中选择多个选项
>
><select multiple = {true} value={['B','C']}>

文件输入标签

它的值是只读的，所以它是React中不受控制的组件  

HTML：
>`<input type = "file>"`

### 处理多个输入

当需要处理多个受控input元素时，可以name为每个元素添加一个属性，并让成处理函数根据值来选择要执行的操作`event.target.name`  

```react
class Reservation extends React.Component{

    constructor(props){
        super(props);
        this.state = {
        isGoing:true,
        numberOfGuset:2
        };
        this.handleInputChange = this.handleInputChange.bind(this);
    }

    handleInputChange(event){
        const target = event.target;
        const value = target.type === 'checkbox' ? target.checked : target.value;
        const name = target.name;
        
        this.setState({
        [name]:value
        });
    }

    render(){
        return{
        <form>
            <label>
            Is going:
            <input 
                name = "isGoing"
                type = "checkbox"
                checked = {this.state.isGoing}
                onChange = {this.handleInputChange}
            />
            </label>
            <br/>
            <label>
            Number of guests
            <input 
                name = "numberOfGuests"
                type = "number"
                value = {this.state.numberOfGuset}
                onChange = {this.handleInputChange}
            />
            </label>        
        </form>
        }
    }
}

    ReactDOM.render(
    <Reservation/>,
    document.getElementById('root')
);
```

其中 

```react
this.setState({
    [name]:value
});
```

相当于下面的这个es5代码:

```react
var pratialState = {};
pratialState[name] = value;
this.setState(pratialState);
```

### 受控组件的替代品
使用受控组件有时会非常乏味，因为您需要为数据可以改变的每种方式编写一个事件处理程序，并通过React组件管理所有输入状态。当您将预先存在的代码库转换为React或将React应用程序与非React库集成时，这会变得特别烦人。在这些情况下，您可能需要检查不受控制的组件，这是实现输入表单的另一种技术。


九、提升状态`Lifting State Up`  
通常几个组件需要放映相同的变化数据，建议将共享状态提升至最接受的共同祖先  
温度计算器，用于计算在给定温度下水是否沸腾  

```react
funtion BoilingVerdict(props){
    if(props.celsius >=100){
        return <p>The water would boil</p>
    }
    return <p> The water would not bold.</p>
}

class Calulator extends React.Component{

    constructor(props){
        super(props);
        this.handleChange = this.handleChange.bind(this);
        this.state = {temperature:''};
    }

    handleChange(e){
        this.setState({temperature:e.target.value});
    }
    
    render(){
        const temperature = this.state.temperature;
        return( 
        <fieldset>
            <legend>Enter temperature in Celsius:</legend>
            <input 
            value = {temperature}
            onChange = {this.handleChange}
            />
            <BoilingVerdict celsius={parseFloat(temperature)}/>
        </fieldset>
        )
    }
}

ReactDOM.render(
    <Calulator/>,
    document.getElementById('root')
);
```

### 华氏 摄氏同时转换判断是否水沸腾(props/state的区别使用)

```react
const scaleNames = {
    c:'Celsius',
    f:'Fahrenheit'
};

function toCelsius(fahrenheit){
    return (fahrenheit-32)*5/9;
}

function toFahrenheit(celsius){
    return (celsius*9/5)+32;
}

function tryConvert(temperature,convert){
    const input = parseFloat(temperature);
    if(Number.isNaN(input)){
        return '';
    }
    const output = convert(input);
    const rounded = Math.round(output * 1000)/1000;
    return rounded.toString();
}

funtion BoilingVerdict(props){
    if(props.celsius >=100){
        return <p>The water would boil</p>
    }
    return <p> The water would not bold.</p>
}

class TemperatureInput extends React.Component{

    constructor(props){
        super(props);
        this.handleChange = this.handleChange.bind(this);
    };

    handleChange(e){
        this.props.onTemperatureChange(e.target.value);
    }

    render(){
        const temperature = this.props.temperature;
        const scale = this.props.scale;
        return(
        <fieldset>
            <legend>Enter temperature in {scaleNames[scale]}:</legend>
            <input value={temperature}
                onChange={this.handleChange} />
        </fieldset>
        );
    }
}

class Calculator extends React.Component{

    constructor(props){
        super(props);
        this.handleCelsiusChange = this.handleCelsiusChange.bind(this);
        this.handleFahrenheitChange = this.handleFahrenheitChange.bind(this);
        this.state = {temperature: '', scale: 'c'};
    }

    handleCelsiusChange(temperature){
        this.setState({scale:'c',temprature});
    }

    handleFahrenheitChange(temperature){
        this.setState({scale:'f',temprature});
    }
    
    render(){
        const scale = this.state.scale;
        const temperature = this.state.temperature;
        const celsius = scale === 'f' ? tryConvert(temperature,toCelsius) : temperature;
        const fahrenheit = scale === 'c' ? tryConvert(temperature,toFahrenheit) : temperature;
        return(
        <div>
            <TemperatureInput 
            scale = "c"
            temperature = {celsius}
            onTemperatureChange = {this.handleCelsiusChange}
            />
            <TemperatureInput 
            scale = "f"
            temperature = {fahrenheit}
            onTemperatureChange = {this.handleFahrenheitChange}
            />       
            <BoilingVerdict
            celsius={parseFloat(celsius)} /> 
        </div>
        );
    }
}

ReactDOM.render(
    <Calculator />,
    document.getElmentById('root')
);
```





参考链接：https://react.docschina.org/docs/hello-world.html