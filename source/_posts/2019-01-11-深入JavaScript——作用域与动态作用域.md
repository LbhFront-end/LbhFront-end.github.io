---
title: 深入JavaScript—— 作用域与动态作用域
date: 2019-01-10 11:30:00
tags: 深入JavaScript
categories: 
	- JavaScript
---

经一些热心网友推荐，看到了[冴羽](https://github.com/mqyqingfeng) 的深入系列，现做学习与记录，希望可以每天学习一篇，加强巩固自己对于 js 的理解。[原仓库地址](https://github.com/mqyqingfeng/Blog)。

# JavaScript深入之词法作用域和动态作用域

## 作用域

作用域是指程序源代码中定义变量的区域。

作用域规定了如何查找变量，也就是确定当前执行代码对变量的访问权限。

JavaScript 采用词法作用域（lexical scoping），也就是静态作用域。

## 静态作用域与动态作用域

因为 JavaScript 采用的是词法作用域，函数的作用域在函数定义的时候就决定了。而与词法作用域相对的是动态作用域，函数的作用域是在函数调用的时候才决定的。下面举个例子

```javascript
var value = 1;
function foo(){
    console.log(value);
}

function bar(){
    var value = 2;
    foo();
}

bar(); // 结果是什么？
```

假设 JavaScript 采用静态作用域，让我们分析一下执行过程：

执行 foo 函数，先从 foo 函数内部查找是否有局部变量 value,如果没有，就根据书写的位置，查找上一层的代码，也就是 value 等于 1，所以结果会打印 1.

假设 JavaScript 采用的是动态作用域，让我们分析以下执行过程：

执行 foo 函数，依然是从 foo 函数内部查找是否有局部变量 value,如果没有，就从调用函数的作用域，也就是从 bar 函数内部查找 value 变量，所以打印结果是 2.

前面我们说了，JavaScript 采用的是 静态作用域，所以这里的打印结果是 1.

## 动态作用域

`bash` 就是动态作用域



## 思考题

看一个 《JavaScript 权威指南》 的例子

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

两段代码打印出来的都是：local scope ，因为 JavaScript 采用的是词法作用域 ，函数的作用域基于函数创建的位置。

JavaScript 函数的执行用到了作用域链，这个作用域链是在函数定义的时候创建的。嵌套在函数 f（）定义在这个作用域链里，其中的变量 scope 一定是局部变量，不管何时何地执行函数 f（）,这种绑定在执行 f（）时依然有效。