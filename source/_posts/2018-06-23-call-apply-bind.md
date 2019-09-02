---
title: call/apply/bind 的区别
date: 2018-06-23 18:00:45
tags: ES6
categories: 
	- ES6相关
---

为什么需要使用 `call`/`apply`/`bind`
--------------------------------------

举个栗子：

```javascript
box.onclick = function(){
  function fn(){
    alert(this);
  }
  fn();
}
```

上例中的 `this` 指向是 `window`，而不是 `box`

解决方法：

```javascript
box.onclick = function(){
  var self = this;
  function fn(){
    alert(self);
  }
  fn();
}
```

通过把 `this` 定义给变量 `self` 将其保存下来，有时候我们想让伪数组也能够调用数组的一些方法，这时候 `call`/`apply`/`bind` 就派上用场了

```javascript
box.onclick = function(){
  function fn(){
    console.log(this);
  }
  fn.call(this);
}
```

`call` 的作用就是改变 `this` 的指向，第一个传的就是一个对象，就是我们要借用的对象。 `fn.call(this)` 就是让 `this` 去调用 `fn()`，这里的 `this` 就是 `box` 

上述例子可以进行简写： 
简写1

```javascript
box.onclick = function(){
  var fn = function(){
    console.log(this); //box
  }.call(this);
}
```

简写2

```javascript
box.onclick = function(){
  (function(){
    console.log(this); //box
  }.call(this));
}
```

另一种形式

```javascript
var objName = {name:'lbh'};
var obj = {
  name:'hello',
  sayHello:function(){
    console.log(this.name);
  }.bind(objName)
};
obj.sayHello(); //lbh
```

三者的差别
-------------------

`call`/`apply`/`bind` 都是用来改变 `this` 指向的，但也有一些小小的差别

```javascript
function fn(a,b,c,d){
  console.log(a,b,c,d);
}

//call
fn.call(null,1,2,3);

//apply
fn.apply(null,[1,2,3]);

//bind
var f = fn.bind(null,1,2,3);
f(4);

结果如下：
1 2 3 undefined  
1 2 3 undefined  
1 2 3 4
```

第一个要传入的参数就是要借用的对象，但是这里我们不需要就用了 `null` 。 
`call` 就是挨个传值， `apply` 是传一个数组， `bind` 也是挨个传值，但是和 `call` 与 `apply` 不同，使用 `call` 和 `apply` 都会直接执行这个函数，而 `bind` 并不直接执行，而是将绑定好的 `this` 重新返回一个新函数，什么时候调用由自己决定 。也就是说，当你希望改变上下文环境之后并非马上执行的，而是回调执行，就使用 bind 方法，而apply 与 call 则会立即执行函数。

```javascript
var objName = {name:'lbh'};
var obj = {
  name:'hello',
  sayHello:function(){
    console.log(this.name);
  }.bind(objName);
}
obj.sayHello(); //lbh
```

上述例子之所以使用 `bind` 的原因也是因为使用 `call` 会报错，`sayHello` 在 `obj` 都已经执行完了，就根本不会有 `sayHello` 这个函数

## apply、call实例

### 数组之间追加

```javascript
var array1 = [12,'foo',{name:'joe'},-2542]
var array2 = ['Doe',123,100]
Array.prototype.push.apply(array1, array2);
// [12,'foo',{name:'joe'},-2542,'Doe',123,100]
```

### 获取数组中的最大值和最小值

正常情况下用 `Math.max` 如下：

```javascript
Math.max(10,6)
```

传一个数组可以用 `apply` 
```javascript
var arr = [1,2,40,32,5]; 
console.log(Math.max.apply(null,arr)); // 40
```

数组本身没有 max 方法，但是 Math 有，可以借助 call 或者 apply 使用其方法。

### 验证是否是数组（前提是 toString() 方法没有被重写过）

```javascript
functionisArray(obj){
    return Object.prototype.toString.call(obj) === '[object array]'
}
```

### 伪数组调用数组

```javascript
var domNodes = Array.prototype.slice.call(document.getElementsByTagName('*'));
function fn(){
  [].push.call(arguments,3);
  console.log(arguments); //[1,2,3]
}
fn(1,2);
```

javaScript 中存在一种名为伪数组的对象结构，比较特别的是 arguments 对象，还有像调用 getElementByTagName ，document.childNodes 之类的，它们返回的 NodeList 对象都属于伪数组。不能应用 Array 下的 push,pop等方法，但是我们可以通过 Array.prototype.slice.call 转换为真正的数组带有 length 属性的对象，这样 domNodes 就可以应用 Array 下的所有方法了。 

其他的：

```javascript
var arr = ['abds'];
console.log(''.indexOf.call(arr,'b')); //3
```

实际上浏览器的内部并不会在意你是谁，而是关心你传给我的是不是我能够运行的

### 面试题

定义一个 log 方法，让它可以处理 console.log 方法，常见的解决方法是：

```javascript
function log(msg){
    console.log(msg);
}
log(1); // 1
log(1,2) // 1
```

上面的方法可以解决基本的，但当传入的参数不确定的时候，上面的方法就没有用了。这个时候就要考虑使用 apply 或者是 call ，因为传入的参数不确定，最好是用数组的形式来传参，那么就可以使用 apply。

```javascript
function log(){
    console.log.apply(console,arguments)
}
log(1); // 1
log(1,2) // 1,2
```

接下来要给每一个 log 消息添加一个前缀“(app)”，比如：

```javascript
log('hello world'); //(app)hello world
```

这个时候可以想到 arguments 参数是一个伪数组，通过 Array.prototype.slice.call 转为标准数组，再使用数组的方法:

```javascript
function log(){
    // 传统方法  Array.prototype.slice.call
    var args = Array.prototype.slice.call(arguments);
    // ES6 的 Array.from
    var args =Array.from(arguments);
    // ES6 的展开式
    var args =[...arguments];
    args.unshift('(app)')
    console.log(console,args);
}
```

可以看看 slice 内部的原理：

```javascript
Array.prototype.slice = function(start,end){
    var result = new Array();
    start = start || 0; // 如果不传则设置默认值
    end = end || this.length; // 如果不传则设置默认值
    
    // this 指向调用的对象，当用了call ，能够改变 this 的指向，也就是传进来的对象，这是关键
    for(var i = start; i < end; i++){
        result.push(this[i]);
    }
    return result;
}
```

## bind

讲解bind 之前先看一道题目

```javascript
var altwrite = document.write;
altwrite('hello');
```

结果是：`Uncaught TypeError:Illegal invacation`

altwrite() 函数改变了 this 的指向 global 或 window 对象，导致执行提示非法调用异常，正确的方案就是使用 bind 方法

```javascript
altwrite.bind(document)('hello')
```

当然也可以使用 call 方法

```javascript
altwrite.call(document,'hello')
```

### **绑定函数**

bind 最简单的就是创建一个函数，使得整个函数不论怎么调用都有同样的 this 值，常见的错误就像上面的例子一样，将方法从对象中拿出来，然后调用，并希望 this 指向原来的对象。如果不做特殊处理，一般会丢失原来的对象，使用bind 方法能够漂亮的解决这个问题：

```javascript
this.num = 9;
var myModule = {
    num: 80,
    getNum: function(){
        console.log(this.num);
    }
}
var getNum = myModule.getNum;
getNum(); // 9 在这个例子中， this 指向全局变量

var getNum2 = getNum.bind(myModule);
getNum2(); // 80
```

bind 方法与 apply 和 call 相似，也是可以改变函数内的 this 的指向。

MDN 的解释是：bind 方法会创建一个新的函数，称为绑定函数，当调用这个绑定函数的时候，绑定函数会以创建它时传入 bind 方法的第一个参数为 this,传入 bind 方法的第二个以及以后的参数加上绑定函数运行时本身的参数按照顺序作为原函数的参数来调用原函数。

下面是一个具体使用的例子：

```javascript
var foo = {
    bar:1,
    eventBind: function(){
        var _this = this;
        $('someClass').on('click',functoin(event){
            console.log(_this.bar); //1
        });
    }
}
```

由于 JavaScript 特有的机制，上下文环境在 eventBind 这个函数过渡到 someClass 的点击函数发生了变化，上述使用变量保存 this 这些方式都是有用的。当然使用 bind 可以更加优雅地解决这个问题。

```javascript
var foo = {
    bar:1,
    eventBind: function(){        
        $('someClass').on('click',functoin(event){
            console.log(this.bar); //1
        }.bind(this));
    }
}
```

上面的代码中，bind 创建 了一个函数，当这个 click 事件绑定在被调用的时候，它的 this 关键字会被设置成传入的值（这里指的是调用 bind 传入的参数）。因此，我们这里想要传入的上下文 this (其实就是 foo)，但bind 函数中。然后当回调函数执行的时候，this 便指向 foo 对象。

另外一个例子：

```javascript
var bar = function(){
    console.log(this.x);
}
var foo = {
    x:3
}
bar(); // undefined
var func = bar.bind(foo);
func(); // 3
```

这里我们创建了一个新的函数，当使用 bind 创建一个绑定函数之后，它被执行的时候，它的 this 会被设置成 foo,而不是像我们调用 bar() 时的全局作用域。

### 偏函数（Partial Functions）与柯里化（Currying）

#### **偏函数**

定义：

> Partial application can be described as taking a function that accepts some number of arguments, binding values to one or more of those arguments, and returning a new function that only accepts the remaining, un-bound arguments.

大概的意思就是：

偏函数可以描述为接受一些参数的函数，它将值绑定到一个或者多个参数，返回一个只接受其余未绑定参数的新函数。

这是一个很好的特性，使用 bind 我们设定函数的预定义参数，然后调用的时候传入其他参数就可以了。

```javascript
function list(){
    return Array.prototype.slice.call(arguments);
}
var list1 = list(1,2,3); //[1,2,3]
var lending = list.bind(undefined,37); 
var list2 = lending(); //[37]
var list3 = lending(1,2,3); // [37,1,2,3]
```

与之相似的有一个叫做柯里化的

#### **柯里化**

柯里化是是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数且返回结果的新函数的技术

两者的区别：

柯里化是将一个多参数函数转换成多个单参数函数，也就是将一个 n 元函数转换成 n 个一元函数

偏函数是固定一个函数的一个或者多个参数，也就是将一个 n 元函数转换成 n - x 元函数。

#### **使用没有上下文的偏函数**

bind 可以实现偏函数，但是如果要固定一些参数，但是不绑定 this ?

内置的 bind 不允许这样，我们不能忽略上下文并跳转到参数，幸运的是，可以仅绑定 partial 函数容易实现：

```javascript
function partial(func,...argsBound){				
    return function(...args){
        return func.call(this,...argsBound,...args);
    }
}
let user = {
    firstName:'John',
    say(time,phrase){
        console.log(`[${time}] ${this.firstName}: ${phrase}!`);
    }
}
// 偏函数，绑定第一个参数，say 的 time 
user.sayNow = partial(user.say,new Date().getHours() + ':' + new Date().getMinutes());
//调用新函数提供的第二个参数 phrase
user.sayNow('Hello');
```

调用 partical(func,[arg1,arg2...])函数的结果为调用 func 的包装器（即第一个 return 的函数）：

1. this 一致（因为 user.sayNow 是通过 user 调用的）
2. 然后给其...argsBound ——partical 使用该参数（’时间‘）进行调用
3. 然后提供参数...args——提供给包装器的参数（’Hello‘）

使用 spread 运算符很容易实现

#### **柯里化的实现**

柯里化是另一种有趣的处理函数的技术：转换一个调用函数f(a,b,c)为 f(a)(b)(c)的方式调用。让我们实现柯里化函数，执行一个两元参数函数，即转换f(a,b)至f(a)(b)

```javascript
function curry(func){
    return function(a){
        return function(b){
            return func(a,b)
        }
    }
}

function sum(a,b){
    return a + b;
}
let carriedSum = curry(sum);
console.log(carriedSum(1)(2));
```

上面是通过一系列包装器实现的。

1. curry(func)的结果是 function(a) 的一个包装器
2. 当调用sum(1) ，参数被保存在词法环境中，然后返回新的包装器funtion(b)
3. 然后 sum(1)(2)提供 2 并最终调用 function(b)，然后传递调用给原始多参数函数 sum

#### **高级柯里化实现**

有一些柯里化的高级实现，可以实现更加复杂的功能：其返回一个包装器，它允许函数提供全部参数被正常调用，或返回偏函数，实现如下：

```javascript
function curry(func){
    return function curried(...args){
        if(args.length >= func.length){
            // 如果参数大于等于函数参数，那么运行函数提供全部参数被正常调用
            return func.apply(this,args);
        }else{
            // 提供参数小于函数参数，返回偏函数
            return function pass(...args2){
                return curried.apply(this,args.concat(args2));
            }
        }
    }
}
function sum(a,b,c){
    return a + b + c;
}
let curriedSum = curry(sum);
// 提供全部参数，正常调用
console.log(curriedSum(1,2,3));
// 返回偏函数包装器，并且提供2,3参数
console.log(curriedSum(1,2)(3));
```

当我们运行的时候，有两个分支：

1. 提供全部参数正常调用，如果传递 args 数与原来的函数定义的参数个数一样或者更长，直接调用
2. 获得偏函数，否则，不调用func 函数，返回另一个包装器，提供连接之前的参数一起作为新的参数重新调用 curried ，返回一个新偏函数（如果参数不够）或最终结果。

上面的例子中，调用 curriedSum(1)(2)(3)的过程：

1. 第一次调用 curried(1)，在词法环境中记住1，返回包装器 pass
2. 使用参数 2 调用包装器，其带着前面的参数1，连接它们然后调用 curried(1,2)，因为参数数量仍然小于3，返回包装器 pass
3. 再次调用包装器 pass,带着之前的参数（1，2）,加上3，并调用 curried(1,2,3)。最终有三个参数，传递给原函数，因为参数个数相等，直接调用 func 函数。

还有一种性能更加好的，不过判断条件反过来的写法:

```javascript
function curry(func){
    function curried(restProp,argsList){
        return restProp === 0 ? fn.apply(null,argsList):function(x){
            return curried(restProp - 1,argsList.concat(x));
        }
    }
    return curried(func.length,[]);
}

// ES6的写法
const curry = func =>{
    const curried = (restProp,argsList)=> restProp === 0 ?fn(...argsList):x=>curried(restProp - 1,argsList.concat(x));   
    return curried(func.length,[])
}
```



#### **总结**

- 当把已知的一些参数固定，结果参数被称为偏函数，通过使用bind 获得偏函数，也有其他方式实现。
  - 用途：当我们不想重复多次调用相同的参数的时候，偏函数是很便捷的，有 send(from,to)函数，如果 from 总是相同的，可以使用偏函数简化调用
- 柯里化是转换函数调用从 f(a,b,c)至f(a)(b)(c)，JavaScript 通常既可以实现正常调用，也可以实现参数不足时的偏函数方式的调用。
  - 用途：
    - 参数返回
    - 提前返回
    - 延迟计算或运行，参数随意设置

提前返回，常见的例子：兼容现代浏览器以及 IE 浏览器的事件添加方法

```javascript
var addEvent = function(el,type,fn,callback,capture){
    if(window.addEventListener){
        el.addEventListener(type,function(e){
            fn.call(el,e);
        },capture)
    }else if(window.attackEvent){
        el.attackEvent('on'+type,function(e){
            fn.call(el,e);
        })
    }
}
```

上面的方法有一个问题，就是每次用 addEvent 为元素添加事件的时候，(ie7/8)都会走一遍if..else if 其实只要判定一次就好了。这个时候就可以采用柯里化了。

```javascript
var addEvent = (function(){
    if(window.addEventListener){
        return function(el,type,fn,capture){
            el.addEventListener(type,function(e){
                fn.call(el,e);
            },capture)
        }
    }else if(window.attackEvent){
        return function(el,type,fn,capture){
            el.attackEvent('on'+type,function(e){
                fn.call(el,e);
            })
        }
    }
});
```

初始addEvent的执行其实只实现了部分的应用（只有一次的if...else if...判定），而剩余的参数应用都是其返回函数实现的，典型的柯里化思想。



### 和 setTimeout 一起使用

```javascript
function Bloomer(){
    this.petalCount = Math.ceil(Math.random() * 12) + 1;
}
// 1 秒后用 declare 函数
Bloomer.prototype.bloom = function(){
    window.setTimeout(this.declare.bind(this),1000);
}

Bloomer.prototype.declare = function(){
    console.log(`我有 ${this.petalCount}朵花瓣！`);
}
var bloo = new Bloomer();
bloo.bloom(); //我有5朵花瓣！
```

### 捷径

bind()也可以为需要特定this值的函数创造捷径。

例如：要将一个类数组转换成为数组，当然这个是在出现ES6之前的

```javascript
var unBoundSlice = Array.prototype.slice;
var slice = Function.prototype.call.bind(unBoundSlice);

slice(arguments);
```

### 实现

首先我们可以通过给目标函数指定作用域来简单实现 bind 方法

```javascript
Function.prototype.bind = function(context){
    self = this; // 保存 this，即调用 bind 方法的目标函数
    return function(){
        return self.apply(context,arguments);
    }
}
```

考虑到柯里化的情况，我们可以构建一个更加健壮的bind()

```javascript
Function.prototype.bind = function(context){
    var args = Array.prototype.slice.call(arguments,1);			
    self = this;
    return function(){
        var innerArgs = Array.prototype.slice.call(arguments);
        var finalArgs = args.concat(innerArgs);	
        return self.apply(context,finalArgs);
    }
}
```

这次的bind 方法可以绑定对象，也支持在绑定的时候传参。

JavaScript 的函数还可以作为构造函数，那么绑定后的函数用这种方式调用时，情况就比较微妙了，需要涉及到原型链的传递：

```javascript
Function.prototype.bind = function(context){
    var args = Array.prototype.slice(arguments,1),
    F = function(){},
    self = this,
    bound = function(){
        var innerArgs = Array.prototype.slice.call(arguments);
        var finalArgs = args.concat(innerArgs);
        return self.apply((this instanceof F ? this : context),finalArgs);
    }
    F.prototype = self.prototype;
    bound.prototype = new F();
    return bound;
}
```

这是《JavaScript Web Application》一书中对bind()的实现：通过设置一个中转构造函数F，使绑定后的函数与调用bind()的函数处于同一原型链上，用new操作符调用绑定后的函数，返回的对象也能正常使用instanceof，因此这是最严谨的bind()实现。

对于为了在浏览器中能支持bind()函数，只需要对上述函数稍微修改即可：

```javascript
Function.prototype.bind = function (oThis) {
    if (typeof this !== "function") {
        throw new TypeError("Function.prototype.bind - what is trying to be bound is not callable");
    }

    var aArgs = Array.prototype.slice.call(arguments, 1), 
    fToBind = this, 
    fNOP = function () {},
    fBound = function () {
        return fToBind.apply(
            this instanceof fNOP && oThis ? this : oThis || window,
            aArgs.concat(Array.prototype.slice.call(arguments))
            );
    };

    fNOP.prototype = this.prototype;
    fBound.prototype = new fNOP();

    return fBound;
};
```

有一个有趣的问题，如果连续 bind 两次或者是三次，那么输出的结果是什么？

```javascript
var bar = function(){
    console.log(this.x);
}

var foo = {
    x:3
}

var sed = {
    x:4
}

var func = bar.bind(foo).bind(sed);
foo(); // ?

var fix = {
    x:5
}

var func = bar.bind(foo).bind(sed).bind(fiv);
func(); // ？
```

答案是。两次仍然将输出3，原因是在 JavaScript 中，多次的 bind 是无效的。更深层的原因是，bind 的实现相当于使用函数在内部包了一个 call/apply，两次 bind 相当于再包住第一次的 bind,故第二次以后的 bind 是无法生效的。







## 参考链接：

[JS中的call、apply、bind方法详解](https://www.cnblogs.com/moqiutao/p/7371988.html)

[js基础进阶--关于Array.prototype.slice.call(arguments) 的思考](https://blog.csdn.net/wxl1555/article/details/80329091)

[JS中的柯里化 及 精巧的自动柯里化实现](https://segmentfault.com/a/1190000012364000)

[理解JS里的偏函数与柯里化](https://www.cnblogs.com/goloving/p/8542817.html)