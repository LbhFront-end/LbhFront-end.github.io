---
title: 深入JavaScript——从原型到原型链
date: 2019-01-09 11:30:00
tags: 深入JavaScript
categories: 
	- JavaScript
---

经一些热心网友推荐，看到了[冴羽](https://github.com/mqyqingfeng) 的深入系列，现做学习与记录，希望可以每天学习一篇，加强巩固自己对于 js 的理解。[原仓库地址](https://github.com/mqyqingfeng/Blog)。

# JavaScript深入之从原型到原型链

## 构造函数创建对象

```javascript
function Person(){}
var person = new Person();
person.name = 'K';
console.log(person.name); 'K'
```

在这个例子中，Person 就是一个构造函数，我们使用 new 创建了一个实例对象 person。

## prototype

每个函数都是一个 prototype属性，而且 prototype是函数才有的属性。例如：

```javascript
function Person(){}
Person.prototype.name = 'K';
var person1 = new Person();
var person2 = new Person()；
console.log(person1.name); // K
console.log(person2.name) // K
```

那这个函数的 prototype 属性的指向是什么？是这个函数的原型吗？

其实，函数的 prototype 属性指向了一个对象，这个对象正是调用该构造函数而创建的实例的原型，也就是这个例子中的 person1 和 person2 的原型。原型指的是每个 JavaScript 对象（null除外）在创建的时候会与之关联另一个对象，这个对象就是我们说的原型，每一个对象都会从原型“继承”属性。

```
			prototype
 Person  --------------- Person.prototype
(构造函数)					  (实例原型)
```

上面中，用 Object.prototype 表示实例原型

那么实例与实例原型，也就是 person 和 Person.prototype 之间有什么关系呢，这个时候就要说到第二个属性

## \__proto__

这个每一个 JavaScript对象(除了null)都具有的一个属性，叫做 \__proto__，这个属性会指向该对象的原型。

```javascript
function Person(){}
var person = new Person();
console.log(person.__proto__ === Person.prototype); //true
```

更新关系图：

![关系图](https://raw.githubusercontent.com/mqyqingfeng/Blog/master/Images/prototype2.png)

既然实例对象和构造函数都可以指向原型，那么原型是否有属性指向构造函数或者实例呢？

## constructor

指向实例是没有的，因为一个构造函数可以生成多个实例，但是原型指向构造函数倒是有的。每个原型都有一个 constructor 属性指向关联的构造函数。

```javascript
function Person(){}
console.log(Person === Person.prototype.constructor); // true
```

更新关系图：

![关系图](https://raw.githubusercontent.com/mqyqingfeng/Blog/master/Images/prototype3.png)

```javascript
function Person(){}
var person = new Person();
person.__proto__ === Person.prototype;
person.prototype.constructor === Person;
// 获取对象原型的方法
Object.getPrototypeOf(person) === Person.prototype;
```

## 实例与原型

当读取实例的属性，如果找不到，就会查找对象关联的原型中的属性，如果还查不到。就去找原型的原型，一直找到最顶层位置。

举个例子

```javascript
function Person() {}
Person.prototype.name = 'K';
var person = new Person();
person.name = 'D';
person.name; // 'D'
delete person.name;
person.name // 'K'
```

上面的例子中，我们给实例对象 person 添加了 name 属性，当我们打印 person.name 的时候，结果自然是 D，但是当我们删除了 person 的 name 属性后。读取 person.name ，从 person 对象中找不到 name 属性就会从 person 的原型，也就是 person.\__proto__,也就是 Person.prototype 中查找，找到了 K。

但是万一没有找到呢，那么原型的原型又是什么？

## 原型的原型

原型是一个对象，既然是对象，就可以用最原始的方式创建它，那就是：

```javascript
var obj = new Object();
obj.name = 'K';
obj.name // K
```

其实原型对象就是通过 Object 构造函数生成的，结合之前的，实例的 \__proto__ 指向函数的 prototype,更新一下关系图

![关系图](https://raw.githubusercontent.com/mqyqingfeng/Blog/master/Images/prototype4.png)

## 原型链

那 Object.prototype 的原型呢？

null,可以通过打印验证：

```javascript
Object.prototype.__proto__ === null // true
```

然后 null 究竟代表了什么？

null 代表没有对象，即该处不应该有值。

所以 Object.prototype.\__proto__ 的值为 null 跟 Object.prototype 没有原型其实表达了一个意思。

所以最后一张关系图就可以停止查找了。

![关系图](https://raw.githubusercontent.com/mqyqingfeng/Blog/master/Images/prototype5.png)

图中相互关联的原型组成的链状结构就是原型链，也就是蓝色这条线。

## 补充

### constructor

constructor 是部署在 Object.prototype 上的属性，位于原型链的最高一层（再高一层是null）。任何对象（包括函数，都有 constructor 属性），任何一个 prototype 对象都有一个 constructor 属性，指向它的构造函数。每一个实例也有一个 constructor 属性（原型包含 constructor 属性，因为可以通过对象的实例访问），默认调用 prototype 对象的 constructor 属性，即也指向它的构造函数。

```javascript
function Person(){}
var person = new Person();
person.constructor === Person; // true
```

当获取 person.constructor 时，其实 person 中并没有 constructor 属性，当不能读取到 constructor 属性时，会从 person 的原型，也就是 Person.prototype.constructor 获取，正好原型有该属性，所以

```javascript
person.constructor === Person.prototype.constructor
```



### \__proto__

绝大部分的浏览器都支持这个非标准的方法访问原型，然而它并不存在于 Person.prototype 中，实际上，它是来自 Object.prototype ，与其说是一个属性，不如说是一个 getter/setter 。当使用 obj.\__proto__ 时，可以理解为返回了 Object.getPrototypeOf(obj)。

该属性也没有正式写入 ES6 的正文中，而是写入了附录。原因是 `__proto__`前后的下划线，说明它本质上是一个内部属性，而不是一个正式对外的 API。只是由于浏览器广泛支持才加入了 ES6 	。标准明确规定，只有浏览器必须部署这个属性，其他运行环境不一定要部署，而且新的代码最好认为这个属性是不存在的。因为无从从语义上还是从兼容性上，都尽量不要使用这个属性。而是使用下面的 这些方法来作代替：

- `Object.setPrototypeOf()`：写操作
- `Object.getPrototypeOf()`：读操作
- `Object.create()`：生成操作

实际上，`__proto__` 调用的是 `Object.prototype.__proto__`，具体的实现如下.

```javascript
Object.defineProperty(Object.prototype,'__proto__',{
    get(){
        let _thisObj = Object(this);
        return Object.getPrototypeOf(_thisObj);
    },
    set(proto){
        if(this === undefined || this === null){
           	throw new TypeError();
        }
        if(!isObject(this)){
           return undefined;
        }
        if(!isObject(proto)){
           return undefined;
        }
        let status = Reflect.setPrototype(this,proto);
        if(!status){
           	throw new TypeError();
        }        
    }
});
function isObject(value){
   return Object(value) === value; 
}
```



### 真的是继承吗

前面提到，每一个对象都会从原型 “继承” 属性，实际上，继承是一个具有迷惑性的说法，继承以为是复制操作，r然而 JavaScript 默认并不会复制对象的属性，相反，JavaScript 只是在两个对象之间创建一个关联，这样，一个对象可以通过委托访问另一个对象的属性和函数。所以与其叫做继承，委托的说法反而更准确一点。

### 补充完整图

Object 是一个函数对象，也是 Function 构造的，Object.prototype 是一个普通对象。Function.prototype 是一个函数对象，之前讨论的函数对象都有一个显示的 prototype 属性，但是 Function.prototype 却没有 prototype 属性，就是 Function.prototype.prototype === undefined ，所有 Function.prototype 函数对象都是一个特例，没有 prototype 属性。

![原型链](/images/2019-01-09-深入JavaScript——从原型到原型链-原型链.png)

### 相关的函数

#### **instanceof 原理**

instanceof 是判断实例对象的 `__proto__` 和生成该实例的 `prototype` （也是实例对象和原型对象之间）是不是引用的同一个地址，是的会就返回 true,不是就返回 false。

#### **new 运算符**

new 运算符的原理

1. 一个新对象被创建，它继承自 foo.Prototype
2. 构造函数返回一个对象，在执行的时候，相应的参数会被传入，同时上下文（this）会被指定为这个新的实例
3. new foo 等同于 new Foo(),只能用在不传递任何参数的情况下
4. 如果构造函数返回了一个对象，那么这个对象会取代整个 new 出来的结果。如果构造函数没有返回对象，那这个 new 出来的结果则为步骤1创建的对象。

```javascript
var myNew = function(func){
    var o = Object.create(func.prototype); // 创建对象
    var k = func.call(o); // 改变 this 的指向，把结果给 k
    return (typeof k === 'object' ? k : o); // 判断 k 的类型是不是对象，如果是就返回 k，不是则返回原来的对象 o
}
```

通过简单的检验，可以知道上面这个 myNew 函数跟 new 运算符的作用是一样的。

#### **isPrototypeOf()**

这个方法，用来判断某个 `prototype`对象和实例之间的关系。

#### **hasOwnProperty()**

每个实例对象都一个 `hasOwnPerperty()`方法，用来判断某一个属性是本地的，还是继承自 `prototype`对象的属性。

#### **in 操作符**

**in** 操作符可以用来判断，某个实例是否有某个属性，不管是不是本地属性。也是为什么在遍历中，不建议使用它的原理，可能会遍历到预想不到的结果，如果真的要使用的话，建议结合 `hasOwnProperty()`函数来判断是否为本地的属性再作操作。

#### prototype 和 \__proto__

所有构造器/函数（也是对象）的 `__proto__`都指向 Function.prototype,它是一个空函数（Empty function）

```javascript
Number.__proto__ === Function.prototype;
Boolean.__proto__ === Function.prototype;
String.__proto__ === Function.prototype;
Object.__proto__ === Function.prototype;
Function.__proto__ === Function.prototype;
Array.__proto__ === Function.prototype;
RegExp.__proto__ === Function.prototype;
Rrror.__proto__ === Function.prototype;
Date.__proto__ === Function.prototype;

// 自定义的
function Person(){}
const Man = function{}
Person.__proto__ === Function.prototype;
Man.__proto__ === Function.prototype;
```

JavaScript 中有内置（build-in）构造器/对象共计12个（ES5加入了JSON），剩下的如 global 不能直接访问，Arguments 仅在函数调用时由 JS 引擎创建，Math,JSON 是以对象形式存在的，无需 new .它们的原型指向是 Object.prototype

```javascript
Math.__proto__ === Object.prototype;
JSON.__proto__ === Object.prototype;
```

函数也是对象，构造函数也是对象，可以理解为：构造函数是由 Function 构造函数 实例化出来的函数对象。

所有的构造器都来自于 Function.prototype,甚至包括跟构造器 Object 及 Function 本身，所有的构造器都继承了 Function.prototype 的属性及方法。如 length，call,apply,bind。

那么 Function.prototype 的原型指向什么呢?

```javascript
Function.prototype.__proto === Object.prototype;
```

这说明了所有的构造器也都是一个普通的函数，可以给构造器添加/删除属性等。同时它也继承了 Object.prototype 上的所有的方法：toString，valueOf,hasOwnProperty 等。

最后 

```javascript
Object.prototype.__proto__ === null
```

已经到顶了，实例是没有 prototype 属性的，prototype 只有函数才有的，跟原型链没有关系。

#### **protytype**

JavaScript 规定，每一个构造函数都有一个 prototype 属性，指向另一个对象，这个对象的所有属性和方法，都会被构造函数的实例继承。这意味着，我们可以将不变的属性和方法，直接定义到 prototype 对象上。这个属性包含一个对象，所有的实例对象需要共享的属性和方法，都放在这个对象里面，那么不需要共享的属性和方法，就放在构造函数里面。实例对象一旦被创建，将自动引用 prototype 对象的属性和方法。也就是说，实例对象的属性和方法，分成两种，一种是本地的，另一种是引用 protytype 对象的。

#### **apply,call,bind**

每个函数都包含两个非继承而来的方法：call() 和 apply()，这两个函数设置函数体内 this 对象的值。call 和 apply 是 Function 的方法，第一个参数是 this ,第二个参数是 Function 参数。比如说函数里写了 this，普通调用这个this 可能是 window ,而使用了call 或者是 apply 。第一个参数写的是什么，里面的this 就是什么（即改变 this 的指向）。

call 和 apply 都是为了改变某个函数的运行的 context 即上下文而存在的。换句话说，就是为了高边 函数体内的 this 指向。普通函数调用是隐式的传入 this,call 和 apply 可以显式地指定它。call 和 apply 真正强大的地方在于它们能够扩充函数赖以运行的作用域，而且好处在于对象不需要与方法有任何耦合关系。



## 参考链接：

[JavaScript 构造函数 prototype属性和_proto_和原型链 constructor属性 apply(),call()和bind() 关键字this new操作符](https://www.cnblogs.com/oneplace/p/5374347.html)



