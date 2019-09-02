---
title: 你不知道的JavaScript(上)——this 和原型对象
date: 2019-01-18 18:30:00
tags: 你不知道的JavaScript
categories: 
	- JavaScript
---

这个系列的作品是上一次当当网有活动买的，记得是上一年九月份开学季的时候了。后面一直有其他的事情，或者自身一些因素，迟迟没有开封这本书。今天立下一个 flag，希望可以在两个月内看完并记录这个系列的三本书，保持学习的激情，不断弥补自己的基础不够扎实的缺点。

[作者的github](https://github.com/getify/You-Dont-Know-JS)

书籍的购买链接，自己搜。

# 你不知道的JavaScript(上)——this 和原型对象

## 关于this

### 为什么要用 this

```javascript
function identify(){
    return this.name.toUpperCase();
}
function speak(){
    var greeting = `Hello, I am ${identify.call(this)}`;
    console.log(greeting);
}
var me = {
    name:'Kyle'
}
var you = {
    name:'Reader'
}
identify.call(me); // KYLE
identify.call(you); // Reader

speak.call(me); // Hello,I am KYLE
speak.call(you); // Hello,I am READER
```

这段代码可以在不同上下文对象（me 和 you）中重复使用函数 identify 和 speak ，不用针对每个对象编写不同的函数。

如果不使用 this,那么就需要给这两个函数显式传入一个上下文对象。

```javascript
function identify(context){
    return context.toUpperCase();
}
function speak(context){
    var greeting = `Hello, I am ${identify(context)}`;
    console.log(greeting);
}
identify(you); // READER
speak(me);// hello,I am KYLE
```

然而，this 提供了一种更优雅的方式隐式传递一个对象引用，因为可以将 API 设计得更加简洁并且易于复用。随着使用模式的复杂程序提高，显式传递上下文对象会让代码变得越来越混乱，使用 this 则不会这样。

### 误解

有两种常见于 对于 this 的解释，但是它们都是错误的。

#### **指向自身**

人们很容易把 this 理解成指向函数本省，这个推断从英语的语法角度是说得通的。那么为什么需要从函数内部引用函数自身呢？常见的原因是递归（从函数内部调用这个函数）或者可以写一个在第一次被调用后自己解除绑定的事件处理器。

记录 foo 被调用的次数，思考下面的代码。

```javascript
function foo(num){
    console.log("foo:"+num);
    // 记录foo 被调用的次数
    this.count++;
}
foo.count = 0;
var i;
for(i = 0; i < 10; i++){
    if(i > 5){
       foo(i)
    }
}
// foo:6
// foo:7
// foo:8
// foo:9
console.log(foo.count); // 0 为什么？
```

console.log 语句产生了 4条输出，证明 foo(..)确实是被调用了4次，但是 foo.count 的值仍然是0，显然从字面意思来理解 this 是错误的。上面这段代码其实在无意之中，会创建一个全局的 count 变量，它的值为 NaN。

可能有些人会通过创建另一个带有 count 属性的对象。

```javascript
function foo(num){
    console.log("foo:"+num);
    // 记录foo 被调用的次数
    data.count++;
}
var data = {
    count:0
}
var i;
for(i = 0; i < 10; i++){
    if(i > 5){
       foo(i)
    }
}
// foo:6
// foo:7
// foo:8
// foo:9
console.log(foo.count); // 4
```

从某个角度这确实解决了问题，但可惜忽略了真正的问题，无法理解 this 的含义和工作原理，而是使用了更熟悉的词法作用域技术。

如果要从函数 对象内部引用它本身，那只使用 this 是不够的，一般来说，需要通过一个指向函数对象的词法标识符（变量）来引用它。

思考下面的函数：

```javascript
function foo(){
    foo.count = 4; // foo 指向它本身
}
setTimeout(function(){
    // 匿名函数无法指向本身（没有名字）
},10);
```

第一函数叫做具名函数，在它内部可以使用 foo 来引用自身。但是在第二个例子中，传入 setTimeout(..)的回调函数没有名称标识符（这种函数被称为匿名函数），因此无法从函数内部引用本身。

> 还有一种传统但是现在已经被弃用和批判的用法，使用 arguments.callee 来引用当前正在运行的函数对象。这是唯一一种可以从匿名函数对象内部引用自身的方法。然而更好的方法是避免使用匿名函数，至少在需要自引用时使用具名函数（表达式）。argumentss.callee 已经被弃用，尽量不要使用它

所以，另一种解决 方法是使用 foo 标识符替代 this 来引用函数对象。

```javascript
function foo(num){
    console.log("foo:"+num);
    // 记录foo 被调用的次数
    foo.count++;
}
foo.count = 0;
var i;
for(i = 0; i < 10; i++){
    if(i > 5){
       foo(i)
    }
}
// foo:6
// foo:7
// foo:8
// foo:9
console.log(foo.count); // 0 为什么？
```

然而，这种方法同样回避了 this 的问题，并且完全依赖于变量 foo 的词法作用域。

另一种方法是强制 this 指向 foo 函数对象：

```javascript
function foo(num){
    console.log("foo:"+num);
    // 记录foo 被调用的次数
    this.count++;
}
foo.count = 0;
var i;
for(i = 0; i < 10; i++){
    if(i > 5){
        // 使用 call(..)确保 this 指向函数对象的本身
       foo.call(foo,i)
    }
}
// foo:6
// foo:7
// foo:8
// foo:9
console.log(foo.count); // 0 为什么？
```

#### **它的作用域**

第二种常见的误解是，this 指向函数的作用域，这个观点是片面的，在某种情况下是正确的。

需要明确的是，this 在任何情况下都不指向函数词法作用域，在 JavaScript 内部，作用域确实和对象很类似，可见的标识符都是它的属性。但是作用域“对象”无法通过 JavaScript 代码访问，它存于 JavaScript 引擎内部。

思考下面的代码，它试图（但是没有成功）跨越便捷，使用 this 来隐式引用 函数的词法作用域：

```javascript
function foo(){
    var a = 2;
    this.bar();
}
function bar(){
    console.log(this.a);
}
foo(); // ReferenceError: a is not defined
```

首先这段代码试图通过 this.bar() 来引用 bar 函数，这样调用成功纯属意外、调用 bar() 最自然的方式是忽略前面的 this,直接使用词法引用标识符。此外，这段代码还尝试使用 this 联通 foo 和 bar 两个函数的词法作用域，从而让 bar 函数可以访问 foo 作用域的变量 a,这是不可能实现的，使用 this 不可能在词法作用域中查到什么。

### this 到底是什么？

排除了一些错误的理解后，我们说过 this 是在运行时绑定的，并不是编写时绑定的，它的上下文取决于函数调用时的各种条件，this 的绑定和函数的声明位置没有任何关系，只取决于函数的调用方式。

当一个函数被调用时，会创建一个活动记录（也被称为执行上下文）。这个记录会包含函数在哪里被调用（调用栈）、函数的调用方式、传入的参数等信息。this 就是这个记录的一个属性，会在函数执行的过程中用到。

### 小结

学习 this 的第一步是明白 this 既不指向函数自身也不指向函数的词法作用域，抛开以前的错误的假设和理解。this 实际上是在函数被调用时发生绑定的，它指向什么完全取决于函数在哪里被调用。



## this 的全面解析

### 调用位置

在理解 this 的绑定过程之前，首先要理解调用位置：调用位置就是函数在代码中被调用的位置（而不是声明的位置）。只有仔细分析调用位置才可以回答这个问题：这个 this 到底引用的是什么？

通常来说，寻找调用位置就是寻找“函数被调用的位置”，但是做起来并没有那么简单，因为某些编程模式可能会隐藏真正的调用位置。

最重要的是分析调用栈（就是为了达到当前执行位置所调用的所有函数）。我们关心的调用位置就在当前正在执行的函数的前一个调用中。

下面看看调用栈和调用位置：

````javascript
function baz(){
    // 当前调用栈是:baz
    // 因此，当前调用位置是全局作用域
    console.log("baz");
    bar();
}
function bar(){
    // 当前调用栈是:baz -> bar
    // 因此，当前调用在 baz 中
    console.log("bar");
    foo();
}
function foo(){
    // 当前调用栈是:baz -> bar -> foo
    // 因此，当前调用在 bar 中
    console.log("foo");    
} 
baz(); // baz 的调用位置
````

注意上面是如何（从调用栈中）分析出真正的调用位置，因为它决定了 this 的绑定。

### 绑定规则

找到调用位置后，判断需要应用下面的四条规则中的哪一条。

#### **默认绑定**

最常用的函数调用类型：独立函数调用。可以把这条规则看做是无法应用其他规则时的默认规则。

思考下面的代码：

```javascript
function foo(){
    console.log(this.a);
}
var a = 2;
foo(); // 2
```

声明在全局作用域中的变量就是全局对象的一个同名属性，它们本质上就是同一个东西，并不是通过复制得到的，就像一个硬币的两面一样。

当调用 foo() 的时候，this.a 被解析成了全局对象a，因为在本例中，函数调用时应用了 this 的默认绑定，因此 this 指向了全局对象。可以通过分析调用位置来看看 foo 是如何调用的。在代码中，foo 是直接使用不带任何修饰符的函数引用进行调用的，因此因此只能使用默认绑定，无法应用在其他规则。

如果使用严格模式（strict mode）,则不能将全局对象用于默认绑定，因此 this 会绑定到 undefined:

```javascript
function foo(){
    "use strict"
    console.log(this.a);
}
var a = 2;
foo(); // TypeError: this is undefined
```

这里有一个微妙重要的细节，虽然 this 的绑定规则完全取决于调用位置，但是只有 foo 运行在非 严格模式下时，默认绑定才能绑定到全局对象，在严格模式下调用 foo 则不影响默认绑定：

```javascript
function foo(){
    console.log(this.a);
}
var a = 2;
(function(){
    "use strict";
    foo(); // 2
})();
```

#### **隐式绑定**

另一条需要考虑的是调用位置是否有上下文对象，或者说是否被某个对象拥有或包含，不过这种说法可能会造成一些误导。

思考下面的代码：

```javascript
function foo(){
    console.log(this.a);
}
var obj = {
    a:2,
    foo:foo
};
obj.foo(); // 2
```

需要注意的是 foo 的声明方式，及其之后是如何被当做引用属性添加到 obj 中的。但是无论是直接在 obj 中定义还是先定义再添加为引用属性，这个函数严格来说都不属于 obj 对象。然后，调用位置会使用 obj 的上下文来引用函数，因此可以说函数被调用时 foo 对象拥有或者包含函数引用。

当foo 被调用时，它的前面确实添加了对 obj 的引用。当函数引用有上下文对象时，隐式绑定规则会在函数调用中的 this 绑定到这个上下文对象。因为调用 foo  时 this 被绑定到 obj,因为 this.a 和 obj.a 是一样的。

对象属性引用链中只有一层或者说最后一层在调用位置中起到了作用。举例说：

```javascript
function foo(){
    console.log(this.a);
}
var obj2 = {
    a:42,
    foo:foo
};
var obj = {
    a:2,
    foo:foo
};
obj1.obj2.foo(); // 42
```

**隐式丢失**

一个常见的 this 绑定问题就是被隐式绑定的函数会丢失绑定对象，也就说说它会应用默认绑定，从而把 this 绑定到全局对象或者 undefined 上，取决于是否是严格模式。

思考下面的代码：

```javascript
function foo(){
    console.log(this.a);
}
var obj = {
    a:2,
    foo:foo
};
var bar = obj.foo; // 函数别名
var a = "oops, global"; // a 是全局对象的属性
bar(); // oops, global
```

虽然 bar 是 obj.foo 的一个引用，但是实际上，它引用的是 foo 函数本身，因此此时的 bar() 其实是一个不带任何修饰的函数调用，因此应用了默认绑定。

一种更微妙，更常见并且更出乎意料的情况发生在传入回调函数时：

```javascript
function foo(){
    console.log(this.a);
}
function doFoo(fn){
    // fn 其实引用的是 foo
    fn(); // 调用位置
}
var obj = {
    a:2,
    foo:foo
};
var a = "oops, global"; // a 是全局对象的属性
doFoo(obj.foo); // oops, global
```

参数传递其实就是一种隐式赋值，因此我们传入函数时也会被隐式赋值，所以结果和上一个例子一样。如果把函数传入语言内置的函数而不是传入自己声明的函数，会发生什么呢？结果是一样的，没有区别：

```javascript
function foo(){
    console.log(this.a);
}
var obj = {
    a:2,
    foo:foo
};
var bar = obj.foo; // 函数别名
setTimeout(obj.foo,100);// oops, global
// js 环境中内置的 setTimeout函数实现和下面的伪代码相似
function setTimeout(fn,delay){
    // 等待 delay 秒
    fn(); // 调用位置
}
```

回调函数丢失 this 绑定是非常常见的。除此之外，还有一种情况 this 的行为会出乎我们的意料：调用回调函数的函数可能会修改 this,在一些流行的库中事件处理器会把回调函数 的 this 强制绑定在触发事件的 DOM 元素上。这在一些情况下可能有用。

无论是哪种情况，this 的改变都是意想不到的。实际上你无法控制回调函数的执行方式，因此就没有办法控制调用位置以得到期望的绑定。后面会用固定 this 来修复这个问题。

#### **显式绑定**

在分析隐式绑定时，我们必须在一个对象内部包含一个指向函数的属性，并通过这个属性间接引用函数，从而把 this 间接（隐式）绑定到这个对象上。

那如果我们不想在对象内部包含函数引用，而想在某个对象上强制调用函数，该怎么做呢?

JavaScript 中的所有函数都有一些可用的特性（和它们的[[Prototype]] 有关），可以解决这个问题。具体的说，可以用 call(..) 和 apply(..) 方法。严格来说，JavaScript 的宿主环境有时会提供一些非常特殊的函数，它们并没有这两个方法。但是这样的函数非常罕见，JavaScript 提供的绝大多数函数已经自己创建的函数都可以使用这两个方法。

它们的第一个参数是一个对象，是给 this 准备的，接着在调用函数时，将其绑定到 this.因为可以直接指定 this 对象，所以称之为显式绑定。

```javascript
function foo(){
    console.log(this.a);
}
var obj = {
    a:2
};
foo.call(obj); // 2
```

通过 foo.call() 可以在调用 foo 时强制把它的 this 绑定到 obj 上。如果传入了一个原始值（字符串类型、布尔类型或者数字类型）来当做 this 的绑定对象，这个原始值会被转换成它的对象形式（也就是 new String(..)、new Boolean(..)、new Number(..)）。这通常被称为“装箱”

> 从 this 绑定的角度来说，call 和 apply 是一样的，区别在于参入的参数不一样，call 传入单个值，apply 可以传一个或者一个以上的以数组形式的值

可惜，显示绑定仍然无法解决之前提出的丢失绑定问题。

**1.硬绑定**

但是显示绑定的一个变种可以解决这个问题。

```javascript
function foo(){
    console.log(this.a);
}
var obj = {
    a:2
};
var bar = function(){
    foo.call(obj);
}
bar; // 2
setTimeout(bar,100); //2

// 硬绑定的 bar 不可能再修改它的 this
bar.call(window); // 2
```

上面的例子创建了函数 bar()，并在它的内部手动调用了 foo.call(obj)，因此强制把 foo 的 this 绑定到了 obj上。之后无论如何调用函数 bar ,它总会手动在 obj 上调用 foo.这种绑定是一种显式的强制绑定，我们称之为硬绑定。

硬绑定的典型应用场景就是创建一个包裹函数，负责接口参数并返回值：

```javascript
function foo(something){
    console.log(this.a,something);
    return this.a + something;
}
var obj = {
    a:2
};
var bar = function(){
    return foo.apply(obj,arguments);
}
var b = bar(3); // 2 3
console.log(b); // 5
```

另一中方法就是创建一个可以重复使用的辅助函数：

```javascript
function foo(something){
    console.log(this.a,something);
    return this.a + something;
}
// 简单的辅助绑定函数
function bind(fn,obj){
    return function(){
        return fn.apply(obj,arguments)
    }
}
var obj = {
    a:2
};
var bar = bind(foo,obj);
var b = bar(3);
console.log(b); // 5
```

由于硬绑定是一种非常常用的模式，所有 ES5 提供了内置的方法 Funtion.prototype.bind,它的用法如下

```javascript
function foo(something){
    console.log(this.a,something);
    return this.a + something;
}
var obj = {
    a:2
};
var bar = foo.bind(obj);
var b = bar(3);
console.log(b); // 5
```

bind(...)会返回一个硬编码的新函数，它会把你指定的参数设置为 this 的上下文并调用原始函数。

**2.API 调用“上下文”**

第三方库的许多函数，以及 JavaScript 语言和宿主环境中许多新的内置函数，都提供了一个可选的参数，通常被称为 "上下文"（context），其作用和 bind(..)一样，都是确保你的回调函数使用指定的 this，举个例子：

```javascript
function foo(el){
    console.log(el,this.id);
}
var obj = {
    id:'awesome'
};
// 调用 foo(..)时把 this 绑定到 obj
[1,2,3].forEach(foo,obj);
// 1 awesome 2 awesome 3 awesome
```

这些函数实际就是通过 call 或者 apply 实现了显式绑定，这样就可以少些一些代码。

#### **new 绑定**

这是第四条规则也是最后一条 this 绑定规则。在传统的面向类的语言中，“构造函数”是类中的一些特殊方法，使用 new 初始化类时会调用类中的构造函数，通常的形式是这样的：

```javascript
something = new MyClass(..);
```

JavaScript 也有一个 new 操作，使用方法看起来和那些面向类的语言是一样的，绝大多数中开发者都认为 JavaScript 中的 new 机制也和那些语言一样的，然后实则不同。

首先我们重新定义一个 JavaScript 中的构造函数，它只是一些使用 new 操作符时被调用的函数。它们并不会属于哪个类，也不会实例化一个类。实际上，它们甚至都不能说是一种特殊的函数类型，它们只是 被 new 操作符调用的普通函数而已。

举例来说，思考一下 Number（..）作为构造函数时的行为，ES5.1 中这样描述它

> 当 Number 在 new 表达式中被调用是，它只是一个构造函数：它会初始化新创建的对象

所以，包括内置对象函数（比如 Number(..)）在内的所有函数都可以用 new 来调用，这种函数调用被称为构造函数调用。这里有一个重要但是细微的区别：实际上并不存在所谓的“构造函数”，只有对函数的“构造调用”。

使用 new 来调用函数，或者说发生构造函数调用时，会自动执行下面的操作。

1. 创建（构造）一个全新的对象
2. 这个新对象会被执行 [[prototype]] 连接
3. 这个新对象会绑定到函数调用的 this
4. 如果函数没有返回其他对象，那么 new 表达式中的函数会自动返回这个新的对象

思考下面的代码：

```javascript
function foo(a){
    this.a = a;
}
var bar = new foo(2);
console.log(bar.a); // 2
```

使用 new 来调用 foo(..)，我们会构造一个新对象并把它绑定到 foo(..)调用中的 this上。new 是最后一种可以影响函数调用时 this 绑定行为的方法，我们称之为 new 绑定。

## 优先级

现在我们了解刀函数调用中 this 绑定的四条规则，需要做的是找到函数的调用位置并判断应当应用那条规则。但是，如果某个调用位置可以应用多条规则该怎么办？为了解决这个问题就必须给这些规则设定一个优先级。

毫无以为，默认绑定的优先级是这四条规则里面最低的，隐式和显式绑定哪个优先级更高，我们可以来测试一下：

```javascript
function foo(){
    console.log(this.a);
}
var obj1 = {
    a:2,
    foo:foo
}
var ojb2 = {
    a:3,
    foo:foo
}
obj1.foo(); // 2
obj2.foo(); // 3

obj1.foo.call(obj2); // 3
obj2.foo.call(obj1); // 2
```

可以看到，显式绑定优先级更高，也就是说在判断时应当先考虑是否可以存在显式绑定。

我们需要搞清楚 new 绑定和隐式绑定的优先级谁高谁低：

```javascript
function foo(something){
    this.a = something;
}
var obj1 = {
    foo:foo
}
var obj2 = {};

obj1.foo(2);
console.log(obj1.a); // 2

obj1.foo().call(obj2,3);
console.log(obj2.a); // 3

var bar = new obj1.foo(4); 
console.log(obj1.a); // 2
console.log(bar.a); // 4
```

可以看到 new 绑定比隐式绑定优先级高，但是 new 绑定和 显式绑定谁的优先级高呢？

> new 和 call/apply 无法一起使用，因此无法通过 new foo.call(obj1) 来直接进行测试，但是可以用硬绑定测试它俩的优先级

回忆一下硬绑定是怎么工作的，Function.prototype.bind() 会创建一个新的包装函数，这个函数会忽略它当前的 this 绑定（无论绑定的对象是什么），并把我们提供的对象绑定到 this 上。

这样看起来硬绑定（也是显式绑定的一种）似乎比 new 绑定的优先级更高，无法使用 new 来控制 this 的绑定。让我们验证一下：

```javascript
function foo(something){
    this.a = something;
}
var obj1 = {};
var bar = foo.bind(obj1);
bar(2);
console.log(obj1.a); // 2

var baz = new bar(3);
console.log(obj1.a); // 2
console.log(baz.a); // 3
```

可以看到，bar 被硬绑定到obj1上，但是 new bar(3) 并没有像我们预计的那样把 obj1.a 修改为 3.相反，new 修改了硬绑定（到 obj1）调用 bar(..)中的 this.因为使用了 new 绑定，我们得到一个名字为 baz 的新对象，并且 baz.a 的值是3.

再来看看看之前介绍的“裸”辅助函数 bind:

```javascript
function bind(fn,obj){
    return function(){
        fn.apply(obj,arguments);
    }
}
```

在辅助函数中 new 操作符的调用无法修改 this 绑定，但是在上一个例子中 new 的确修改了 this 绑定。实际上，ES5 中内置的 Function.prototype.bind(..)更加复杂，下面是 MDN 提供的一种 bind(..)的实现，为了方便我们阅读，作者进行了排版：

```javascript
if(!Function.prototype.bind){
    Function.prototype.bind = function(oThis){
        if(typeof this !== "function"){
            // 与 ES5 最接近的内部的 IsCallbale 函数
            throw new TypeError(
                "Function.prototype.bind -what is trying to be bound is not callable"
                )
        }
        var aArgs = Array.prototype.slice.call(arguments,1),
        fToBind = this,
        fNOP = function(){},
        fBound = function(){
            return fToBind.apply(this instanceof fNOP && oThis ? this : oThis),
            aArgs.concat(Array.prototype.slice.call(arguments));
        }
        fNOP.prototype = this.prototype;
        fBound.prototype = new fNOP();
        return fBound;
    }
}
```

> 这种 bind(...) 是一种 polyfill 代码（主要用于旧浏览器的兼容，比如说在旧浏览器中使用没有内置 bind 函数），对于 new 使用的硬绑定函数来说，这段 polyfill 代码在旧浏览器和 ES5 内置的 bind（..）函数并不完全相同（后面介绍为什么要在 new 中使用硬绑定函数）。由于 polyfill 并不是内置函数，所以无法创建一个不包含 .prototype 的函数。因此会有一些副作用。如果你要在 new 中使用硬绑定函数并且依赖 polyfill 代码的话，一定要非常小心。

下面是 new 修改 this 的相关代码：

```javascript
this instanceof fNOP && oThis ? this : oThis
// 以及
fNOP.prototype = this.prototype;
fBound.prototype = new fNOP();
```

简单来说，这段代码会判断硬绑定函数是否被 new 调用，如果是的话就是用新创建的 this 来替换硬绑定 的this.

那么为什么要在 new 中使用硬绑定函数呢？直接使用普通函数不是更简单？

之所以要在 new 中使用硬绑定函数，主要目的是预先设置函数的一些参数，这样在使用 new 初始化时就可以只传入其余的参数。bind（..）功能之一就是可以把除了第一个参数（第一个参数用于绑定 this）之外的其他参数都传给下一层的函数（这种技术称为“部分应用”，是“柯里化”的一种。关于柯里化更多的内容，可以查看[这里](http://laibh.top/2018-06-23-call-apply-bind.html)）举例说明：

```javascript
function foo(p1,p2){
    this.val = p1 + p2;
}
// 之所以使用 null 是因为这个例子中我们并不关心硬绑定的 this 是什么，反正使用 new 时，this会被修改
var bar = foo.bind(null,"p1");
var baz = new bar("p2");
baz.val; 
```

#### **判断 this**

现在我们可以根据优先级来判断函数在某个调用位置应用的是哪些规则，可以按照下面的顺序来进行判断：

1. 函数是否在 new 调用（new 绑定）？如果是的话 this 绑定 的是新创建的对象。

2. 函数是否通过 call,apply(显示绑定)或者硬绑定调用？如果是的话，this绑定的是指定的对象

   var bar = foo.call(obj2);

3. 函数是否在某个上下文对象中调用（隐式绑定）？如果是的话，this 绑定的是那个上下文的对象 

   var bar = obj1.foo();

4. 如果都不是的话，使用默认绑定，如果在严格模式下，就绑定到 undefined ，否则绑定到全局对象。

   var bar = foo;

对于正常函数来说，理解了这些知识就可以明白了 this 的绑定原理了，不过也有例外。

   

## 绑定例外

规则总有例外，这里也是一样的。在某些应当应用其他绑定规则时，实际上应用的是默认绑定规则

### 被忽略的 this

如果你把 null 或者 undefined 作为 this 的绑定对象传入 call、apply 或者 bind ，这些值在调用时会被忽略，实际应用到是默认的绑定规则：

```javascript
function foo(){
    console.log(this.a);
}
var a = 2;
foo.call(null); // 2
```

那么什么情况下会传入 null呢？

一种非常常见的做法是使用 apply(..)来展开一个数组，并且当做参数传入一个函数。类似地，bind(..)可以对参数进行柯里化（预先设置一些参数），这种方法有时非常有效。

```javascript
function foo(a,b){
    console.log("a:"+a+",b:"+b);
}
// 把数组展开成参数
foo.apply(null,[2,3]); // a:2,b:3
// 使用 bind(..)进行柯里化
var bar = foo.bind(null,2);
bar(3); // a:2,b:3
```

这两种方法都需要传入一个参数作为 this 的绑定对象，如果函数并不关心 this 的话，你仍然需要传入一个占位符，这时 null 可能是一个不错的选择，就像代码中所示那样。

> ES6 中，可以用 Spread 展开符（...）替代 apply(..)来展开数组。foo(...[1,2]) 和 foo(1,2)是一样的，这样就可以避免不必要的 this 绑定。可惜在 ES6 中没有柯里化的先关语法，还是要使用 bind(...)

然而总是使用 null来忽略 this 绑定可能产生一些副作用，如果某个函数确实使用了 this (比如 第三方库中的一个函数)，那默认绑定规则就会 this 绑定到全局对象（在浏览器中这个对象是 window），这将导致不可预计的后果（比如修全局对象）。

显而易见，这种方式可能会导致许多难以分析和追踪的 bug.

**更安全的 this**

一种“更安全”的做法是传入一个特殊的对象，把 this 绑定到这个对象不会对你的程序产生任何副作用，就像网络（以及军队）一样。我们可以创建一个“DMZ”(demilitarized zone,非军事区)对象——它就是一个空的非委托的对象（委托后面会讲到）。

如果我们在忽略 this 绑定时总是传入一个DMZ 对象，那就不用担心了，因为任何对于 this 的使用都会被限制在这个空对象中，不会对全局对象产生任何影响。

由于这个对象是一个空对象，可以用自己喜欢的变量名来表示。不过建议使用比较特殊的。

在 JavaScript 中创建一个空对象最简单的方式都是 Object.creact(null).Object.create(null)和{}很像，但是并不会创建 Object.prototype 这个委托，所以它比{}还空。

```javascript
function foo(a,b){
    console.log("a:"+a+",b:"+b);
}
// 我们的 DMZ 空对象
var ∅  = Object.create(null);
// 把数组展开成参数
foo.apply(∅ ,[2,3]); // a:2,b:3
// 使用 bind(..)进行柯里化
var bar = foo.bind(∅ ,2);
bar(3); // a:2,b:3
```

使用变量名 ∅ 不仅让函数更加安全，还可以提高代码的可读性，因为 ∅  表示”我希望this 是空的“，这比null 的含义更加清楚。

### 间接引用

另一个需要注意的是，有可能（无意或者有意地）创建一个函数的“间接引用”，在这种情况下，调用这个函数会应用默认绑定规则。

间接引用最容易在赋值时发生：

```javascript
function foo(){
    console.log(this.a);
}
var a = 2;
var o = { a: 3, foo:foo }
var p = { a: 4 };
o.foo(); // 3
(p.foo = o.foo)(); // 2
```

赋值表达式 p.foo = o.foo 的返回值是目标函数的引用，因此调用位置是 foo() 而不是 p.foo 或者 o.foo 根据之前说过的，这里会应用默认绑定。

对于默认绑定来说，决定 this 绑定对象的并不是调用位置是否处于 严格模式，而是函数体是否处于严格模式。如果函数体处于严格模式，this 会被绑定到 undefined ，否则 this 会被绑定到全局对象。

### 软绑定

之前我们已经看到过了，硬绑定这种方式 把 this 强制绑定到指定的对象（除了使用 new 时），防止函数调用应用默认绑定规则。问题在于，硬绑定会大大降低函数的灵活性，使用硬绑定之后就无法使用隐式绑定或者显式绑定来修改 this。

如果可以给默认绑定指定一个全局对象和 undefined 以外的值，那就可以实现和绑定相同的效果，同时保留隐式绑定或者显式绑定修改 this 的功能。

可以通过一种被称为软板定的方法来实现我们想要的效果：

```javascript
if(!Function.prototype.softBind){
    Function.prototype.softBind = function(obj){
        var fn = this;
        // 捕获所有 curried 对象
        var curried = [].slice.call(arguments,1);
        var bound = function(){
            return fn.apply((!this || this === (window || global)?obj:this,curried.concat.apply(curried,arguments)));
        }
        bound.prototype = Object.create(fn.prototype);
        return bound;
    }
}
```

除了软绑定之外，softBind(...)其他原理和 ES5 内置的bind（...）类似。它会对指定的函数进行封装，首先检查调用时 this ,如果 this 绑定到全局对象或者 undefined ，那就把指定的默认对象 obj 绑定到 this，否则就不会修改 this.此外，这段代码还支持可选的柯里化

下面看这个函数的调用：

```javascript
function foo(){
    console.log("name:"+this.name);
}
var obj1 = {name: "obj"},
    obj2 = {name: "obj2"},
    obj3 = {name: "obj3"};
var fooOBJ = foo.softBind(obj);
fooOBJ(); //name: obj

obj2.foo = foo.softBind(obj);
obj2.foo(); // name: obj2

fooOBJ.call(obj3); // name: obj3

setTimeout(obj2.foo,10); // name: obj
```

可以看到，软绑定版本的 foo() 可以手动将 this 绑定到 obj2 或者 obj3 上，但如果应用默认绑定，则会把 this 绑定到 obj。

## this 词法

ES6中介绍了一种无法使用前面这些规则的特殊函数类型：箭头函数

箭头函数并不是使用 function 关键字定义的，而是使用被称为“胖箭头”的操作符 => 定义的，箭头函数不使用 this 的四种标准规则，而是更加外层（函数或者全局）作用域来决定 this.

看看箭头函数的词法作用域：

```javascript
function foo(){
    // 返回一个箭头函数
    return (a) =>{
        // this 继承来自 foo()
        console.log(a);
    }
}
var obj1 = {
    a:2
}
var obj2 = {
    a:3
}
var bar = foo.call(obj1);
bar.call(obj2); // 2不是 3
```

foo() 内部创建的箭头函数会捕获调用时 foo() 的this。由于 foo() 的this 绑定到obj1 。bar(医用箭头函数的)this 也会绑定在 obj1,箭头函数的绑定无法被修改。（new 也不行）

箭头函数最常用于回调函数，例如事件处理器或者是定时器

```javascript
function foo(){
    setTimeout(()=>{
        // 这里的 this 在词法上继承自 foo()
        console.log(this.a):
    });
}
var obj = {
    a:2
}
foo.call(obj); //2
```

虽然 self = this 和箭头函数看起来都可以取代 bind(...)，但从本质上讲，它们想替代的是 this 机制。

如果你经常编写 this 风格的代码，但是绝大部分时候都会使用 self = this 或者 箭头函数来否定 this 机制，那你或者应当：

1. 只采用词法作用域并且完全抛弃错误 this 风格的代码
2. 完全采用 this 风格，在必要时使用 bind(...)，尽量避免使用 self = this 和箭头函数

当然包含这两种风格的程序可以正常运行，大那是在同一个函数或者同一个程序汇总混合使用这两种风格通常会使得代码更难维护，并且可能也会更难编写。

## 小结

如果要判断一个运行中函数的 this 绑定，就需要找到这个函数的直接调用位置。找到之后就可以顺序应用下面这四条规则来判断 this 的绑定对象。

1. 由new 调用？绑定到新创建的对象
2. 由call 或者 apply 、bind 调用？绑定到指定对象
3. 由上下文对象调用？绑定到那个上下文对象
4. 默认：在严格模式下绑定到 undefined ，否则绑定到全局对象

一定要注意，有些调用可能在无意或者有意使用 默认规则，如果想要更安全地忽略 this 绑定，可以使用一个DMZ 对象，比如 ∅ = Object.create(null);以保护全局对象。

ES6 中的箭头函数并不会使用四条规则，而是根据当前的词法作用域来决定 this,具体来说，箭头函数会继承外层函数调用的 this 绑定（无论 this 绑定到了什么）。这其实和 ES6 之前代码的 self = this 机制是一样的。