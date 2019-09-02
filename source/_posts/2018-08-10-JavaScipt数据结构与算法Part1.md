---
title: 为什么我要放弃javaScript数据结构与算法（第一章）—— JavaScript简介
date: 2018-08-10 17:55:12
tags: javaScript数据结构与算法
categories: 
	- javaScript相关

---



数据结构与算法一直是我算比较薄弱的地方，希望通过阅读《javaScript数据结构与算法》可以有所改变，我相信接下来的记录不单单对于我自己有帮助，也可以帮助到一些这方面的小白，接下来让我们一起学习。



## 第一章 JavaScript简介

众所周知，JavaScript是一门非常强大的编程语言，不仅可以用于前端开发，也适用于后端开发，其中Node.js就是背后的技术。



### JavaScript数据结构与算法

那么学习JavaScript数据结构与算法有什么作用呢？首要的作用是数据结构和算法可以高效地解决常见的问题，这影响着代码的质量。其次，在计算机科学里面，算法是基础的概念，是面试的时候的重头戏。



### 环境搭建

浏览器是最简单的开发环境，下载个Firefox或者Chrome都可以。

下载好浏览器之后可以搭载一个Web服务器，方式有很多种,也都很简单，可以自行百度。下面介绍Node.js.

#### 使用Node.js 搭建服务器

首先到[Node.js官网](http://node.js/org/)下载和安装Node.js。然后打开终端应用，输入一下命令安装

```bash
npm i http-server -g
```

嫌安装过程慢的，可以安装一下淘宝镜像，就可以用 `cnpm`来安装,具体安装过程如下

```bash
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

安装完淘宝镜像后，就可以用 `cnpm`了

```bash
cnpm i http-server -g
```

安装好 `http-server`后，`cd`进入到目标路径后输入 `http-server`就可以在本地运行服务器。

![使用Node.js 搭建服务器](/images/2018-08-10-read-图解HTTP-Part2-为什么我要放弃javaScript数据结构与算法Part1-使用Nodejs搭建服务器.png)

同样的还有 `anywhere`，也可以达到同样的目的。

```bash
cnpm i anywhere -g
```

这样，整个环境就搭建好了。



### JavaScript基础

#### 变量

变量能保存的数据可以在需要时设置、更新和摄取。赋给变量的值都有对应的类型。

JavaScript的类型有数字、字符串、布尔值、函数和对象。还有 undefined 和 null，以及数组、日期和正则表达式。

注意：JavaScript不是强语言类型，跟后面我要学的TypeScript不一样，这意味着声明变量的时候不用指定变量的类型

调试输出变量值的时候有三种简单的方法

| 方法                | 描述                               |
| ------------------- | ---------------------------------- |
| alert("x")          | 将输出到浏览器的警示窗口           |
| console.log("x")    | 把文本输出到调试工具的Console标签  |
| document.write("x") | 直接输出到HTML页面里并被浏览器呈现 |

##### 变量作用域

作用域指在编写的算法函数时，我们能够访问的变量（在使用时，函数作用域也可以是一个函数）。有局部变量和全局变量两种。



#### 操作符

在JavaScript里有算数操作符、赋值操作符、比较操作符、逻辑操作符、位操作符、一元操作符和其他操作符。

##### 算数操作符

| 算数操作符 | 描述 |
| ---------- | ---- |
| +          | 加法 |
| -          | 减法 |
| *          | 乘法 |
| /          | 除法 |
| %          | 取余 |
| ++         | 递增 |
| - -        | 递减 |

##### 赋值操作符

| 赋值操作符 | 描述                         |
| ---------- | ---------------------------- |
| =          | 赋值                         |
| +=         | （x += y） == ( x = x + y )  |
| -=         | （x -= y） == ( x = x - y )  |
| *=         | （x *= y） == ( x = x \* y ) |
| /=         | （x /= y） == ( x = x / y )  |
| %=         | （x %= y） == ( x = x % y )  |

##### 比较操作符

| 比较操作符 | 描述     |
| ---------- | -------- |
| ==         | 相等     |
| ===        | 全等     |
| !=         | 不等     |
| >          | 大于     |
| \>=        | 大于等于 |
| <          | 小于     |
| <=         | 小于等于 |

##### 逻辑操作符

| 逻辑操作符 | 描述 |
| ---------- | ---- |
| &&         | 与   |
| 双竖线     | 或   |
| ！         | 非   |

##### 位操作符

| 位操作符 | 描述 |
| -------- | ---- |
| &        | 与   |
| 单竖线   | 或   |
| ~        | 非   |
| ^        | 异或 |
| <<       | 左移 |
| \>>      | 右移 |

`typeof`操作符可以返回变量或者表达式的类型。

```javascript
console.log(typeof num);  // number
console.log(typeof "num"); // string
console.log(typeof true); // boolean
console.log(typeof [1,2,3]); //object
console.log(typeof {num:"2"}); // object
```

#### 真值和假值

`true`和 `false`在 javascript 中的转换如下表

| 数值类型  | 转换成布尔值                                |
| --------- | ------------------------------------------- |
| undefined | false                                       |
| null      | false                                       |
| 布尔值    | true或者false                               |
| 数字      | +0/-0/NaN都是false其他是true                |
| 字符串    | 如果字符串是空（长度是0）为false,其他为true |
| 对象      | true                                        |

#### 相等操作符

| 类型（*x*）  | 类型（*y*）  | 结果                |
| ------------ | ------------ | ------------------- |
| null         | undefined    | true                |
| undefined    | null         | true                |
| 数字         | 字符串       | x == toNumber(y)    |
| 字符串       | 数字         | toNumber(x) == y    |
| 布尔值       | 任何类型     | toNumber(x) == y    |
| 任何类型     | 布尔值       | x == toNumber(y)    |
| 字符串或数字 | 对象         | x == toPrimitive(y) |
| 对象         | 字符串或数字 | toPrimitive(x) == y |

如果x和y是相同类型，javaScript会比较它们的值或者是对象值。其他没有在上述表格中的情况都会返回false。

`toNumber`和 `toPrimitive`方法都是内部的，并根据以下表格对其进行估值

`toNumber`方法对不同类型返回的结果如下表

| 值类型    | 结果                                                         |
| --------- | ------------------------------------------------------------ |
| undefined | NaN                                                          |
| null      | +0                                                           |
| 布尔值    | true=>1或者false=>+0                                         |
| 数字      | 数字对应的值                                                 |
| 字符串    | 将字符串解析成数字。如果字符串中包含字母，就返回NaN,如果是由数字字符组成的，就转换成数字 |
| 对象      | Number(toPrimitive(value))                                   |

toPrimitive方法对不同类型返回的结果如下：

| 值类型 | 结果                                                         |
| ------ | ------------------------------------------------------------ |
| 对象   | 如果对象的valueOf方法的结果是原始值，返回原始值。如果对象的toString方法返回原始值，就返回这个值，其他情况都返回一个操作。 |

```javascript
"x" ? true : false // true
"x"(NaN) == true(1) // false
"x"(NaN) == false(+0) // false
```

那么 `===`操作符呢？

相比较起来就简单很多，如果比较的两个值类型不同，比较的结果就是 `false`,如果类型相同，就会根据下面的表格进行判断。

| 类型（*x*） | 值                            | 结果 |
| ----------- | ----------------------------- | ---- |
| 数字        | *x*和*y*数值相同（但不是NaN） | true |
| 字符串      | *x*和*y*是相同的字符          | true |
| 布尔值      | *x*和*y*同是false或者true     | true |
| 对象        | *x*和*y*引用同个对象          | true |

```javascript
"x" === true // false
"x" === "x" // true
var person1 = { name: 'John' }
var person2 = { name: 'John' }
person1 === person2 // false 不同的对象
```



### 控制结构

条件语句支持 `if...else`和 `switch`。循环支持 `while`、`do...while`和 `for`。

##### 条件语句

实例：判断month

````javascript
1. if...else
if(month === 1){
    console.log('January');
}else if(month === 2){
    console.log('February');
}...
else{
    ...
}

2. switch
switch(month){
    case 1:
    console.log('January');
    break;
    case 2:
    console.log('February');
    break;
    ...
    default:
    consolo.log(...)
}

3. && 与 || 运算符
(month == 1 && console.log('January')) || (month == 2 && console.log('February')) || ...

4. 三元运算符
month == 1 ? console.log('January') : month == 2 ? console.log('February') : ...
````

##### 循环

处理数组元素的时候经常会用到循环。

实例：输出0到9

```javascript
1. for
for(var i = 0;i < 10;i++){
    console.log(i);
}

2.while
var i = 0;
while(i<10){
    console.log(i);
    i++
}

3.do ... while
var i = 0;
do{
    console.log(i);
    i++:
} while(i<10)
```



### 函数

函数在JavsScript中是很重要的。分为用传参数、return或者是直接调用。



### JavaScript面向对象编程

JavaScript里的对象就是普通名值对的集合。创建一个普通对象有两种方式。

```javascript
// 第一种方式:
var obj = new Object();

// 第二种方式
obj = {
    name:{
        first:"赖",
        last："xx"
    },
    address: "jj"
}
```

可以看出，声明JavaScript对象的时候，键值对中的键就是对象的属性，值就是对应属性的值。

在面向对象编程（OOP）中，对象是类的实例。一个类定义了对象的特征。我们会创建很多类来表示算法和数据结构。

```javascript
// 声明一个类来表示书
function Book(title, pages, isbn){    
	this.title = title;
	this.pages = pages;
	this.isbn = isbn;
}

// 实例化这个类
var book = new Book('title', 'pages', 'isbn' );
//可以修改或者是访问对象的属性
book.title // 书名
book.title = "new title"; //修改书名

// 类可以包含函数，可以声明和使用函数
Book.prototype.printTitle = function(){
    console.log(this.title);
}
book.printTitle();

// 也可以直接在类的定义里声明函数
function Book(title, pages, isbn){    
	this.title = title;
	this.pages = pages;
	this.isbn = isbn;
	this.printIsbn = function() {
        console.log(this.isbn);
	}
} 
book.printIsbn();
```

注意：在原型的例子里， `printTitle`函数只会创建一次，在所有实例中共享。如果在类的定义里声明，则每个实例都会创建自己的函数副本。使用原型方法可以节约内存和降低实例化的开销。不过原型方法只能声明公共函数和属性，而类定义可以声明只在类内部访问的私有函数和属性。



### 调试工具

Firefox 和 Chrome 都支持调试。这里有一个了解 [谷歌开发者工具的好教程](https://blog.csdn.net/csdnligao/article/details/53925094),可以自己了解一下。



### ECMAScript概述

ECMAScript是一种语言的规范，具体想知道的可以 [点击这里](https://github.com/LbhFront-end/About-ES6/blob/master/ES6-Start.md)



### ECMAScript6的功能

因为在别的地方有做了更详细的笔记，在这里就不累述，详情点击下面。

[点我](https://github.com/LbhFront-end/About-ES6)

#### 补充：使用类进行面向对象编程

```javascript
function Book(title, pages, isbn){
    this.title = title;
    this.pages = pages;
    this.isbn = isbn;
}
Book.prototype.printTitle = function() {
    console.log(this.title);
}
ES6:

class Book {
    constructor(title, pages, isbn){
        this.title = title;
    	this.pages = pages;
    	this.isbn = isbn;
    }
    printTitle(){
        console.log(this.title);
    }
}
```

继承

除了新的声明类的方式，类的继承也有简化的语法。

```javascript
clas ITBook extends Book {
    constructor(title, pages, isbn, technology){
        super(title, pages, isbn);
        this.technology = technology;
    }
    printTitle(){
        console.log(this.title);
    }
}
```

我们可以用 `extends`关键字扩展一个类并继承它的行为，在构造函数中，我们可以通过 `super`关键字引用父类的构造函数。





### 小结

本章主要讲了如何搭建简单的开发环境，也稍微地把javaScript基础知识、和ECMAScript6过了一遍，为接下来的数据结构——数组做铺垫。

书籍链接： [学习JavaScript数据结构与算法](https://book.douban.com/subject/26639401/)