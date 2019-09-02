---
title: var、let、const三者的区别
date: 2018-05-15 23:12:45
tags: js
categories: 
	- javaScript相关
---
JS中的块级作用域，var、let、const三者的区别
=============


首先，ECMAScript和JavaScript关系： 
      ECMAScript是一个国际通过的标准化脚本语言。JavaScript由ECMAScript和DOM、BOM三者组成。可以简单理解为：ECMAScript是JavaScript的语言规范，JavaScript是ECMAScript的实现和扩展。

### 块作用域{ }  

JS中作用域有：全局作用域、函数作用域。没有块作用域的概念。ECMAScript 6(简称ES6)中新增了块级作用域。 
块作用域由 { } 包括，if语句和for语句里面的{ }也属于块作用域。   

```javascript
{
    var a = 1;
    console.log(a); // 1
}
console.log(a); // 1
// 可见，通过var定义的变量可以跨块作用域访问到。

(function A() {
    var b = 2;
    console.log(b); // 2
})();
// console.log(b); // 报错，
// 可见，通过var定义的变量不能跨函数作用域访问到

if(true) {
    var c = 3;
}
console.log(c); // 3
for(var i = 0; i < 4; i ++) {
    var d = 5;
};
console.log(i); // 4   (循环结束i已经是4，所以此处i为4)
console.log(d); // 5
// if语句和for语句中用var定义的变量可以在外面访问到，
// 可见，if语句和for语句属于块作用域，不属于函数作用域。
```

### var、let、const的区别  

var定义的变量，没有块的概念，可以跨块访问, 不能跨函数访问。   
let定义的变量，只能在块作用域里访问，不能跨块访问，也不能跨函数访问。   
const用来定义常量，使用时必须初始化(即必须赋值)，只能在块作用域里访问，而且不能修改。   

```javascript
// 块作用域
{
var a = 1;  
let b = 2;
const c = 3;
// c = 4; // 报错
var aa;
let bb;
// const cc; // 报错
console.log(a); // 1
console.log(b); // 2
console.log(c); // 3
console.log(aa); // undefined
console.log(bb); // undefined
}
console.log(a); // 1
// console.log(b); // 报错
// console.log(c); // 报错
```


```javascript
// 函数作用域
(function A() {
    var d = 5;
    let e = 6;
    const f = 7;
    console.log(d); // 5
    console.log(e); // 6  (在同一个{ }中,也属于同一个块，可以正常访问到)
    console.log(f); // 7  (在同一个{ }中,也属于同一个块，可以正常访问到)

})();
// console.log(d); // 报错
// console.log(e); // 报错
// console.log(f); // 报错
```