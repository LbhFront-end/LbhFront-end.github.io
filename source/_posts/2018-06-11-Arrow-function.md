---
title: Arrow_functions
date: 2018-06-11 17:45:00
tags: ES6
categories: 
	- ES6相关
---

箭头函数
==============

基础语法
----------------

>(参数1,参数2,...参数N) =>{函数声明}  
(参数1,参数2,...参数N) => 表达式(单一)  
//相当于：(参数1,参数2,...参数N) =>{return 表达式;}
>
>//当只有一个参数时，圆括号是可选的:  
(单一参数) => {函数声明}  
单一参数 => {函数声明}  
>
>//无参数的函数应该写成一对圆括号  
() => {函数声明}  

高级语法
------------------

>//加括号的函数体返回对象字面表达式  
参数 =>({foo:bar})  
>
>//支持剩余参数和默认参数  
(参数1,参数2,...rest) => {函数声明}  
(参数1 = 默认值1,参数2,...参数N = 默认值N) => {函数声明}
>
>//同样支持参数列表解构  
let f = ([a,b] = [1,2],{x:c} = {x: a + b}) => a + b +c;  
f(); //6

描述
---------------

引入箭头函数有两个方面的作用：更简短的函数并且不绑定 `this`  

```javascript
var a = ['fiftyfifty','done','you','goodjob'];
a.map(function(a){
    return a.length;
}); //[10,4,3,7]

a.map(a) => {
    return a.length;
} //[10,4,3,7]

a.map(a => a.length); //[10,4,3,7]
```

不绑定 `this`
--------------

在箭头函数出现之前，每个新定义的函数都有自己的 `this` 值(在构造函数的情况下是一个新对象，在严格模式的函数调用中为 `undefined`,如果该函数被称为“对象方法” 则为基础对象等 )

```javascript
funtion Person(){
    //Person() 构造函数定义 `this` 作为自己的实例
    this.age = 0;

    setInterval(function growUp(){
        //在非严格模式，growUp()函数定义 `this` 作为全局对象,
        //在与Person() 构造函数中定义的 `this` 并不相同
        this.age++;
    },1000);
}

var p = new Person();
```

在 `ECMAScript 3/5` 中，通过将 `this` 值分配给封闭的变量，可以解决 `this` 问题。

```javascript
function Person(){
    var that = this;
    this.age = 0;
    setIntervar(function growUp(){
        //回调引用的是 `that` 变量，其值是预期的对象
        that.age++;
    },1000);
}
```

或者，可以创建绑定函数，以便将预先分配的 `this` 值传递到绑定的目标函数上

箭头函数不会创建自己的 `this` ,它会从自己的作用域链的上一层继承 `this` .因此，在下面的代码中，传递给 `setInterval` 的函数内的 `this` 与封闭函数中的 `this` 相同

```javascript
    function Person(){
        this.age = 0;
        setInterval(() =>{
            this.age++; //|this| 正确指向 person 对象
        },1000);
    }
    var p = new Person();
```

通过 `call` 或者 `apply` 调用
----------------------------

由于箭头函数没有自己的 `this` 指针，通过 `call()` 或者 `apply()` 方法调用一个函数时，只能传递参数

```javascript
var adder = {
    base:1,
    add:function(a){
        var f = v => v + this.base;
        return f(a);
    },

    addThruCall: function(a){
        var f = v => v + this.base;
        var b = {
            base: 2
        };
        return f.call(b,a);
    }
}
console.log(adder.add(1)); //2
console.log(adder.addThruCall(1)); //2 不是3
```

不绑定 `arguments`
----------------------

箭头函数不绑定 `arguments 对象`，在下例中， `arguments` 只是引用了封闭作用域内的 `arguments`

```javascript
var arguments = [1,2,3];
var arr = () => arguments[0];
arr(); //1
function foo(n){
    var f = () => arguments[0] + n; //隐式绑定 foo 函数的 arguments 对象，arguments[0] 是 n
    return f();
}

foo(1); //2
```

大多数情况下，使用剩余参数是相较使用 `arguments` 对象的更好选择

```javascript
function foo(){
    var f = (...args) => args[0];
    return f(2);
}

foo(1); //2
```

像方法一样使用箭头函数
--------------------

```javascript
'use strict';
var obj = {
    i:10,
    b:() => console.log(this.i,this),
    c:function(){
        console.log(this.i,this);
    }
}
obj.b();
//undefined
obj.c();
//10,Object{...}
```

使用 `new` 操作符
--------------------

箭头函数不能用作构造器，和 `new` 一起用会抛出错误

>var Foo = () =>{};  
var foo = new Foo(); //TypeError:Foo is not a constructor


使用 `prototype` 属性
--------------------

箭头函数没有 `prototype` 属性
>var Foo = () =>{}  
console.log(Foo.prototype); //undefined

函数体
---------------------

箭头函数可以有一个简写体或者是常见的块体  
在一个简写体重，只需要一个表达式，并附加一个隐式的返回值，在块体中，必须使用明确的 `return` 语句

>var func = x => x * x;  
//简写函数 省略return  
>
>var func = (x,y) => {return x+y;};
//常规编写，明确的返回值

返回对象字面量
----------------

使用 `params =>{object:literal}` 这种简单的语法返回对象字面量是行不通的  
记得用圆括号把字面量包起来  

>var func = () => ({foo:1});

换行
------------------

箭头函数在参数和箭头之间不能换行

解析顺序
------------------

虽然箭头函数中的箭头不是运算符，但箭头函数具有与常规函数不同的特殊运算符优先级解析规则

```javascript
let callback;
callback = callback || function(){}; //ok
callback = callback || (() =>{}); //ok

//空的箭头函数返回 undefined
let empty = () => {}

(() => 'foobar')();
//Return "foobar"
//这是一个立即执行函数表达式
```

箭头函数也可以使用条件（三元）运算符：
------------------------------------

```javascript
var simple = a => a > 15 ? 15 : a;
simple(16); //15
simple(10); //10

let max = (a,b) => a > b ? a : b;
```

箭头函数内定义的变量及其作用域
----------------------------

```javascript
//常规写法
var greeting = () => { let now = new Date();return("Good" + ((now.getHours() > 17) ? "evening." : "day."));}
console.log(now);    // ReferenceError: now is not defined 标准的let作用域

// 参数括号内定义的变量是局部变量（默认参数）
var greeting = (now = new Date()) => "Good" + ((now.getHours() > 17) ? "evening." : "day.");
console.log(now);    // ReferenceError: now is not defined

//对比：函数体内{} 不使用var 定义的变量是全局变量
var greeting = () => {now = new Date(); return ("Good" + ((now.getHours() > 17) ? "evening." : "day."));}
console.log(now);     // Fri Dec 22 2017 10:01:00 GMT+0800 (中国标准时间)

//对比：函数体内用{} 用var定义的变量是局部变量
var greeting = () =>{var now = new Date(); return ("Good" + ((now.getHours() > 17) ? "evening." : "day."));}
console.log(now);    // ReferenceError: now is not defined
```

箭头函数也可以使用闭包
----------------------

```javascript
//标准的闭包函数
function A(){
    var i = 0;
    return function b(){
        return (++i);
    };
};

var v = A();
v(); //1
v(); //2

//箭头函数体的闭包 (i=0 是默认参数)
var Add = (i=0) => {return (() => (++i))};
var v = Add();
v(); //1
v(); //2

//因为只有一个返回，return 以及括号也可以省略
var Add = (i=0) => (++i);
```