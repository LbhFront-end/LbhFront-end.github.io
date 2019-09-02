---
title: 深入JavaScript—— 执行上下文栈
date: 2019-01-12 11:30:00
tags: 深入JavaScript
categories: 
	- JavaScript
---

经一些热心网友推荐，看到了[冴羽](https://github.com/mqyqingfeng) 的深入系列，现做学习与记录，希望可以每天学习一篇，加强巩固自己对于 js 的理解。[原仓库地址](https://github.com/mqyqingfeng/Blog)。

# JavaScript深入之执行上下文栈

## 顺序执行

举个例子：

```javascript
var foo = function(){
    console.log('foo1');
}
foo(); // foo1
var foo = function(){
    console.log('foo2');
}
foo(); // foo2
```

另一个例子：

```javascript
var foo  = function(){
    console.log('foo1');
}
foo(); // foo2

function foo(){
    console.log('foo2');
}
foo(); // foo2
```

JavaScript 引擎并非一行一行地分析和执行程序的，而是一段一段分析执行。当执行一段代码的时候，会进行一个“准备工作”。比如第一个例子中的，变量提升。和第二个例子中的函数提升。

## 可执行代码

分为三种：全局代码、函数代码和 eval 代码

举个例子。当执行到一个函数的时候，会进行准备工作，也就是我们经常听到的 “执行上下文（execution context）”

## 执行上下文栈

JavaScript 引擎创建了执行上下文（Execution context stack,ECS）来管理执行上下文，为了模拟执行上下文栈的行为，让我们定义执行上下文是一个数组：

```javascript
ECStack = [];
```

当 JavaScript 开始要解释执行代码的时候，最先遇到的是全局代码，所以初始化的时候会首先向执行上下文栈压如一个全局执行上下文，我们用 globalContext 表示它，并且只有当整个应用程序结束的时候，ECStack 才会被清空，所以当程序结束之前，ECStack 最底部永远有一个 globalContext：

```javascript
ECStack = [
    globalContext
]
```

现在 JavaScript 遇到下面这段代码：

```javascript
function func3(){
    console.log('func3');
}
function func2(){
    func3();
}
function func1(){
    func2();
}
func1();
```

当执行一个函数的时候，就会创建一个上下文，并且压入执行上下文栈，当函数执行完毕的时候，就会将函数的执行上下文从栈中弹出，知道了这样的工作原理之后，让我们来看看如何处理上面这段代码：

```pseudocode
// 伪代码
// func1()
ECStack.push(<func1> functionContext);

// func1 中竟然调用了 func2，还要创建 func2 的执行上下文
ECStack.push(<func2> functionContext);

// func2 还调用了func3
ECStack.push(<func3> functionContext);

// func3 执行完毕
ECStack.pop();

// func2 执行完毕
ECStack.pop();

// func1 执行完毕
ECStack.pop();

// javascript 接着执行下面的代码，但是 ECStack 底层永远有个 globalContext
```

## 解答思考题

```javascript
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f();
}
checkscope();

var scope = "global scope";
function checkscope(){
    var scope = "local scope"
    function f(){
        return scope;
    }
    return f;
}
checkscope()();

```

两段代码不一样的地方主要体现在执行上下文栈的不一样

模拟第一段代码：

```pseudocode
ECStack.push(<checkscope> functionContext)
ECStack.push(<f> functionContext)
ECStack.pop();
ECStack.pop();
```

模拟第二段代码：

```pseudocode
ECStack.push(<checkscope> functionContext)
ECStack.pop();
ECStack.push(<f> functionContext)
ECStack.pop();
```

