---
title: React 高级指引 D1
date: 2018-07-05 19:12:45
tags: react
categories: 
	- react相关
---
React 高级指引学习笔记-第一天
============================

深入 `JSX`
--------------

在本质上讲， `JSX` 只是为 `React.createElement(component,props,...children)` 方法提供的与语法糖  

```react
<MyButton color="blue" shadowSize = {2}> Click Me </MyButton>
```

编译为：

```react
React.createElement( MyButton , { color : 'blue' , shadowSize : 2} , 'Click Me' );
```

如果没有子代，可以用闭合标签

```react
<div className = 'sidebar' />
```

编译为：

```react
React.createElement( 'div' , { className : 'sidebar' } , null);
```



指定 `React` 元素类型
--------------------

`JSX` 的标签名决定了 `React` 元素的类型  
大写开头的 `JSX` 标签标示一个 `React` 组件，这些标签会被编译为同名变量并被引用，所以如果使用了 `<Foo/>` 表达式，就必须在作用域先声明 `Foo` 变量

### `React` 必须声明

由于 `JSX` 编译后会调用 `React.createElement` 方法，所以在 `JSX` 代码中必须首先声明 `React` 变量  
下面的导入都是必须的，尽管在代码中没有直接引用  

```react
import React from 'react';
import CustomButton from './CustomButton';
function WarningButton(){
  return <CustomButton color = "red">
}
```

如果使用 `<script>` 加载 `React` ，将作用于全局

### 点表示法

使用 `JSX` 中的点来引用 `React` 组件，可以方便地从一个模块中导出许多 `React` 组件  

```react
import React from 'react';
const MyComponents = {
  DatePicker : function DatePicker(props){
    return <div> Imagine a { props.color } datepicker here.</div>
  }
}

function BlueDatePicker(){
  return <MyCompoents.DatePicker color = "blue">
}
```

### 首字母大写

当元素类型以小写字母开头时，它表示一个内置组件，如 `<div>` 或 `<span>`,并将字符串 `'div'` 或 `'span'` 传递给 `React.createElement` 。以大写字母开头的类型，如 `<Foo />` 编译为 `React.createElement(Foo)` ，并它正对应与在 `JavaScript` 文件中定义或导入的组件

例如，下面的代码将无法按预期运行：

```react
import React from 'react';

// 错误！组件名应该首字母大写:
function hello(props) {
  // 正确！div 是有效的 HTML 标签:
  return <div>Hello {props.toWhat}</div>;
}

function HelloWorld() {
  // 错误！React 会将小写开头的标签名认为是 HTML 原生标签:
  return <hello toWhat="World" />;
}
```

为了解决这个问题，我们将 `hello` 重命名为 `Hello`，然后使用 `<Hello />` 引用：

```react
import React from 'react';

// 正确！组件名应该首字母大写:
function Hello(props) {
  // 正确！div 是有效的 HTML 标签:
  return <div>Hello {props.toWhat}</div>;
}

function HelloWorld() {
  // 正确！React 能够将大写开头的标签名认为是 React 组件。
  return <Hello toWhat="World" />;
}
```

### 在运行时选择类型

你不能使用表达式来作为 `React` 元素的标签。如果你的确想通过表达式来确定 `React` 元素的类型，请先将其赋值给大写开头的变量。这种情况一般会在你想通过属性值条件渲染组件时出现：

```react
import React from 'react';
import { PhotoStory, VideoStory } from './stories';

const components = {
  photo: PhotoStory,
  video: VideoStory
};

function Story(props) {
  // 错误！JSX 标签名不能为一个表达式。
  return <components[props.storyType] story={props.story} />;
}
```

要解决这个问题，我们需要先将类型赋值给大写开头的变量。

```react
import React from 'react';
import { PhotoStory, VideoStory } from './stories';

const components = {
  photo: PhotoStory,
  video: VideoStory
};

function Story(props) {
  // 正确！JSX 标签名可以为大写开头的变量。
  const SpecificStory = components[props.storyType];
  return <SpecificStory story={props.story} />;
}
```

属性
--------------------

在 `JSX` 中有几种不同的方式来指定属性

### 使用 `JavaScript` 表示式

可以传递任何 `{}` 包裹的 `JavaScript` 表示式作为一个属性值

```react
<MyComponent foo = { 1 + 2 + 3 + 4 }>
```

`if` 语句和 `for` 循环在 `JavaScript` 中不是表达式，因此它们不能直接在 `JSX` 中使用，但是可以把它们放在周围的代码中

```react
function NumberDescriber(props){
  let description;
  if(props.number % 2 == 0){
    description = <strong>even</strong>
  } else{
    description = <i>odd</i>
  }
  return <div>{props.number} is an {description} number </div>
}
```

### 字符串常量

可以将字符串常量作为属性值传递，下面的 `JSX` 是等价的

```react
<MyCompoent message = 'hello world' />  
<MyConmpoent message = { 'hello world' }>
```



当传递一个字符串常量时，该值会被解析为 `HTML` 非转义字符串，所以下面的 `JSX` 表达式时相同的

```react
<MyCompoent message = '3' />  
<MyConmpoent message = { '<3' }>
```

### 默认为 `True`

如果没有给属性值，它默认有属性传值，默认为 `true` 

```react
<MyCompoent autocomplete />  
<MyConmpoent autocomplete  = { true }>
```

一般情况下，不建议这么使用，因为它hi与 `ES6对象简洁表示法混淆`。比如 `{foo}` 是 `{foo: foo}` 的简写，而不是 `{foo: true}`。这里能这样用，是因为它符合 `HTML` 的做法。

### 扩展属性

如果已经有 `props` 属性，并且想在 `JSX` 中传递它，可以使用 `...` 作为扩展操作符来传递整个属性对象

```react
function App1(){
  return <Greeting firstName = "Ben" lastName = "Hector" />;
}

function App2(){
  const props = { firstName : 'Ben' , lastName : 'Hector' };
  return <Greeting {...props} />
}
```

当构造通用容器时，扩展属性会非常有用，然而这样做也有可能让很多不相关的属性，传递到不需要它们的组件中使得代码非常混乱，谨慎这个用法

子代
--------------------

在包含开始和结束标签的 `JSX` 表达式中，标记之间的内容作为特殊的参数传递 ： `props.children` ，有几种不同的方法来传递子代

### 字符串常量-

可以在开始和结束标签之间放入一个字符串，则 `props.children` 就是那个字符串，对于很多内置 `HTML` 元素很有用

```react
<MyComponent> Hello World! </MyComponent>
```

这是有效的 `JSX` ，并且 `MyComponent` 的 `props.children` 值将会直接是 `hello world!` 因为 `HTML` 未转义，所以可以像 `HTML` 一样写 `JSX` 

```react
<div> This is valid HTML \ JSX at the same time </div>
```

`JSX` 会移除空行和开始与结尾处的空格。标签邻近的新行也会被移除，字符串常量内部的换行会被压缩成一个空格，所以下面这些都等价：

```react
<div>Hello World</div>

<div>
  Hello World
</div>

<div>
  Hello
  World
</div>

<div>

  Hello World
</div>
```

### JSX

可以通过子代嵌入更多的 JSX 元素，这对于嵌套显示组件非常有用：

```react
<MyContainer>
  <MyFirstComponent />
  <MySecondComponent />
</MyContainer>
```

你可以混合不同类型的子元素，同时用字符串常量和 `JSX` 子元素，这是 `JSX` 类似 `HTML` 的另一种形式，这在 `JSX` 和 `HTML` 中都是有效的：

```react
<div>
  Here is a list:
  <ul>
    <li>Item 1</li>
    <li>Item 2</li>
  </ul>
</div>
```

`React` 组件也可以通过数组的形式返回多个元素：

```react
render() {
  // 不需要使用额外的元素包裹数组中的元素
  return [
    // 不要忘记 key :)
    <li key="A">First item</li>,
    <li key="B">Second item</li>,
    <li key="C">Third item</li>,
  ];
}
```

### `JavaScript` 表达式

可以将任何 `{}` 包裹的 `JavaScript` 表达式作为子代传递。例如，下面这些表达式是等价的：

>\<MyComponent>foo\</MyComponent>  
\<MyComponent>{'foo'}\</MyComponent>

这对于渲染任意长度的 JSX 表达式的列表很有用。例如，下面将会渲染一个 `HTML` 列表：

```react
function Item(props){
  return <li>{ props.message }</li>
}
function TodoList(){
  const todos = ['finish doc' , 'submit pr' , 'nag dan to review'];
  return(
    <ul> {todos.map( (message) => <Item message = { message } key = { message }> )} </ul>
  )
}
```

`JavaScript` 表达式可以与其他类型的子代混合使用，这通常对于字符串模板非常有用

```react
function Hello(props){
  return <div> Hello { props.address } !</div>
}
```

### 函数

通常情况下，插入 `JSX` 中的 `JavaScript` 表达式认作字符串, `React` 或这些内容的列表。然而， `props.children` 可以像其他属性一样传递任何数据，而不仅仅是 `React` 元素。例如，如果你使用自定义组件，则可以将调用 `props.children` 来获得传递的子代

```react
function Repeat(props){
  let items = [];
  for(let i = 0; i < props.numTimes; i++){
    item.push( props.children(i) );
  }
  return <div>{ item }</div>
}

function ListOfTenThings(){
  return(
    <Repeat numTimes = {10}>
      { (index) => <div key ={index}>This is item {index} in the list </div> }
    </Repeat>
  )
}
```

传递给自定义组件的子代可以是任何的元素，只要该组件在 `React` 渲染钱将其转换成 `React` 能够理解的东西，这个用法并不常见，但是当你想扩展  `JSX` 时可以使用

### 布尔值 、 `Null` 和 `Undefined` 被忽略

`false` 、 `null` 、 `undefined` 和 `true` 都是有效的子代，但它们不会直接被渲染。下面的表达式是等价的：

```react
<div />

<div></div>

<div>{false}</div>

<div>{null}</div>

<div>{undefined}</div>

<div>{true}</div>
```

这在根据条件来确定是否渲染 `React` 元素时非常有用。以下的 `JSX` 只会在 `showHeader` 为 `true` 时渲染 `<Header />` 组件。

```react
<div>
  {showHeader && <Header />}
  <Content />
</div>
```

值得注意的是，`JavaScript` 中的一些 “`falsy`” 值(比如数字0)，它们依然会被渲染。例如，下面的代码不会像你预期的那样运行，因为当 `props.message` 为空数组时，它会打印 `0`:

```react
<div>
  {props.messages.length &&
    <MessageList messages={props.messages} />
  }
</div>
```

要解决这个问题，请确保 `&&` 前面的表达式始终为布尔值：

```react
<div>
  {props.messages.length > 0 &&
    <MessageList messages={props.messages} />
  }
</div>
```

相反，如果你想让类似 `false` 、`true` 、`null` 或 `undefined` 出现在输出中，你必须先把它转换成字符串 :

```react
<div>
  My JavaScript variable is {String(myVariable)}.
</div>
```

注解：
---------

`JavaScript` 中 `falsy` 值的例子 (将 `falsy` 值转换为 `false` ):

```react
if (false)  
if (null)  
if (undefined)  
if (0)  
if (NaN)  
if ('')  
if ("")  
if (document.all)  
```

