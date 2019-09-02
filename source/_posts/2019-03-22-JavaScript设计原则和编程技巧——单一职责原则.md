---
title: JavaScript设计原则和编程技巧——单一职责原则
date: 2019-03-22 16:30:00
tags: JavaScript设计模式
categories: 
	- JavaScript设计模式





---

学习曾探的 《JavaScript设计模式与开发实践》并做记录。

[书籍的购买链接](http://www.ituring.com.cn/book/1632/)

设计模式的定义是：在面向对象软件设计过程中针对特定问题的简洁而优雅的解决方案。

通俗一点，设计模式是在某种场合下对某个问题的一种解决方案。是给面向对象软件开发中的一些好的设计取个名字。

说每种设计模式都是为了让代码迎合其中一个或多个原则而出现的，它们本身已经融入了设计模式之中，给面向对象编程指明了方向。

前辈总结的这些设计原则通常指的是单一职责原则、里氏替换原则、依赖倒置原则、接口隔离原则、合成复用原则和最少知识原则 

# JavaScript设计原则和编程技巧——单一职责原则

就一个类而言，应该仅有一个引起它变化的原因，在JavaScript 中，需要用到的类的场景并不太多，单一职责原则则是更多地运用在对象或者方法级别上面。

单一职责原则（SRP）的职责被定义为引起变化的原因。如果我们有两个动机去改写一个方法，那么这个方法就具有两个职责。每个职责都是变化的一个轴线。如果一个方法承担了过多的职责，那么杂需求的变迁中，需要改写这个方法的可能性就越大。

因此，SRP原则体现为：一个对象（方法）只做一件事情。

## 设计模式中的SRP原则

SRP原则在很多设计模式中都有着很广泛的应用，例如代理模式，迭代器模式、单例模式和装饰者模式。

**1.代理模式**

图片预加载，通过增加虚拟代理的方式，把预加载图片的职责放到代理对象中，而本体仅仅是负责往页面中添加 img 标签，这也是它最原始的职责。

```javascript
const myImage = function(){
    const imgNode = document.createElement('img');
    document.body.appendChild(imgNode);
    return{
        setSrc:function(src){
            imgNode.src = src;
        }
    }
}
```

proxyImage 负责预加载图片，并在预加载完成之后把请求交给本体 myImage:

```javascript
const proxyImage  = (function(){
    const img = new Image;
    img.onload = function(){
        myImage.setSrc(this.src);
    }
    return{
        setSrc:function(src){
            myImage.setSrc('xxx.jpg');
            img.src = src;
        }
    }
})();
```

把添加 img 标签的功能和预加载图片的职责分开放到两个对象中，这两个对象各自都只有一个被修改的动机。在它们各自发生改变的时候，也不会影响另外的对象

**2.迭代器模式**

我们有这样一段代码，先遍历一个集合，然后往页面中添加一些 div，这些 div 的 innerHTML分别对应集合里的元素：

```javascript
const appendDiv = function(data){
    for(let i=0,l=data.length;i<l;i++){
        const div = document.createElememt('div');
        div.innerHTML = data[i];
        document.body.appendChild(div);
    }
}
appendDiv( [ 1, 2, 3, 4, 5, 6 ] ); 
```

这其实是一段很常见的代码，经常用于 ajax 请求之后，在回调函数中遍历 ajax 请求返回的数据，然后在页面中渲染节点。

appendDiv 函数本来只是负责渲染数据，但是在这里它还承担了遍历聚合对象 data 的职责。我们想象一下，如果有一天 cgi 返回的 data 数据格式从 array 变成了 object，那我们遍历 data 的代码就会出现问题，必须改成 for ( var i in data )的方式，这时候必须去修改 appendDiv 里的代码，否则因为遍历方式的改变，导致不能顺利往页面中添加 div 节点.

我们有必要把遍历 data 的职责提取出来，这正是迭代器模式的意义，迭代器模式提供了一种方法来访问聚合对象，而不用暴露这个对象的内部表示

当把迭代聚合对象的职责单独封装在 each 函数中后，即使以后还要增加新的迭代方式，我们只需要修改 each 函数即可，appendDiv 函数不会受到牵连，代码如下：

```javascript
const each = function(obj,callback){
    const value,
          i = 0,
          length = obj.length,
          isArray = isArraylike(obj);
    if(isArray){
        for(;i<length;i++){
            callback.call(obj[i],i,obj[i]);
        }   
    }else{
        for( i in obj){
            value = callback.call(obj[i],i,obj[i]);
        }
    }
    return obj;
}

const appendDiv = function(data){
    each(data,function(i,n){
        const div = document.createElememt('div');
        div.innerHTML = n;
        document.body.appendChild(div);
    })
}
appendDiv( [ 1, 2, 3, 4, 5, 6 ] ); 
appendDiv({a:1,b:2,c:3,d:4} ); 
```

**3.单例模式**

惰性单例，最开始的代码是这样的

```javascript
const createLogin = (function(){
    let div;
    return function(){
        if(!div){
            div = document.createElement('div');
            div.innerHTML = '我是登录浮窗';
            div.style.display = 'none';
            document.body.appendChild(div);
        }
        return div;
    }
})();
```

现在我们把管理单例的职责和创建登录浮窗的职责分别封装在两个方法里，这两个方法可以独立变化而互不影响，当它们连接在一起的时候，就完成了创建唯一登录浮窗的功能，下面的代码显然是更好的做法：

```javascript
const getSingle = function(fn){
    let result;
    return functiion(){
        return result || (result = fn.apply(this,arguments));
    }
}
const createLogin = function(){
    div = document.createElement('div');
    div.innerHTML = '我是登录浮窗';
    div.style.display = 'none';
    document.body.appendChild(div);
}
const createSingleLogin = getSingle(createLogin);
const l1 = createSingleLogin();
const l2 = createSingleLogin();
l1 === l2 // true
```

**4.装饰者模式**

使用装饰者模式的时候，我们通常让类或者对象一开始只具有一些基础的职责，更多的职责在代码运行时被动态装饰到对象上面。装饰者模式可以为对象动态增加职责，从另一个角度来看，这也是分离职责的一种方式

我们把数据上报的功能单独放在一个函数里，然后把这个函数动态装饰到业务函数上面：

```javascript
Function.prototype.after = function( afterfn ){ 
const __self = this; 
    return function(){ 
        var ret = __self.apply( this, arguments ); 
        afterfn.apply( this, arguments ); 
    	return ret; 
	} 
}; 
const showLogin = function(){ 
 console.log( '打开登录浮层' ); 
}; 
const log = function(){ 
 console.log( '上报标签为: ' + this.getAttribute( 'tag' ) ); 
}
document.getElementById( 'button' ).onclick = showLogin.after( log ); 
```

## 何时应该分离职责

SRP  原则是所有原则中最简单也是最难正确运用的原则之一。

一方面，随着需求的变化，有两个原则总是同时变化，那么就不必分离它们，比如在 ajax 请求的时候，创建 xhr 对象和发送 xhr 对象几乎总是在一起的，那么创建 xhr 对象的职责和发送 xhr 请求的职责就没有必要分开。

另一方面，职责的变化轴线仅当它们确定会发生变化时才具有意义，即使两个职责已经被耦合在一起，但它们还没有发生改变的征兆，那么也许就没有必须要分离它们。在代码重构的时候再分离也不迟。

## 违反SRP原则

在人的常规思维中，总是习惯性地把一组相关的行为放到一起，如何正确地分离职责不是一件容易的事情。

我们也许从来没有考虑过如何分离职责，但这并不妨碍我们编写代码完成需求。对于 SRP原则，许多专家委婉地表示“This is sometimes hard to see.”。

一方面，我们受设计原则的指导，另一方面，我们未必要在任何时候都一成不变地遵守原则。在实际开发中，因为种种原因违反 SRP 的情况并不少见。比如 jQuery 的 attr 等方法，就是明显违反 SRP 原则的做法。jQuery 的 attr 是个非常庞大的方法，既负责赋值，又负责取值，这对于jQuery 的维护者来说，会带来一些困难，但对于 jQuery 的用户来说，却简化了用户的使用。

在方便性与稳定性之间要有一些取舍。具体是选择方便性还是稳定性，并没有标准答案，而是要取决于具体的应用环境。比如如果一个电视机内置了 DVD 机，当电视机坏了的时候，DVD机也没法正常使用，那么一个 DVD 发烧友通常不会选择这样的电视机。但如果我们的客厅本来就小得夸张，或者更在意 DVD 在使用上的方便，那让电视机和 DVD 机耦合在一起就是更好的选择

## SRP原则的优缺点

优点是降低单个类或者对象的复杂度，按照职责把对象分解成为更加小的粒度，这有助于代码的复用，也有利于单元测试，当一个职责需要变更的时候，不会影响到其他的职责。

缺点是会增加代码的复杂度，当我们按照职责将对象分解成更小的粒度之后，实际上也增大了这些对象之间互相联系的难度。