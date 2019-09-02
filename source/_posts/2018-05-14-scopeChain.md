---
title: js作用域
date: 2018-05-14 17:54:12
tags: js
categories: 
	- javaScript相关
---



js的作用域
=============
### 作用域

作用：访问、操作、调用..  
域：区域、范围、空间  
作用域就是变量和函数的可访问范围，或者说是变量或者函数起作用的区域  

1.js函数的作用域  
函数内的区域，就是这个函数的作用域，变量和函数在这个区域内都可以访问操作。最外层函数外的区域叫全局作用域，函数内的区域叫局部作用域


2.js变量的作用域  
在源代码中变量所在的区域就是这个变量的作用域。变量在这个区域内可以被访问。在全局作用域上定义的变量叫做全局变量，在函数内部定义的变量叫做局部变量。

全局变量与局部变量  
① 全局作用域（Global Scope）  
最外层函数或者最外层函数定义的变量具有全局作用域

```javascript
var outer = 'outer';
function fn(){
	console.log(outer);
}
fn(); //outer
```

② 所有未定义直接赋值的变量自动声明为全局作用域  

```javascript
function fn(){
	inner= 'inner';
	
}
fn();
console.log(inner); //inner
```

③ 所有window对象的属性拥有全局作用域  

局部作用域（Local Scope）  
局部作用域一般只在固定的代码片段可以访问到，对于函数外部是无法访问的,最常见的例如函数内部   

```javascript
function fn(){
	var inner= 'inner';
	
}
fn();
console.log(inner); //undefined
```

注意：只要函数内定义了一个局部变量，函数在解析的时候都会将这个变量提前声明

```javascript
var global = 'global';
function fn(){
    console.log(global); //undefined
    var global = 'newGlobal';             
    console.log(global); //newGlobal
}
console.log(global); //global
```

相当于

```javascript
var global = 'global';
function fn(){
    var global; //提前声明了局部变量；
    console.log(global); //undefined
    var global = 'newGlobal';             
    console.log(global); //newGlobal
}
console.log(global); //global
```



### 执行环境（Execution Contexts）  

也可以翻译为执行上下文，当解释器进入到ECMAScript的可执行代码，解释器就进入一个执行环境，活动的执行环境组成了逻辑上的一个栈，在这个逻辑栈顶部的执行环境是当前运行的执行环境。  
js为每一个执行环境关联了一个变量对象，环境中定义的所有变量和函数都保存在这个对象中。js的执行顺序是根据函数的调用来决定的，当一个函数的变量对象弹出，把控制权交给了之前的执行环境变量对象。  

变量对象(Variable Object)、活动对象(Activation Object)和Arguments对象(Arguments Object)当前执行环境的变量对象始终在作用域链的第0位。  


解析器处理代码时的两个阶段：

我们都知道javascript解析器是一段一段解析处理代码的，为什么？这就要涉及解析器处理代码时的两个阶段，解析代码和执行代码。

当解析器进入执行环境时，变量对象就会添加执行环境中声明的变量和函数作为它的属性，这就意味着变量和函数在声明之前已经可用，变量值为undefined，这就是变量和函数声明提升(Hoisting)的原因，与此同时作用域链和this确定，此过程为解析阶段，俗称预解析。接着解析器开始执行代码，为变量添加相应值的引用，得到执行结果，此过程为执行阶段。



### 作用域链（Scope Chain）  

作用域链是js内部中一种变量、函数查找机制，它决定了变量和函数的作用范围，即作用域。函数对象和其他对象一样，拥有可以通过代码访问的属性和一系列仅供js引擎访问的内部属性。其中一个内部属性就是scope，该内部属性包含了函数被创建的作用域中对象的集合，这个集合就是被称为函数的作用域链，决定了那些数据能被函数访问。

在函数执行过程中，没遇到一个变量，都会经历一次标识符解析过程以决定从哪里获取和存储数据。该过程从作用域链头部，也就是从活动对象开始搜索，查找同名的标识符，如果找到了就使用这个标识符对应的变量，如果没找到继续搜索作用域链中的下一个对象，如果搜索完所有对象都未找到，则认为该标识符未定义。函数执行过程中，每个标识符都要经历这样的搜索过程。

四、作用域链和代码优化  
从作用域链的结构可以看出，在运行期上下文的作用域链中，标识符所在的位置越深，读写速度就会越慢。如上图所示，因为全局变量总是存在于运行期上下文作用域链的最末端，因此在标识符解析的时候，查找全局变量是最慢的。所以，在编写代码的时候应尽量少使用全局变量，尽可能使用局部变量。一个好的经验法则是：如果一个跨作用域的对象被引用了一次以上，则先把它存储到局部变量里再使用

优化前：

```javascript
function changeColor(){
    document.getElementById("btnChange").onclick=function(){
        document.getElementById("targetCanvas").style.backgroundColor="red";
    };
}
```

优化后：

```javascript
function changeColor(){
    var doc=document;
    doc.getElementById("btnChange").onclick=function(){
        doc.getElementById("targetCanvas").style.backgroundColor="red";
    };
}
```
