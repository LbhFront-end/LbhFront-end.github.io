---
title: 前端面试题目汇总摘录（JS 基础篇2）
date: 2019-04-01  09:30:54
tags: 前端面试题
categories: 
	- 前端面试


---

温故而知新，保持空杯心态,复习到一半的时间，突然发现了 [前端面试之道](https://yuchengkai.cn/docs/frontend)，从第二道题目开始按学习这本书的路径来

## JS 基础2

### React/Vue 项目时为什么要在组件中写 key，其作用是什么？

> key是给每一个vnode的唯一id,可以`依靠key`,更`准确`, 更`快`的拿到oldVnode中对应的vnode节点。

key 的作用是为了在 diff 算法执行时更快的找到对应的节点，提高 diff 速度。

Vue 和 React 都是采用 diff 算法来对比新旧虚拟节点，从而更新节点。在 vue 中的 diff 函数，交叉对比中，当新节点跟旧节点 `头尾交叉对比`没有结果的时候，会根据新节点的 key 对比旧节点数组中的 key，从而找到对应旧节点。如果没有找到就认为是一个新增节点。而如果没有 key，那么就会采用遍历查找的方法找到对应的旧节点。。一种一个map 映射，另一种是遍历查找。相比之下，map 映射的速度更快。

vue 部分源码：

```javascript
// oldCh 是一个旧虚拟节点数组
if(isUndef(oldKeyToIdx)){
   oldKeyToIdx = createKeyToOldIdx(oldCh,oldStartIdx,oldEndIdx);
}
if(isDef(newStartVnode.key)){
   // map 方式获取
   idxInOld = oldKeyToIdx[newStartVnode.key]
}else{
// 遍历方式获取
  idxInOld = findIdxInOld(newStartVnode, oldCh, oldStartIdx, oldEndIdx)    
}

// 创建 map 函数
function createKeyToOldIdx(children,beginIdx,endIdx){
    let i,key;
    const map = {};
    for(i = beginIdx;i <= endIdx,++i){
        key = children[i].key;
		if(isDef(key) map[key] = i;
    }
    return map;
}

// 遍历寻找
// sameVnode 是对比新旧节点是否相同的函数
function findIdxInOld(node,oldCh,start,end){
    for(let i=start;i<end;i++){
        const c = oldCh[i];
        if(isDef(c) && sameVnode(node,c)) return i;
    }
}
```

### ["1","2","3"].map(parseInt)解析

```javascript
['10','10','10','10','10'].map(parseInt); // [10,NaN,2,3,4]
```



`parseInt(string,radix)`

参数：

`string`:要被解析的值，如果参数不是一个字符串，则将其转换成字符串。字符串开头的空白符会被忽略。

`radix`:一个介于2 和 36 的整数，表示上述字符串的基数。比如参数10 表示我们通常用的十进制数值系统。始终指定该参数可以消除阅读的困惑并且保证转换结果可预测。当未指定基数时，不同的实现会产生不同的结果，通常将值默认是10.

`返回值`:返回解析的整数值。如果被解析参数的第一个字符无法被转换为数值类型，则返回 `NaN`

注意：`radix`参数为n 会把第一个参数看做是一个数的 n 进制表示，而返回的值是十进制的。

- 如果字符串string 是以 ‘0x‘ 或者 '0X'开头，则基数是16进制
- 如果字符串 string 是以 ’0‘ 开头，基数是8进制或者10进制。ES5 规定用10进制。
- 如果字符串string 以其他任何值开头，则默认是十进制

```javascript
parseInt(100); // 100
parseInt(100,10); // 100
parseInt(100,2); // 4
```

`map`

map() 方法会创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数返回的结果。

```javascript
const new_array = arr.map(function callback(currentValue[,index[,array]]){
    // return element for new_array;
}[,thisArg]);
```

`callback`回调函数需要三个参数，我们通常只用了第一个参数（其他两个是可选的）

`currentValue`:是 callback 数组中正在处理的当前元素。

`index`:可选，是 callback 数组中正在处理的当前元素的索引

`array`:可选，是 callback map 方法被调用的数组

另外还有 `thisAry`:执行 callback 函数使用的 this 值

```javascript
['10','10','10','10','10'].map(parseInt);

// 相当于
['10','10','10','10','10'].map((item,index)=>{
    return parseInt(item,index);
});
// 即是
parseInt('10',0); // 10
parseInt('10',1); // NaN
parseInt('10',2); // 2
parseInt('10',3); // 3
parseInt('10',4); // 4

```

那么原题目也是同样的道理。

如果要将字符串数组循环变成数组可使用下面的方法

```javascript
['10','10','10','10','10'].map(Number);
// [10,10,10,10,10]
```

### 内置类型

JS 中分为7种内置类型，内置类型又分为两大类型：基本类型和对象（Object）[Function,Object,Array,Boolean,Number,String,Date,Error,RegExp,全局对象]

基本类型有：`null`,`undefined`,`string`,`number`,`boolean`,`symbol`

其中 JS 的数字类型是浮点类型，没有整型。`NaN`也是 `number`类型，并且 `NaN`等于自身

对于基本类型来说，如果使用字面量的方式，那么这个变量只是个字面量，只有在必要的时候才会被会转换为对应的类型：

```javascript
let a = 111; // 这只是字面量，不是 number 类型
a.toString(); // 使用的时候才会被转换成为对象类型
```

对象（Object ）是引用类型，在使用过程中会遇到浅拷贝和深拷贝的问题

```javascript
let a = {name:'haha'}
let b = a;
b.name = 'haha2'
a.name; // haha2
```

### Typeof

typeof 对于基本类型，除了 null 都可以显示正确的类型

```javascript
typeof 1; //'number'
typeof '1'; //'string'
typeof undefined; //'undefined'
typeof null; //'null'
typeof true; //'boolean'
typeof Symbol(); //'symbol'
typeof b // b 没有声明，但是还会显示 undefined
```

typeof 对于对象，除了函数都会显示 Object

```javascript
typeof []; 'object'
typeof {}; 'object'
typeof console.log; 'function'
```

对于 null 来说，虽然它是基本类型。但是会显示 object ，这是一个存在很久的 Bug。在JS 的最初版本，使用的是32位系统，为了性能问题使用低位储存了变量的内部信息，`000` 开头代表对象，然后 `null` 表示全为零，所以将它错误的判断为 object 。虽然现在内部类型判断代码已经更改了，但是这个 bug 却是一直流传下来的。

如果想要获得一个变量的正确类型，可以通过 `Object.prototype.call(xx)`，这样就可以获得类似 `[object type]`的字符串

```java
let a
// 我们也可以这样判断 undefined
a === undefined
// 但是 undefined 不是保留字，能够在低版本浏览器被赋值
let undefined = 1
// 这样判断就会出错
// 所以可以用下面的方式来判断，并且代码量更少
// 因为 void 后面随便跟上一个组成表达式
// 返回就是 undefined
a === void 0
```

### 类型转换

#### **转Boolean**

在条件判断时，除了 `undefined`,`null`,`false`,`NaN`,`''`,`0`,`-0` 其他所有值都转为 true,包括所有对象。

#### **对象转基本类型**

对象转基本类型时，首先会调用 `valueOf`然后调用 `toString`，并且这个两个方法是可以重写的

```javascript
let a = {
    valueOf(){
        return 0;
    }
}
```

也可以重写 `Symbol.toPrimitive`，该方法在转基本类型时调用优先级最高

```javascript
let a ={
    valueOf(){
        return 0;
    },
    toString(){
        return '1';
    },
    [Symbol.toPrimitive](){
        return 2;
    }
}
1 + a // 3
'1' + a // 12
```

#### **四则运算符**

加法运算规则：

1. 其中一方是字符串类型，另外一方亦然
2. 其中一方是数字类型，另外一方亦然
3. 只会触发三种类型转换：值 => 原始值, => 数字，=> 字符串

```javascript
1 + '1' // 11
2 * '2' // 4
[1,2] +[2,1] // '1,22,1'
// [1,2].toString -> '1,2'
// [2,1].toString -> '2,1'
'1,2'+ '2,1' = '1,22,1'

// 对于加号要注意表达式 'a'++'b'
'a'++ 'b' // 'aNaN'
// 因为 + 'b' 等于 NaN
```

#### **== 操作符**

比较运算 x==y,其中 x 和 y 是值，产生 true 或者 false ，这样的比较按下面的方式进行：

1. 若Type(x) 和 Type(y)相同，则
   1. 若 Type(x) 为 undefined,返回 true
   2. 若 Type(x) 为 Null,返回 true
   3. 若 Type(x) 为 Number,则
      1. 若 x 为 NaN,返回 false
      2. 若 y 为 NaN,返回 false
      3. 若 x 与 y 为相等数值，返回 true
      4. 若 x 为 +0, y 为 -0，返回 true
      5. 若 x 为 -0, y 为 +0，返回 true
      6. 返回 false
   4. 若 Type(x) 为 String,则 x 和 y 为完全相同的字符序列（长度相等且相同字符在相同位置）时返回 true,否则，返回 false
   5. 若 Type(x) 为 Boolean,当 x 和 y 同为 true 或者同为 false 时返回 true,否则，返回 false。
   6. 当 x 和 y 为引用同一对象时返回 true,否则返回 false.
2. 若 x 为 null 且 y 为 undefined ，返回 true
3. 若 x 为 undefined 且 y 为 null ,返回 true
4. 若 Type(x) 为 Number,且 Type(y) 为 String,返回比较 x == toNumber(y) 的结果
5. 若 Type(x) 为 String 且 Type(y) 为 Number,返回比较 ToNumber(x) == y 的结果
6. 若 Type(x) 为 Boolean，返回 比较 ToNumber(x) ==y 的结果
7. 若 Type(y) 为 Boolean,返回比较 ToNumber(y) ==x 的结果
8. 若 Type(x) 为 String 或者 Number，且 Type(y) 为 Object,返回比较 x==ToPrimitive(y) 的结果
9. 若 Type(y) 为 String 或者 Number，且 Type(x) 为 Object,返回比较 y==ToPrimitive(x) 的结果
10. 返回 false

toPrimitive 就是对象转基本类型

对照上面的规则，分析下面的案例

```javascript
[] == ![] // true
// 由于 ！优先级大于 ==，所以先运算右边，[] 为 true, ![] 取反为 false，得出
[] == false
// 根据第7条规则 ToNumber(y) ==x ，得出
[] == 0
// 根据第9条规则， y==ToPrimitive(x)，得出
ToPrimitive([]) == 0
// 即是
[].toString() == 0
// 得出
'' == 0;
// 根据第5条规则，ToNumber(x) == y 得出
0 == 0 // ->true
```

#### **比较运算符**

1. 如果是对象，就通过 toPrimitive 转换对象
2. 如果是字符串，就通过 unicode 字符索引来比较

### 原型

![原型](./images/前端面试题目汇总摘录-JS 基础篇2-原型.png)

每个函数都有 prototype 属性，除了 Function.prototype.bind() 该属性指向原型。

每个对象都有 `__proto__`属性，指向了创建该对象的构造函数的原型，其实这个属性指向了 `[[proptotype]]`，但是 `[[proptotype]]`是内部属性，我们并不能访问到，所以使用 `__proto__`来访问。对象可以通过 `__proto__`来寻找不属于该对象的属性，`__proto__`将对象连接起来形成了原型链。

### new

1. 新生成了一个对象
2. 链接到原型
3. 绑定 this
4. 返回新对象

在调用 new 的过程会发生上面四种事情，下面是自己实现的一个 new

```javascript
function create(){
    // 创建一个空的对象
    let obj = new Object();
    // 获得构造函数
    let Con = [].shift.call(arguments);
    // 链接到原型
    Obj.__proto__. = Con.prototype;
    // 绑定 this,执行构造函数
    let result = Con.apply(obj,arguments);
    // 确保 new 出来的是个对象
    return typeof result == 'Object' ? result : obj;
}
```

对于实例对象来说，都是通过 new 产生的，无论是 function Foo(){} 还是 let a = {b:1}

对于创建一个对象来说，更推荐使用字面量的方式来创建对象（无论是性能上还是可读性）。使用 new Object 方式创建对象需要通过作用域链一层层找到 Object，但是使用字面量就没有这个困扰

```javascript
function Foo(){}
// function 就是个语法糖，相当于 new Function()
let a = {b:1}
// 这个字面量也是使用了 new Object();
```

对于 new 来说，还需要注意下面的运算符优先级

```javascript
function Foo(){
    return this;
}
Foo.getName = function(){
    console.log('1');
}
Foo.prototype.getName = function(){
    console.log('2');
}

new Foo.getName(); // 1
new Foo().getName(); //2
```

可以看出 new Foo() 优先级大于 new Foo,所以代码可以这样划分执行顺序

```javascript
new (Foo.getName())
(new Foo).getName();
```

对于第一个函数来说，先执行了 Foo.getName 所以结果为1，对于后者来说，先 new Foo() 产生了一个实例，然后通过原型链找到了 Foo 上面的 getName 函数，所以结果为 2

### instanceof

instanceof 可以正确判断对象的类型，因为内部机制是通过判断对象的原型链是不是能找到类型的 prototype 

我们也可以试着实现:

```javascript
function instanceof(left,right){
    // 获得类型的原型
    let prototype = right.prototype;
    // 获得对象的原型
    left = left.__Proto__;
    // 判断对象的类型是否等于类型的原型
    while(true){
        if(left === null){
           return false
        }
        if(prototype === left){
           return true
        }
        left = left.__proto__
    }
}
```

### this

this 记住几个规则就可以了

```javascript
function foo(){
    console.log(this.a);
}
var a = 1;
foo();
var obj = {
    a:2,
    foo:foo
}
obj.foo();

// 上面两种情况 this 只依赖调用函数前的对象，优先级是第二个情况大于第一个情况
// 下面的优先级是最高的，this 只会绑定在c 上面，不会被任何方式修改 this 指向
const c = new foo();
c.a = 3;
console.log(c.a); // 3

//还有一种情况是利用 call,apply或者 bind 改变 this，这个优先级仅次于 new 
```

箭头函数中的 this 

```javascript
function a(){
    return ()=>{
        return ()=>{
            console.log(this);
        }
    }
}
console.log(a()()());
```

箭头函数其实是没有 this，这个函数中的 this 只取决于外面的第一个不是箭头函数的函数的this 。在上面的例子中，因为调用 a 符合前面代码的第一种情况，所以 this 是 window，并且一旦 this 绑定上下文了，就不会被任何代码改变。

### 执行上下文

当执行 JS 代码的时候，会产生三种执行上下文

- 全局执行上下文
- 函数执行上下文
- eval 执行上下文

每个执行上下文都有三个重要的属性

- 变量对象（VO），包含变量、函数声明和函数的形参，该属性只能在全局上下文中访问
- 作用域链（JS 采用词法作用域，也就是说变量的作用域是在定义时就决定的）
- this

```javascript
var a = 10;
function foo(i){
    var b = 20;
}
foo();
```

对于上述代码，执行栈中有两个上下文：全局上下文和函数 foo 上下文

```javascript
stack = [
    globalContext,
    fooContext
]
```

对于全局上下文来说，VO 大概是这样的

```javascript
globalContext.VO === global
globalContext.VO = {
    a:undefined,
    foo:<Function>,
}
```

对于 函数 foo 来说，VO 不能被访问，只能访问到活动对象（AO）

```javascript
fooContext.VO === foo.AO
fooContext.AO = {
    i:undefined,
    b:undefined,
    arguments:<>
}
// arguments 是函数底油的对象（箭头函数没有），这个对象是一个伪数组，有 length 属性可以通过下标访问元素，该对象的 callee 属性代表函数本身，caller 属性代表函数的调用者
```

对于作用域链，可以把它理解为包含自身变量对象和上级变量对象的列表，通过 [[Scope]] 属性查找上级变量

```javascript
fooContext.[[Scope]] = [ globalContext.VO ]
fooContext.Scope = fooContext.[[Scope]] + fooContext.VO
fooContext.Scope = [
    fooContext.Vo,
    globalContext.Vo
]
```

举个例子，var

```javascript
b(); // hehe
console.log(a); // undefined

var a = 'haha';
function b(){
    console.log('hehe');
}
```

上面的结果是因为函数和变量提升的原因。通常替身的解释是说将生命的代码移到了顶部，这其实没有什么错误，便于理解。但是更准确的解释应该是：在生成执行上下文时，会有两个阶段。第一个阶段是创建的阶段（具体步骤是创建 VO），JS 解释器会找出需要提升的变量和函数，并且给他们提前在内存中开辟好空间，函数的话会将整个函数放入内存中，变量只声明并且赋值为 undefined，所以在第二个阶段，也就是代码的执行阶段，我们可以直接提前使用。

在提升的过程中，相同的函数会覆盖上一个函数，并且函数优先于变量提升

```javascript
b(); //2
function b(){
    console.log('1');
}
function b(){
    console.log('2');
}
var b = 'haha';
```

var 会产生很多错误，所以现在 ES6 中引入了 let，let 不能在声明前使用，但是这并不是常说的 let 不会提升，let 提升了声明但是没有赋值，因为临时死区导致了并不能在声明前使用

对于非匿名立即执行函数需要注意下面的问题

```javascript
var foo = 1;
(function foo(){
    foo = 10;
    console.log(foo);
}());

/**ƒ foo() {
    foo = 10
    console.log(foo)
}
*/
```

因为当 JS 解释器在遇到非匿名的理解执行函数时，会创建一个辅助的特定对象，然后将函数名称作为这个对象的属性，因此函数内部才可以访问到 foo，但是这个值又是只读的，所以对它的赋值并不会生效，所以打印出来的还是这个函数，并且外部的值也没有任何改变。

```javascript
specialObject = {}
Scope = specialObject + Scope;
foo = new FunctionExpression;
foo.[[Scope]] = Scope;
specialObject.foo = foo; // {DontDelete}, {ReadOnly}

delete Scope[0]; // remove specialObject from the front of scope chain
```

### 闭包

闭包的定义很简单：函数 A 返回一个函数 B，并且函数 B 中使用了 函数 A 的变量，函数 B就被称为闭包

```javascript
function A(){
    let a = 1;
    function B(){
        console.log(a);
    }
    return B;
}
```

函数 A 中的变量这时候是存储在堆上的，JS 引擎可以通过逃逸分析辨别哪些变量需要存储在对上，哪些需要存储在栈上。

循环中使用闭包解决 var 定义函数的问题

```javascript
for(var i=1;i<=5;i++){
    setTimeout(function timer(){
        console.log(i)
    },i*1000);
}
```

首先，因为 setTimeout 是异步函数，所以回先把所有U型你换全部执行完毕，这时候 i 就是 6了，所以会输出一堆 6.

解决的方法有两种，第一种是使用闭包

```javascript
for(var i=1;i<=5;i++){
    (function(j){
        setTimeout(function timer(){
            console.log(j)
        },j*1000);
    })(j);
}
```

第二种是使用 setTimeout 的第三个参数

```javascript
for(var i=1;i<=5;i++){
    setTimeout(function timer(j){
        console.log(i);
    },i*1000,i);
}
```

第三种就是使用 let 定义 i

```javascript
for(let i=1;i<=5;i++){
    setTimeout(function timer(){
        console.log(i)
    },i*1000);
}
```

因为对于 let 来说，会创建一个块级作用域，相当于

```javascript
{
    // 形成块级作用域
    let i = 0;
    {
        let ii = i;
        setTimeout(function timer(){
            console.log(ii);
        },i*1000)
    }
    i++
    {
        let ii = i
    }
    i++
    {
        let ii = i
    }
    ...
}
```

### 深浅拷贝

```javascript
let a = {
    age:1
}
let b = a;
a.age = 2;
b.age; // 2
```

从上述例子可以看出，如果给一个变量赋值一个对象，那么两者的值会是同一引用，其中一方改变，另一方也会相应改变。

通常在开发中，我们不希望出现这样的问题，我们可以使用浅拷贝来解决这个问题。

#### **浅拷贝**

首先可以通过 `Object.assign` 来解决这个问题

```javascript
let a = {
    age:1
}

let b = Object.assign({},a);
a.age = 2;
console.log(b.age); // 1
```

也可通过展开运算符（...）来解决

```javascript
let a = {
    age:1
}
let b = {
    ...a
}
a.age = 2;
console.log(b.age); // 1
```

通常拷贝能解决大部分问题，但是当我们遇到下面的情况就需要使用深拷贝了

```javascript
let a = {
    age:1,
    jobs:{
        first:'FE'
    }
}
let b = {
    ...a
}
a.job.first = 'native';
console.log(b.jobs.first); // native
```

浅拷贝只解决了第一层问题，如果接下去的值中还有对象的话，那么两者又享有相同的引用，要解决这个问题，要引入深拷贝。

#### **深拷贝**

这个问题通常可以通过 `JSON.parse(JSON.stringify(object))`来解决

```javascript
let a = {
    age:1,
    jobs:{
        first:'FE'
    }
}

let b = JSON.parse(JSON.stringify(a));
a.jobs.first = 'native';
console.log(b.jobs.first); // 'FE'
```

但是该方法也是有局限性的：

- 会忽略 `undefined`
- 会忽略 `symbol`
- 不能序列化函数
- 不能解决循环引用的对象

```javascript
let obj = {
    a:1,
    b:{
        c:2,
        d:3
    }
}

obj.c = obj.b;
obj.e = obj.a;
obj.b.c = obj.c
obj.b.d = obj.b
obj.b.e = obj.b.c
let newObj = JSON.parse(JSON.stringify(obj));
console.log(newObj);
// Uncaught TypeError: Converting circular structure to JSON
//   at JSON.stringify (<anonymous>)
//   at <anonymous>:14:30
```

在遇到函数、undefined或者 symbol 的时候，该对象也不能正常的序列化

```javascript
let a = {
    age:undefined,
    sex:Symbol('male'),
    jobs:function(){},
    name:'haha'
}
let b = JSON.parse(JSON.stringify(a));
console.log(b); // {name:'haha'}
```

在上述代码中，该方法会忽略掉函数和 `undefined`

但是在通常情况下，复杂数据是可以序列化的，所以这个函数可以解决大部分问题，并且该函数是内置函数中处理深拷贝性能最快的。数据中含有以上三种情况下，可以使用 [lodash 的深拷贝函数](https://lodash.com/docs##cloneDeep)。

如果所需要拷贝的对象含有内置类型并且不包括函数的，可以使用 MessageChannel

```javascript
function structuralClone(obj){
    return new Promise(resolve =>{
        const {port1,port2} = new MessageChannel;
        port2.onmessage = ev => resolve(ev.data);
        port1.postMessage(obj)
    })
}

var obj = {
    a:1,
    b:{
       c:b
    }
}
// 该方法是异步的
// 可以循环处理 undefined 和循环引用对象
(async ()=>{
    const clone = await structualClone(obj)
})()
```

我们也可以自己创建一个 deepClone 函数

```javascript
// 数字 字符串 function 不需要拷贝
function deepCline(value){
    if(value == null) return value;
    if(typeof value !== 'object') return value;
    if(value instanceof RegExp) return new RegExp(value);
    if(value instanceof Date) return new Date(value)
    // 判断是数组还是对象
    let obj = new value.constructor;
    for(let ket in value){
        obj[key] = deepClone(value[key])
    }
    return obj;
}
```



### 模块化

在有 Babel 的情况下， 可以直接使用 ES6 的模块化

```javascript
// file a.js

export function a(){}
export function b(){}

// file b.js
export default function(){}

import {a,b} from './a.js'
import XXX from './b.js'
```

#### **CommonJS**

CommonJS 是 Node 独有的规范，浏览器中使用就需要用到 Broserify 解析

```javascript
// a.js
module.exports = {
    a:1
}
export.a = 1;

// b.js

var module = require('./a.js');
module.a // -> log 1
```

在上述代码中，module.export 和 export 很容易混淆，看看大致的内部实现

```javascript
var module = require('./a.js');
module.a
// 这里其实就是包装了一层立即执行函数，这样就不会污染全局变量了，重要的是 module 这里，module 是 Node 独有的一个变量
module.exports = {
    a:1
}
// 基本实现
var module = {
    exports:{} // exports 就是空对象
}

// 这也是为什么 exports 和 module.exports 用法相似的原因
var exports = module.exports;
var load = function(module){
    var a = 1;
    module.exports = a;
    return module.exports
}
```

module.exports 和 exports 用法其实是相似的，但是不能对 exports 直接赋值，不会有任何效果。

对于 CommonJS 和 ES6 的模块化的两者区别是：

- 前者支持动态导入，也就是 require(${path}/xx.js),后者不支持，但是已有提案
- 前者是同步导入，因为用于服务端，文件都在本地，同步导入即使卡住线程影响也不大。而后者是异步导入，因为用于浏览器，需要下载文件，如果也采用同步导入会对渲染有很大影响
- 前者在导出时都是值拷贝，就算导出的值变了，导入的值也不会改变，所以如果想要更新值，必须重新导入一次。但是后者采用实时绑定的方式，导入导出的值都指向同一个内存地址，所以导入值会跟随导出值变化
- 后者会编译成 require/exports 来执行

#### **AMD**

AMD 是由 RequireJS 提出的

```javascript
define(['./a.js','./b.js'],function(a,b){
    a.do();
    b.do();
});

define(function(require,exports,module){
    var a = require('./a');
    a.doSomething();
    var b = require('./b');
    b.doSomething();
});
```

### 节流和防抖的理解

防抖和节流都是防止函数多次调用。区别在于，假设一个用户一直触发这个函数，且每次触发函数的间隔小于 wait，防抖的情况只会调用一次，而节流的情况会隔一定时间（参数wait）调用函数

#### **防抖**

在滚动事件中需要做一个复杂计算或者是实现一个按钮防止第二次点击操作。这些需求都可以通过函数防抖来实现，尤其是第一个需求，如果在频繁的时间回调中做复杂计算，很有可能会导致页面卡顿，不如将多次计算合并为一次计算，只在一个精确点做操作。

通俗化：如果用手指一直按着弹簧，它将不会弹起知道你松手为止

袖珍版的防抖：

```javascript
// func 是用户传入需要防抖的函数
// wait 是等待时间
const debounce = (func,wait = 50) =>{
    // 缓存一个定时器id
    let timer = 0;
    // 返回的函数是每次用户实际调用防抖函数，如果已经设定过定时器就清空上一次的定时器，开始一个新的定时器，延迟用户传入的方法
    return function(..args){
        if (timer) clearTimeout(timer);
        timer = setTimeout(()=>{
            func.apply(this,args);
        },wait);
    }
}
// 不难看出来如果用户调用该函数间隔小于 wait 的情况下，上一次时间还未到就被清除了，并不会执行函数。
```

这是一个简单的防抖，但是有缺陷，在于它只能最后调用。一般的防抖会有 immediate 选项，表示是否立即调用。这两者的区别：

- 例如在搜索引擎搜索问题的时候，我们当然希望用户输入完最后一个字才调用查询接口，这个时候用 `延迟执行` 的防抖函数，它总是在一连串（间隔小于 wait）函数触发之后调用
- 例如用户给项目点 star 的时候，我们希望用户点第一下的时候就去调用接口，并成功之后改变 star 按钮的样子，用户就可以立马得到反馈是否 star 成功了，这个情况使用 `立即调用`的防抖函数，它总在第一次调用，并且下一次调用必须和前一次调用的时间间隔大于 wait 才会触发。

带有立即执行的防抖函数

```javascript
// 这个用来获取当前时间戳
function now(){
    return +new Date();
}
/**
* 防抖函数，返回函数连续调用，空闲时间必须大于或者等于 wait,func 才会执行
* @param {function} func 回调函数
* @param {number} wait 表示窗口的间隔
* @param {boolean} immediate 设置为 true时，是否立即调用函数
* @return {function} 
*/
function debounce(func,wait=50,immediate=true){
    let timer,context,args;
    // 延迟执行函数
    const later = () => setTimeout(()=>{
        // 延迟执行函数执行完毕，清除缓存的定时器序号
        timer = null;
        // 延迟执行的情况下，函数会在延时函数中执行，使用到之前缓存的参数和上下文
        if(!immediate){
           func.apply(context,args);
            context = args = null
        }
    },wait)
    
    // 这里返回的函数是每次实际调用的函数
    return function(...params){
        // 如果没有创建样式执行函数（later）,那就创建一个
        if(!timer){
           timer = later();
           // 如果是立即执行，调用函数，否则缓存参数和调用上下文
            if(immediate){
               func.apply(this,params);
            }else{
                context = this;
                args = params
            }
        // 如果已有延迟执行函数（later） ，调用的时候清除原来的并重新设定一个
        // 这样做延迟函数会重新计时
        }else{
            clearTimeout(timer);
            timer = later();
        }
    }
};

```

总结一下：

- 对于按钮点击来说的实现:如果函数是立即执行的，就理解调用，如果函数是延迟执行的，就缓存上下文和参数，放到延迟函数中执行，一旦开始一个定时器，只要定时器还在，每次点击都会重新计时。一旦定时器时间到了，定时器重置为null,就可以再次点击了。
- 对于延迟函数来说的实现：清除定时器ID，如果是延迟调用就调用函数

#### **节流**

防抖和节流本质上是不一样的。防抖是将多次执行变成最后一次执行，节流是将多次执行变成每隔一段时间执行。

通俗化：如果将水龙头拧紧直到水是以水滴的形式流出，那么你会发现隔一段时间，就会有一滴水溜出来。也就是会预先设定一个执行周期，当调用动作的时刻大于等于执行周期则执行该动作，然后进入下一个周期

袖珍版实现：

```javascript
const throttle = (wait,func)=>{
    let last = 0;
    return function(...args){
        const curr = +new Date();
        if(curr - last > wait){
           func.apply(this,args);
            last = curr;
        }
    }
}
```

```javascript
/**
* underscore 节流函数，返回函数连续调用，func 执行频率是 次/wait
* @param {function} func 回调函数
* @param {number} wait 表示窗口的间隔
* @param {object} options 如果想忽略开始函数的调用，传入{leading:false},如果想忽略结尾函数则是{trailing:false},两者不能共存，否则函数不执行。
* @return {function} 返回客户调用的函数
*/
_.throttle = function(func,wait,options){
    let context,args,result;
    let timeout = null;
    // 之间的时间戳
    let previous = 0;
    // 如果 options 没传则设置为空对象
    if(!options) options = {};
    // 定时器回调函数
    const later = function(){
        // 如果设置了 leading,就将 previous 设为0，用于下一个函数的第一个 if 判断
        previous = options.length === false ? 0 : _.now();
        // 置空是为了防止内存泄露，也是为了下面定时器的判断
        timeout = null;
        result = func.apply(context,args);
        if(!timeout) context = args = null;
    }
    return function(){
        // 获得当前的时间戳
        const now = _.now;
        // 首先进入前者肯定要为 true,如果需要第一次不执行函数，就将上次时间戳设定为当前的，就下来的计算中 remaining 的值时会大于0
        if(previous && options.leading === false) previous = now;
        // 计算剩下的时间
        const remaining = wait - (now - previous);
        context = this;
        args = arguments;
        // 如果当前调用给意见大于上次时间 + wait，或者用户手动调了事件，如果设置 trailing，只会进入这个条件
        if(remaining <= 0 || remaining > wait){
            // 如果存在定时器就清理掉否则会调用二次回调
            if(timeout){
               clearTimeout(timeout);
                timeout = null;
             }
            previous = now;
            result = func.apply(context,args);
            if(!timeout) context = args = null;
        }else if(!timeout &&options.trailing !== false){
            // 判断是否设置了定时器和 trailing ，没有的话就开启一个定时器，并且不能同时设置 leading 和 trailing
            timeout = setTimeout(later,remaining);
        }
        return result
    }
}
```

### 继承

在 ES5 中，可以使用下面的方式解决继承的问题

```javascript
function Super(){}
Super.prototype.getNumber = function(){
    return 1
}
function Sub(){}
let s = new Sub();
Sub.prototype = Object.create(Super.prototype,{
    constructor:{
        value:Sub,
        enumerable:false,
        writable:true,
        configurable:true
    }
});
```

上面的继承实现思路就是将子类的原型设置为父类的原型

在 ES6 中，可以通过 class 语法糖解决这个问题

```javascript
class MyDate extends Date{
    test(){
        return this.getTime();
    }
}
let myDate = new MyDate();
myDate.test(); 
```

### call,apply,bind 区别

call 和 apply 都是为了解决改变 this  的指向，作用都是相同的，只是传参的方式不同，除了第一个参数外，call 可以接受一个参数列表，apply 只接受一个参数数组

```javascript
let a ={
    value:1
}
function getValue(name,age){
    console.log(name);
    console.log(age);
    console.log(this.value);
}
getValue.call(a,'haha','24');
getValue.apply(a,['haha','24']);
```

#### **模拟实现 call 和 apply**

可以从下面几点来考虑如何实现

- 不传入第一个参数，那么默认可以为 window
- 改变了 this 指向，让新的对象可以执行该函数。

```javascript
Function.prototype.myCall = function(context){
    var context = context || window;
    // 给 context 添加一个属性
    // getValue.call(a,'haha',24) -> a.fn('haha','24');
    context.fn = this;
    // 将 context 后面的参数取出来
    var args = [...arguments].slice(1);
    var result = context.fn(...args);
    
    // 删除 fn
    delete context.fn
    return result
}
```

apply 的实现也是类似的

```javascript
Function.prototype.myApply = function(context){
    var context = context || window;
    context.fn = this;
    var result;
    // 判断是否存在第二个参数，如果存在就把第二个参数展开
    if(arguments[1]){
    	result = context.fn(...arguments[1]);   
    }else{
        result = context.fn();
    }
}
```

bind 和其他两个方法作用也是一样的，只是该方法会返回一个函数。并且我们可以通过 bind 实现柯里化

模拟实现 bind

```javascript
Function.prototype.myBind = function(context){
    if(typeof this !== 'undefined'){
       throw new TypeError('Error');
    }
    var _this = this;
    var args = [...arguments].slice(1);
    // 返回一个函数
    return function F(){
        if(this instanceof F){
           return new _this(..args,...arguments);
        }
        return _this.apply(context,args.concat(...arguments));
    }
}
```

### Promise 实现

Promise 是 ES6 新增的语法，解决了回调地狱的问题。

可以把 Promise 看成一个状态机。初始状态是 pending 状态，可以通过函数 resolve 和 reject ，将状态转变为 resolved 和 rejected 状态，状态一旦改变就不能再发生变化了。

then 函数会返回一个 Promise 实例，并且该返回值是一个新的实例而不是之前的实例。因为 Promise 规范规定了 pending 状态，其他状态是不可以改变的，如果返回的是同一个实例的话，多个 then 调用就失去意义了。

对于 then 来说，本质上可以把它看成 flatMap

```javascript
  const PENDING = 'pending';
  const RESOLVED = 'resolved';
  const REJECTED = 'rejected';

  // promise 接受一个函数参数，该函数会理解执行
  function MyPromise(fn) {
    let _this = this;
    _this.currentState = PENDING;
    _this.value = undefined;
    // 用于保存 then 中的回调，只有当 promise 状态为 pending 时才会缓存，并且每个实例至多缓存一个
    _this.resolvedCallbacks = [];
    _this.rejectedCallbacks = [];
    _this.resolve = function (value) {
      if (value instanceof MyPromise) {
        // 如果 value 是个 Promise ，递归执行
        return value.then(_this.resolve, _this.reject);
      }
      setTimeout(() => { // 异步执行，保证执行顺序
        if (_this.currentState === PENDING) {
          _this.currentState = RESOLVED;
          _this.value = value;
          _this.resolvedCallbacks.forEach(cb => cb());
        }
      });

    }
    _this.reject = function (reason) {
      setTimeout(() => {
        if (_this.currentState === PENDING) {
          _this.currentState = REJECTED;
          _this.value = reason;
          _this.rejectedCallbacks.forEach(cb => cb());
        }
      });
    }
    // 用于解决下面的问题
    // new Promise(()=> throw Error('error'))
    try {
      fn(_this.resolve, _this.reject)
    } catch (e) {
      _this.reject(e)
    }
  }
// then 函数的作用是为 Promise 实例添加状态改变时的回调函数。then 方法的第一个参数是 resolved 状态的回调函数，第二个参数（可选）是 rejected 状态的回调函数。
// then 方法返回的是一个新的 promise 实例，因此可以采用链式写法，即 then 方法后面再调用另一个 then 方法。
  MyPromise.prototype.then = function (onResolved, onRejected) {
    const self = this;
    // then 必须返回一个新的 promise
    let promise2;
    // 如果 onResolved 和 onRejected 都为可选参数，如果类型不是函数需要忽略，同时也实现了透传
    // Promise.resolve(4).then().then((value)=>console.log(value))
    onResolved = typeof onResolved === 'function' ? onResolved : v => v;
    onRejected = typeof onRejected === 'function' ? onRejected : r => { throw r };

    if (self.currentState === RESOLVED) {
      return (promise2 = new MyPromise(function (resolve, reject) {
        setTimeout(() => {
          try {
            let x = onResolve(self.value);
            resolutionProcedure(promise2, x, resolve, reject)
          } catch (reason) {
            reject(reason)
          }
        });
      }))
    }
    if (self.currentState === REJECTED) {
      return (promise2 = new MyPromise(function (resolve, reject) {
        setTimeout(() => {
          try {
            let x = onRejected(self.value);
            resolutionProcedure(promise2, x, resolve, reject)
          } catch (reason) {
            reject(reason)
          }
        });
      }))
    }
    if (self.currentState === PENDING) {
      return (promise2 = new MyPromise(function (resolve, reject) {
        self.rejectedCallbacks.push(function () {
          try {
            let x = onRejected(self.value);
            resolutionProcedure(promise2, x, resolve, reject)
          } catch (r) {
            reject(r)
          }
        });

        self.resolvedCallbacks.push(function () {
          try {
            let x = onResolved(self.value);
            resolutionProcedure(promise2, x, resolve, reject)
          } catch (r) {
            reject(r)
          }
        });
      }))
    }
  }

  function resolutionProcedure(promise2, x, resolve, reject) {
    // x 不能与 promise2 相同，避免循环
    if (promise2 === x) {
      return reject(new TypeError('Error'));
    }
    // 如果 x 为 Promise ，状态为 pending 需要继续等待否则执行
    if (x instanceof MyPromise) {
      if (x.currentState === PENDING) {
        x.then(function (value) {
          // 再次调用该函数是为了确认 x resolve 的 参数是什么类型，如果是基本类型就再次 resolve，把值传给下一个 then
          resolutionProcedure(promise2, x, resolve, reject);
        }, reject);
      } else {
        x.then(resolve, reject);
      }
      return;
    }
    // reject 或者 resolve 其中一个执行得过的话，忽略其他的
    let called = false;
    // 判断 x 是否为对象或者是函数
    if (x !== null && (typeof x === 'object' || typeof x === 'function')) {
      // 如果不能取出 then ，就 reject
      try {
        let then = x.then;
        if (typeof then === 'function') {
          then.call(
            x,
            y => {
              if (called) return;
              called = true;
              resolutionProcedure(promise2, y, resolve, reject);
            },
            e => {
              if (called) return;
              called = true;
              reject(e);
            });
        } else {
          resolve(x)
        }
      } catch (e) {
        if (called) return;
        called = true;
        reject(e)
      }
    } else {
      resolve(x);
    }
  }
```

### Generator 实现

Generator 是 ES6 中新增的语法，和 Promise 一样，都可以用异步来编程。

Generator 函数也可以理解成为一个状态机，封装了多个内部状态。

执行 Generator 函数会返回一个遍历器对象，也就是说，Generator 函数除了状态机，还是一个遍历器对象生成函数。返回的遍历器对象，可以依次遍历 Generator 函数内部的每一个状态。

形式上，Generator 函数是一个普通函数，但是有两个特征。一个是 function 关键字与函数名之间有一个 星号，二是函数内部使用 yield 表达式，表示不同的内部状态。



```javascript
// 使用 * 表示这是一个 Generator 函数
// 内部可以使用 yield 暂停代码
// 调用 next 恢复执行
function* test(){
    let a = 1 + 2;
    yield 2;
    yield 3;    
}
let b = test();
b.next(); // {value:2,done:false}
b.next(); // {value:3,done:false}
b.next(); // {value:undefined,done:true}
```

上述代码可以发现，加上 * 的函数执行后拥有了 next 函数，也就是说函数执行后返回了一个对象。每次调用 next 函数可以继续执行被暂停的代码，下面是 Generator 的简单实现：

````javascript
  // cb 也就是编译的 test 函数
  function MyGerenator(cb) {
    return (function () {
      const object = {
        next: 0,
        stop: function () { }
      };
      return {
        next: function () {
          const ret = cb(obj);
          if (ret === undefined) return { value: undefined, done: true }
          return {
            value: ret,
            done: false
          }
        }
      }
    })();
  }
  // 使用 babel 编译后可以发现 test 函数变成了这样
  function test() {
    let a;
    return MyGerenator(function () {
      while (1) {
        switch ((_context.prev = _context.next)) {
          // 可以发现通过 yield 将代码分成了几块，每次执行 next 函数就执行一块代码，并且表明下次需要执行哪块代码
          case 0:
            a = 1 + 2;
            _context.next = 4;
            return 2;
          case 4:
            _context.next = 6;
            return 3;
          case 6:
          case 'end':
            return _context.stop();
        }
      }
    })
  }
````

#### 一道题目

```javascript
function *foo(x){
    let y = 2 * (yield(x+1))
    let z = yield(y/3)
    return (x+y+z)
}
console.log(it.next())   // => {value: 6, done: false}
console.log(it.next(12)) // => {value: 8, done: false}
console.log(it.next(13)) // => {value: 42, done: true}
```

分析：

- 首先 `Generator` 函数调用和普通函数不同，它会返回一个迭代器
- 当执行第一次 `next` 时，传参会被忽略，并且函数暂停在 `yield (x + 1)` 处，所以返回 `5 + 1 = 6`
- 当执行第二次 `next` 时，传入的参数等于上一个 `yield` 的返回值，如果你不传参，`yield` 永远返回 `undefined`。此时 `let y = 2 * 12`，所以第二个 `yield` 等于 `2 * 12 / 3 = 8`
- 当执行第三次 `next` 时，传入的参数会传递给 `z`，所以 `z = 13, x = 5, y = 24`，相加等于 `42`

### Map、FlatMap 和 Reduce

Map 的作用是生成一个数组，遍历原数组，将每个元素拿出来然后做一些变换然后 append 到新的数组中

```javascript
[1,2,3].map(v=>v+1);
// [2,3,4]
```

Map 有三个参数，分别是当前索引元素，索引，原数组

```javascript
['1','2','3'].map(parseInt);
// parseInt('1',0) -> 1
// parseInt('2',1) -> NaN
// parseInt('3',2) -> NaN
```

FlatMap 和 map 的作用几乎是相同的，但是对于多维数组来说，会将原数组降维。可以将 FlatMap 看成是 map + flatten ，目前该函数在浏览器中还不支持。

```javascript
[1,[2],3].flatMap(v=v+1);
// [2,3,4]
```

如果想将一个多维数组彻底的降维，可以这样实现

```javascript
const flattenDeep = arr => Array.isArray(arr) ? arr.reduce((a,b)=>[...a,...flattenDeep(b)],[]):[arr];
flattenDeep([1,[[2],[3,[4]],5]]);
// [1,2,3,4,5]
```

Reduce 作用是数组中的值组合起来，最终得到一个值

```javascript
function a(){
    console.log(1)
}
function b(){
    console.log(2);
}
[a,b].reduce((a,b)=>a(b()));
```

### async 和 await

一个函数如果加上 async,那么该函数就会返回一个 Promise

```javascript
async function test(){
    return '1'
}
console.log(test()); // -> Promise {<resolved>: "1"}
```

可以把 async 看成函数返回值使用 Promise.resolve() 包裹了下。

await 只能在 async 函数中使用

```javascript
function sleep(){
    return new Promise(resolve=>{
        setTimeout(()=>{
            console.log('finish');
            resolve('sleep');
        },2000);
    });
}
async function test(){
    let value = await sleep();
    console.log('object');
}
test();
```

上面代码会先打印 `finish` 然后再打印 `object` 。因为 `await` 会等待 `sleep` 函数 `resolve` ，所以即使后面是同步代码，也不会先去执行同步代码再来执行异步代码。

async 和 await 相比直接使用 Promise 来说，优势在于处理 then 的调用链，能够更清晰准确的写出代码。缺点在于滥用 await 可能会导致性能问题，因为 await 会阻塞代码，也许之后的异步代码并不依赖前者，但仍然需要等待前者完成，导致代码失去了并发性。

```javascript
let a = 0;
const b = async() =>{
    a = a + await 10;
    console.log('2',a);
    a = (await 10) + a;
    console.log('3',a);
}
b();
a++
console.log('1',a);

// VM3859:10 1 1
// VM3859:4 2 10
// VM3859:6 3 20
```

- 首先函数 b 执行，在执行到了 await 10 之前 a 的变量还是 0，因为在 await 内部实现了generators ,generators 会保留堆栈中东西，a=0 被保存下来。
- 因为 await 是异步操作，遇到 await 会立即返回一个 pending 状态的 promise 对象，暂时返回执行代码的控制权，使得函数外的代码得以继续执行，所以会先执行 同步代码 console.log('1',a);
- 同步代码之后就是异步代码，将保存下来的值拿出来用，这时候 a = 10
- 后面就是常规的执行代码了

### 常用的定时器函数

相关面试题：setTimeout、setInterval、requestAnimationFrame 各有什么特点？

#### **requestAnimationFrame**

请求动画帧。屏幕刷新频率，也就是屏幕上的图像每秒钟出现的次数，它的单位是赫兹（HZ）。当对着电脑屏幕什么也不做的情况下，显示器也会以每秒60次的频率在不断更新屏幕上的图像。我们之所以感觉不到变化的原因是因为人的眼睛有视觉停留效应，画之间间隔时间只有16.7ms（1000/60)，所以我们会觉得屏幕上的图像是静止不动的。

动画的本质就是要让人眼看到图像被刷新而引起的变化的视觉效果，这个变化要以连贯的平滑的方式过渡。

我们在每次刷新前，将图像的位置移动一个像素，这样一来，屏幕每次刷新出来的图像位置都比前一个要差一个像素。因为你会看到图像在移动，由于人眼的视觉停留效应，当前位置的图像停留在大脑的影响还没有消失，紧接着图像又被移到了下一个位置，因为你会看到图像在流畅地移动，这就是视觉效果上形成的动画。

requestAnimationFrame最大的优势就是系统决定的回调函数的执行时机，大概的意思就是回调函数会随着屏幕刷新的频率的变化而产生对应的变化。它能保证回调函数在屏幕每一次的刷新间隔中只执行一次，这样就不会引起丢帧现象，也不会导致动画出现卡顿的问题。

简单的调用

```javascript
let progress = 0;

function render(){
    progress +=1;
    if(progress<100){
        window.requestAnimationFrame(render)
    }
}
// 第一帧渲染
window.requestAnimationFrame(render);
```

另外它还有两个优势：

- Cpu节能：使用 setTimeout 实现的动画，当页面被隐藏到最小化时，仍然会在后台执行动画人物，由于此时页面处于不可见或者不可用状态，刷新画面也是没有意义的，完全是浪费资源。而 requestAnimationFrame 则完全不同，当页面处理未激活的状态下，该页面的屏幕刷新人物也会被系统暂停，有效节省CPU 开销
- 函数节流：在高频率（resize,scroll）中吗，为了防止在一个刷新间隔内发生多出函数执行，使用 requestAnimationFrame 可以保证每个刷新的间隔内，函数只被执行一次，这样既可以保证流畅性，也能更好的节省函数执行的开销。一个刷新间隔内函数执行多次是没有意义的，因为显示器刷新的频率是一定的，多次绘制不会在屏幕上体现出来。

由于浏览器兼容问题，需要优雅降级做兼容，具体代码,摘自 [requestAnimationFrame](https://github.com/darius/requestAnimationFrame/blob/master/requestAnimationFrame.js)：

```javascript
  if (!Date.now) {
    Date.now = function () {
      return new Date().getTime();
    }
  }
  (function () {
    var vendors = ['webkit', 'moz'];
    for (var i = 0; i < vendors.length && !window.requestAnimationFrame; i++) {
      var vp = vendors[i];
      window.requestAnimationFrame = window[vp + 'RequestAnimationFrame'];
      window.cancelAnimationFrame = window[vp + 'CancelAnimationFrame'] || window[vp + 'CancelRequestAnimationFrame'];
      if (/iP(ad|hone|od).*OS 6/.test(window.navigator.userAgent) || !window.requestAnimationFrame || !window.cancelAnimationFrame) {
        var lastTime = 0;
        window.requestAnimationFrame = function (cb) {
          var now = Date.now();
          var nextTime = Math.max(lastTime + 16, now);
          return setTimeout(function () {
            cb(lastTime = nextTime)
          }, nextTime - now)
        }
        window.cancelAnimationFrame = clearTimeout;
      }
    }
  })()
```



#### **setTimeout**

设置某个时间后执行某个动作，表示延时执行某个动作

#### **setInterval**

设置每隔多久执行某个动作，循环的。setInterval 将注册函数植入 Event Queue,如果前面的任务耗能太久，那么就需要等待。

因为JS 单线程的问题，setTimeout 可能不会按期执行，可以通过代码去修正 setTimeout ，从而使定时器相对准确

```javascript
  let period = 60 * 1000 * 60 * 2;
  let startTime = new Date().getTime();
  let count = 0;
  let end = new Date().getTime + period;
  let interval = 1000;
  let currentInterval = interval;

  function loop() {
    count++;
    // 代码执行所消耗的时间
    let offset = new Date().getTime() - (startTime + count * interval);
    let diff = end - new Date().getTime();
    let h = Math.floor(diff / (60 * 1000 * 60))
    let hdiff = diff % (60 * 1000 * 60);
    let m = Math.floor(hdiff / (60 * 1000));
    let mdiff = hdiff % (60 * 1000);
    let s = mdiff / (1000);
    let sCeil = Math.ceil(s);
    let sFloor = Math.floor(s);
    // 得出下一次循环所消耗的时间
    currentInterval = interval - offset;
    setTimeout(loop, currentInterval);
    console.log('时：' + h, '分：' + m, '毫秒：' + s, '秒向上取整：' + sCeil, '代码执行时间：' + offset, '下次循环间隔' + currentInterval)
  }
  setTimeout(loop, currentInterval)
```



### Proxy

Proxy 是 ES6 中新增的功能，可以用来自定义对象中的操作

```javascript
let p = new Proxy(target,handler)
// target 代表需要添加代理的对象
// Handler 用来自定义对象中的操作

// 可以很方便使用 Proxy 来实现一个数据的绑定和监听
let onWatch = (obj,setBind,getLogger)=>{
    let handler = {
        get(target,property,receiver){
            getLogger(target,property)
            return Reflect.get(target,property,receiver);
        },
        set(target,property,value,receiver){
            setBind(value);
            return Reflect.set(target,property,value);
        }
    };
    return new Proxy(obj,handler);
}

let obj = { a: 1 };
let value;
let pw = onWatch(obj,(v)=>{
    value = v
},(target,property)=>{
    console.log(`Get ${property} = ${target[property]}`);
})
pw.a = 2; // bind value to 2
pw.a // get a = 2
```

### 为什么 0.1 + 0.2 != 0.3

因为 JS 采用 IEEE 754 双精度版本（64位）,并且只要采用 IEEE 754 的语言都有该问题

原生解决方法：

```javascript
parseFloat((0.1+0.2).toFixed(10))
```

### 正则表达式

#### 元字符



| 元字符 | 作用                                                         |
| ------ | ------------------------------------------------------------ |
| .      | 匹配任意字符除了换行符和回车符                               |
| []     | 匹配方括号内的任意字符。比如 [0-9] 就可以用来匹配任意数字    |
| ^      | ^9 这样使用匹配以 9 开头，\[^9] 这样使用代表不匹配方括号内除了9的字符 |
| {1,2}  | 匹配1到2位字符                                               |
| (yck)  | 只匹配 yck 相同字符串                                        |
| \|     | 匹配 \| 前后任意字符                                         |
| \      | 转义                                                         |
| *      | 只匹配出现0次及以上 *前的字符                                |
| +      | 只匹配出现1次及以上 +前的字符                                |
| ?      | ？ 之前字符可选                                              |

#### 修饰符



| 修饰语 | 作用       |
| ------ | ---------- |
| i      | 忽略大小写 |
| g      | 全局搜索   |
| m      | 多行       |

#### 字符简写



| 简写 | 作用                 |
| ---- | -------------------- |
| \w   | 匹配字母数字或下划线 |
| \W   | 与上面相反           |
| \s   | 匹配任意的空白符     |
| \S   | 与上面相反           |
| \d   | 匹配数字             |
| \D   | 与上面相反           |
| \b   | 匹配单词的开始或结束 |
| \B   | 与上面相反           |

### V8下的垃圾回收机制

V8 实现了准确式 GC，GC 算法采用了分代式垃圾回收机制。因此，V8 将内存（堆）分为了新生代和老生代两部分

#### 新生代算法

新生代中的对象一般存活时间较短，使用 Scavenge GC 算法。

在新生代空间中，内存空间分为了两部分，分别为 From 空间和 To 空间。在这两个空间中，必定有一个空间是使用的，另一个空间是空闲的。新分配的对象会被放入 From 空间中，当 From 空间被占满的时候，新生代 GC 就会启动了。算法会检查 From 空间中存活的对象并复制到 To 空间中，如果有失活的对象就会销毁。当复制完成将 From 空间和 To 空间互换，这样 GC 就结束了。

#### 老生代算法

老生代中的对象一般存活时间较长且数量也多，使用了两个算法，分别是标记清除算法和标记压缩算法。

什么情况下对象会出现在老生代空间中：

- 新生代中的对象是否已经经历过一次 Scavenge 算法，如果经历过的话，会将对象从新生代空间移到老生代空间中。
- To 空间的对象占比大小超过 25 %。在这种情况下，为了不影响到内存分配，会将对象从新生代空间移到老生代空间中

老生代中的空间很复杂，有如下几个空间

```shell
enum AllocationSpace {
  // TODO(v8:7464): Actually map this space's memory as read-only.
  RO_SPACE,    // 不变的对象空间
  NEW_SPACE,   // 新生代用于 GC 复制算法的空间
  OLD_SPACE,   // 老生代常驻对象空间
  CODE_SPACE,  // 老生代代码对象空间
  MAP_SPACE,   // 老生代 map 对象
  LO_SPACE,    // 老生代大空间对象
  NEW_LO_SPACE,  // 新生代大空间对象

  FIRST_SPACE = RO_SPACE,
  LAST_SPACE = NEW_LO_SPACE,
  FIRST_GROWABLE_PAGED_SPACE = OLD_SPACE,
  LAST_GROWABLE_PAGED_SPACE = MAP_SPACE
};
```

在老生代中，以下情况会先启动标记清除算法：

- 某一个空间没有分块的时候
- 空间中被对象超过一定限制
- 空间不能保证新生代中的对象移动到老生代中

在这个阶段中，会遍历堆中所有的对象，然后标记活的对象，在标记完成后，销毁所有没有被标记的对象。在标记大型对内存时，可能需要几百毫秒才能完成一次标记。这就会导致一些性能上的问题。为了解决这个问题，2011 年，V8 从 stop-the-world 标记切换到增量标志。在增量标记期间，GC 将标记工作分解为更小的模块，可以让 JS 应用逻辑在模块间隙执行一会，从而不至于让应用出现停顿情况。但在 2018 年，GC 技术又有了一个重大突破，这项技术名为并发标记。该技术可以让 GC 扫描和标记对象时，同时允许 JS 运行，你可以点击 [该博客](https://v8project.blogspot.com/2018/06/concurrent-marking.html) 详细阅读。

清除对象后会造成堆内存出现碎片的情况，当碎片超过一定限制后会启动压缩算法。在压缩过程中，将活的对象像一端移动，直到所有对象都移动完成然后清理掉不需要的内存。

### Event Loop

#### 进程和线程

两个名词都是 CPU 工作时间片的一个描述。

进程描述了 CPU 在运行指令以及记载和保存上下文所需的时间，放在应用上来说就代表了一个程序。

线程是进程中更小的单位，描述了一段指令所需要的时间。

在浏览器中，打开一个 Tab 页面，就是创建了一个进程，一个进程里面可以有多个线程，例如渲染线程，JS 引擎线程，HTTP 请求线程。当发起一个请求时，就是在创建一个线程，当请求结束的时候，该线程就可能会被销毁掉。

众所周知，JS 运行时会阻止 UI 渲染，这两个线程是互斥的，因为 JS 可以修改 Dom ，如果在 JS 执行的时候 Ui 线程还在工作，就可能会导致不能正常安全渲染 UI。这也是单线程的一个好处，得益于 JS 是单线程与很像的，可以达到节省呢欧村，节约上下文切换时间，没有锁的问题的好处。

#### 执行栈

可以把执行栈认为是一个存储函数调用的栈结构，遵循先进后出的原则

#### 浏览器中的 Event Loop

当遇到异步代码的时候，会被挂起并在需要执行的时候加入到 Task  队列中。一旦执行栈为空，Event Loop 就会从 Task 队列中拿出需要你执行的代码并放入执行栈中执行，所以本质上 JS 中的异步还是同步行为。

不同的任务源会被分配到不同的 Task 队列中，任务源可以分成 微任务（mocrotask） 和 宏任务（macrotask)。在 ES6 规范中，macrotask 被称为 task,microtask 被称为 jobs 。下面举个例子看看代码的执行顺序：

```javascript
console.log('script start')

async function async1() {
  await async2()
  console.log('async1 end')
}
async function async2() {
  console.log('async2 end')
}
async1()

setTimeout(function() {
  console.log('setTimeout')
}, 0)

new Promise(resolve => {
  console.log('Promise')
  resolve()
})
  .then(function() {
    console.log('promise1')
  })
  .then(function() {
    console.log('promise2')
  })

console.log('script end')
```

当我们调用 async1 函数的时候，会马上输出 async2 end,并且函数返回一个 Promise，接下来在遇到 await 的时候就让出线程开始执行 async1 外的代码，可以完全把 await 看成是让出线程的标志。

然后当同步代码全部执行完毕以后，就会执行所有的异步代码，那么就会又回到 await 的位置执行返回的 Promise 的 resolve 函数，这又会把 resolve 丢到微任务队列中，接下来执行 then 中的回调，当两个 then 中的回调全部执行完毕后，回到 await 的位置处理返回值，这时候可以看成 `Promise.resolve(返回值).then()`，然后 await 后的代码全部被包裹进了 then 的回调中，所以 `console.log('async1 end')` 会优先执行于 `setTimeout`。

微任务包括 `process.nextTick` ，`promise` ，`MutationObserver`。

宏任务包括 `script` ， `setTimeout` ，`setInterval` ，`setImmediate` ，`I/O` ，`UI rendering`。

Event Loop 执行顺序如下所示：

- 首先执行同步代码，这属于宏任务
- 当执行完所有同步代码后，执行栈为空，查询是否有异步代码需要执行
- 执行所有微任务
- 当执行完所有微任务后，如有必要会渲染页面
- 然后开始下一轮 Event Loop，执行宏任务中的异步代码，也就是 `setTimeout` 中的回调函数

Node 中的 Event Loop

涉及的面试题：Node 中 Event Loop 和 浏览器的有什么不同？process.nextTick 执行顺序

Node 中的 Event Loop 分成6个阶段，它们会按照顺序反复运行，每当进入某一个阶段的时候，都会从对应的回调队列取出函数去执行。当队列为空或者执行的回调函数数量达到系统设定的阈值，就会进入下一个阶段

```shell
┌──────────────────────────┐
┌─>│        timers         │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
│  │     I/O callbacks     │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
│  │     idle, prepare     │
│  └──────────┬────────────┘      ┌───────────────┐
│  ┌──────────┴────────────┐      │   incoming:   │
│  │         poll          │<──connections───     │
│  └──────────┬────────────┘      │   data, etc.  │
│  ┌──────────┴────────────┐      └───────────────┘
│  │        check          │
│  └──────────┬────────────┘
│  ┌──────────┴────────────┐
└──┤    close callbacks    │
   └───────────────────────┘
```

**timer**

timer 阶段会执行 setTimeout 和 setInerval 回调，并是由 poll 阶段控制的

 同样，在 Node 中定时器指定的时间也不是准确的时间，只是尽快执行。

**I/O**

I/O 阶段会处理上一轮循环中少数未执行的的 I/O 回调

**dle,prepare**

idle,prepare 阶段内部实现

**poll**

poll 阶段很重要，在这一阶段中，系统会做两件事情

1. 执行到点的定时器
2. 执行 poll 队列中的事件

并且当 poll 中没有定时器的情况下，会发现以下两件事情

- 如果poll队列不为空，会遍历回调队列并同步执行，直到队列为空或者系统限制
- 如果poll队列为空,会发生两件事情
  - 如果有 setImmediate 需要执行的时候，poll 阶段会停止并且进入到 check 阶段执行 setImmediate
  - 如果没有 setImmediate 需要执行，会等待回调被加入到队列中并立即执行回调，这里同样会有个超时时间设置防止一直等待下去

当然设定了 timer 的话且 poll 队列为空，则会判断是否有 timer 超时，如果有的话会回到 timer 阶段执行回调。

**check**

check 阶段执行 `setImmediate`

**close callbacks**

close callbacks 阶段执行了 close 事件。

首先在有些情况下，定时器的执行顺序其实是**随机**的

```
setTimeout(() => {
    console.log('setTimeout')
}, 0)
setImmediate(() => {
    console.log('setImmediate')
})
```

对于以上代码来说，`setTimeout` 可能执行在前，也可能执行在后

- 首先 `setTimeout(fn, 0) === setTimeout(fn, 1)`，这是由源码决定的
- 进入事件循环也是需要成本的，如果在准备时候花费了大于 1ms 的时间，那么在 timer 阶段就会直接执行 `setTimeout` 回调
- 那么如果准备时间花费小于 1ms，那么就是 `setImmediate` 回调先执行了

当然在某些情况下，他们的执行顺序一定是固定的，比如以下代码：

当然在某些情况下，他们的执行顺序一定是固定的，比如以下代码：

```
const fs = require('fs')

fs.readFile(__filename, () => {
    setTimeout(() => {
        console.log('timeout');
    }, 0)
    setImmediate(() => {
        console.log('immediate')
    })
})
```

在上述代码中，`setImmediate` 永远**先执行**。因为两个代码写在 IO 回调中，IO 回调是在 poll 阶段执行，当回调执行完毕后队列为空，发现存在 `setImmediate` 回调，所以就直接跳转到 check 阶段去执行回调了。

**process.nextTick**

这个函数是独立于 Event Loop 之外的，它有一个自己的队列，当每个阶段完成之后，如果存在 nextTick 阶段，就会清空队列中的所有回调函数，并优于其他 microtask 执行

```javascript
setTimeout(() => {
 console.log('timer1')

 Promise.resolve().then(function() {
   console.log('promise1')
 })
}, 0)

process.nextTick(() => {
 console.log('nextTick')
 process.nextTick(() => {
   console.log('nextTick')
   process.nextTick(() => {
     console.log('nextTick')
     process.nextTick(() => {
       console.log('nextTick')
     })
   })
 })
})
// nextTick
// nextTick
// nextTick
// nextTick
// timer1
// promise1
```



## 参考链接：

[前端面试题目已经答案汇总](https://github.com/Advanced-Frontend/Daily-Interview-Question/blob/master/datum/summary.md)

[防抖](https://yuchengkai.cn/docs/frontend/#%E9%98%B2%E6%8A%96)