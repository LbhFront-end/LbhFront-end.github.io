---
title: 你不知道的JavaScript(上)——原型
date: 2019-01-22 18:30:00
tags: 你不知道的JavaScript
categories: 
	- JavaScript
---

这个系列的作品是上一次当当网有活动买的，记得是上一年九月份开学季的时候了。后面一直有其他的事情，或者自身一些因素，迟迟没有开封这本书。今天立下一个 flag，希望可以在两个月内看完并记录这个系列的三本书，保持学习的激情，不断弥补自己的基础不够扎实的缺点。

[作者的github](https://github.com/getify/You-Dont-Know-JS)

书籍的购买链接，自己搜。

# 你不知道的JavaScript(上)——原型

上一章讲了对象，下面介绍和类相关的对象编程，在研究类的具体机制之前，先介绍面向类的设计模式：实例化（instantitation）、继承（inheritance）和（相对）多态（polymorphism）

可以看到，这些概念实际上无法直接对应到 JavaScript 的对象机制，因为会介绍许多 JavaScript 开发者所使用的解决方法（比如混入，mixin）

## [[Prototype]]

JavaScript 中的对象有一个特殊的 [[Prototype]]内置属性，其实就是对于其他对象的引用。几乎所有的对象在创建的时候 [[Prototype]] 属性都会被赋予一个非空的值。

注意：很快我们就可以看到，对象的 [[Prototype]] 链接可以为空，虽然很少见

```javascript
var myObject = {
    a:2
}
myObject.a; // 2
```

[[Prototype]] 引用有什么用？当你试图引用对象的属性时会触发 [[Get]] 操作，比如 myObject.a。对于默认的 [[Get]] 操作来说，第一步是检查对象本身是否有这个属性，如果有就使用它。

> ES6 中的 Proxy，如果包含这个的话，这里对于 [[Get]] 和 [[Put]] 的讨论就不适用。

但是如果 a 不在 myObject中，就需要使用对象的 [[Prototype]] 链了。

对于默认的 [[Get]] 操作来说，如果无法在对象本身找到需要的属性，就会继续访问对象的 [[Prototype]] 链：

```javascript
var anotherObject = {
    a:2
}
// 创建一个关联到 anotherObject 的对象
var myObject = Object.create(anotherObject);
myObject.a; // 2
```

> Object.create(...) 会创建一个对象并把这个对象的 [[Prototype]] 关联到指定的对象

现在 myObject 对象的 [[Prototype]] 关联到了 anotherObject。显然 myObject.a 并不存在，但是尽管如此。属性仍然成功地（在 anotherObject中）找到了值2.

但是，如果 anotherObject 也找不到 a 并且 [[Prototype]] 不为空的话，就会继续查找下去。

这个过程会持续到找到匹配的属性名或者完整的条 [[Prototype]] 链，如果是后者的话，[[Get]] 操作的返回值就是 undefined.

使用 for..in 遍历对象时原理和查找 [[Prototype]] 链类似，任何可以通过原型链访问到的（并且是 enumerable）的属性都会被枚举。使用 in 操作符检查属性在对象中是否存在时，同样会查找对象的整条原型链（无论属性是否可以枚举）：

```javascript
var anotherObject = {
    a:2
}
// 创建一个关联到 anotherObject 的对象
var myObject = Object.create(anotherObject);
for(var k in myObject){
    console.log('found:'+k);
}
// found:a
("a" in myObject); // true
```

因为，当你通过各种语法进行属性检查时都会查找 [[Prototype]] 链，知道找到属性或者查找完整条原型链

### Object.prototype

但是到哪里是 [[Prototype]] 的尽头呢？

所有普通的 [[Prototype]] 链最终都会指向内置的 Object.prototype 。由于所有的 “普通”（内置。不是特定主机的扩展）对象都“源于”（或者说 把 [[Prototype]] 的顶端设置为）这个 Object.prototype 对象，所以包含 JavaScript 中通用的功能。

比如说：.toString() 或者 .valueOf()，.hasOwnProperty() 和 .isPrototype()等等。

### 属性设置和屏蔽

给一个对象设置属性不仅仅是添加一个新属性或者修改已有的属性值。

```javascript
myObject.foo = "bar";
```

如果 myObject 对象中包含名为 foo 的普通数据访问属性，这条赋值语句就会修改已有的属性值。

如果 foo 不是直接存在于 myObject 中，[[Prototype]] 链就会被遍历，类似 [[Get]] 操作，如果原型链上找不到 foo ，foo 就会被直接添加到 myObject 上。然而，如果foo 存在于原型链上层，那么赋值语句的行为就会有些不同了。

然后，如果 foo 存在于myObject 中也出现在 myObject 的 [[Prototype]] 链上层，那么就会发生屏蔽。myObject 包含的 foo 属性会屏蔽原型链上层的所有 foo 属性，因为 myObject.foo 总是会选择原型链中最底层的 foo 属性。

屏蔽比我们想象中要更加复杂，下面分析一下如果 foo 不直接存在于 myObject 中而是存在于原型链上层时 myObject.foo = "bar" 会出现的三种情况：

1. 如果在 [[Prototype]] 链上层存在名为 foo 的普通数据访问属性并且没有被标记只读（writable:false），那就会直接在 myObject 中添加一个名为 foo 的新属性，它是屏蔽属性
2. 如果在 [[Prototype]] 链上层存在 foo,但是它被标记为只读（writable:false），那么无法修改已有属性或者在 myObject 上创建屏蔽属性。如果运行在严格模式下，代码会抛出一个错误，否则，这条赋值语句会被忽略，总之，不会发生屏蔽。
3. 如果在 [[Prototype]] 链上层存在 foo并且它是一个 setter，那就一定会调用这个 setter,foo 不会被添加到（或者说屏蔽于）myObject,也不会重新定义 foo 这个 setter。

大部分开发者都认为如果向 [[Prototype]] 链上层已经存在的属性([[Put]]) 赋值，就一定会触发屏蔽，但是如你所见，三种情况中只有一种（第一种）是这样的。

如果你希望在第二种和第三种情况下也屏蔽 foo，那就不能使用 = 操作符来赋值，而是使用 Object.defineProperty(..) 来向 myObject 添加 foo。

> 第二种情况是最令人以外的，只读属性会阻止 [[Prototype]] 链下层隐式创建（屏蔽）同名属性，这样做的主要是为了模拟类属性的继承。你可以把原型链上层的 foo 看到是父类的属性，它会被 myObject 继承（复制），这样一来 myObject 中的 foo 属性也是只读的，所以无法创建。但是一定要注意，实际上并不会发生类似的继承复制。这看起来有点奇怪，myObject 对象竟然会因为其他对象中有一个只读 foo 就不能包含 foo 属性，更奇怪的是，这个限制只存在 于 = 赋值中，使用 Object.defineProperty(..) 并不会受到影响

如果需要对屏蔽方法进行委托的话就不得不使用丑陋的显式伪多态。通常来说，使用屏蔽得不偿失，所以应当尽量避免使用。后面会介绍一种不使用屏蔽的更加简洁的设计模式。

有些情况下也会隐式产生屏蔽，思考下面的代码：

```javascript
var anotherObject = {
    a:2
}
// 创建一个关联到 anotherObject 的对象
var myObject = Object.create(anotherObject);
anotherObject.a; // 2
myObject.a; // 2
anotherObject.hasOwnProperty("a"); // true
myObject.hasOwnProperty("a"); // false

myObject.a++; // 隐式屏蔽
anotherObject.a; // 2
myObject.a; // 3
myObject.hasOwnProperty("a"); // true
```

尽管 myObject.a++ 看起来应该（通过委托）查找并增加 anotherObject.a 属性，但是别忘了 ++ 操作相当于 myObject.a = myObject.a + 1.因为 ++ 操作首先通过 [[Prototype]] 查找属性 a 并从 anotherObject.a 获取当前的属性值2，然后给这值加1，接着用 [[Put]] 将值3赋给 myObject 中新建的屏蔽属性 a 。

修改委托属性时一定要小心，如果想让 anotherObject.a 的值增加，唯一的方法是 anotherObject.a++；

## "类"

为什么一个对象需要关联到另一个对象？这样的好处是什么？但是在回答这个问题之前我们应知道 [[Prototype]] 不是什么？

前面说过，JavaScript 和面向类的语言不同，它并没有类作为对象的抽象模式或者说蓝图，实际上，JavaScript 才应该真正被称为 "面向对象"的语言，因为它是少有的可以不通过类，直接创建对象的语言。在 JavaScript 中，类无法描述对象的行为（因为根本就不存在类）对象直接定义自己的行为，JavaScript 中只有对象。

### "类"函数

多年以来，JavaScript 中有一种奇怪的行为一直被滥用，那就是模仿类。

这种奇怪的 “类似类”的行为利用了函数的一种特殊特性：所有的函数都默认会拥有一个名为 prototype 的共有的并不可枚举的属性，它会指向另一个对象，这个对象正是调用该构造函数而创建的实例的原型。原型指的是每个 JavaScript 对象在创建的时候会与之关联的另一个对象，这个对象就是原型，每一个对象都会从原型中“继承”属性。

```javascript
function Foo(){
    //...
}
Foo.prototype; // {}
```

这个对象通常被称为 Foo 的原型，因为我们通过名为 Foo.prototype 的属性引用来访问它。

那么找个对象是什么？

最直接的解释就是，通过调用 new Foo() 创建的每个对象都最终（有点武断）被 [[Prototype]] 链接到这个 “Foo.prototype” 对象。

```javascript
function Foo(){
    //...
}
var a = new Foo();
Object.getPrototypeOf(a) === Foo.prototype; // true
```

使用 new Foo() 时会创建 a ,其中一步就是将 a 内部的 [[Prototype]] 链接到 Foo.prototype 所指向的对象。

在面向类的语言中，类可以被复制（或者说实例化）多次，就像用模具制作东西一样，之所以会这样是因为实例化（或者继承一个类）就意味着“把类的行为复制到物理对象中”，对于每一个新实例来说都会重复这个过程。

但是在 JavaScript 中，并没有类似的复制机制，你不能创建一个类的多个实例，只能创建多个对象，它们的 [[Prototype]] 关联的是同一个对象，但是在默认的情况下并不会复制，因为这些对象之间并不会完全失去联系，它们是互相关联的。

new Foo() 会生成一个新对象（我们称之为 a）,这个新对象的内部链接 [[prototype]] 关联的是 Foo.prototype 对象。

最后我们得到了两个对象，它们之间互相关联，我们并没有初始化一个类，实际上我们并没有从 “类”中复制任何一个行为到一个对象中，只是让两个对象互相关联。

实际上，绝大多数 JavaScript 开发者不知道的秘密是，new Foo() 这个函数调用实际上并没有直接创建关联，这个关联只是一个意外的副作用。new Foo() 只是间接完成了我们的目标：一个关联到其他对象的新对象。

那么有没有更直接的方法来做到这一点呢？当然，功臣就是 Object.create()。后面会介绍它。

#### **关于名称**

在 JavaScript 中，我们并不会将一个对象（“类”）复制到另一个对象（实例），只是将它们关联起来，从数据角度来说，[[Prototype]] 机制如下图所示：

```
(b1)--										--(a1)
	|--->(Bar.prototype)--->(Foo.prototype)<---|
(b2)--										--(a2)
```

这个机制通常被称为原型继承，它常常被视为动态语言版本的类继承，这个名称主要是为了对应面向类的世界中继承的一样，但是违背了动态脚本对应的语义。

“继承”这个词会让人产生非常强的心理预期，仅仅在前面加上“原型”并不能区分出 JavaScript 和类继承几乎完全相反的行为。

继承意味着复制操作，JavaScript （默认）并不会复制对象属性，相反，JavaScript 会在两个对象之间创建一个关联，这样一个对象就可以通过委托访问另一个对象的属性和函数。委托这个术语可以更加准确地描述 JavaScript 中对象的关联机制。

还有个偶尔会用到的 JavaScript 术语差异继承，基本原则是在描述对象行为时，使用其不同于描述的特质。举个例子，描述汽车时你会说汽车是有四个轮子的一种交通工具，但是不会重复描述交通工具具备的通用特性（比如引擎）。

如果你把 JavaScript 中对象的所有委托行为都归结于对象本身并且把对象看作是实物的话，那就（差不多）可以理解差异继承了。默认情况下，对象并不会像差异继承那样暗示的通过复制生成，因此，差异继承也不适合用来描述 JavaScript 中的 [[Prototype]] 机制。

### "构造函数"

回到之前的代码：

```javascript
function Foo(){
    //...
}
var a = new Foo();
```

是什么让我们认为 Foo 是一个类呢？

其中一个原因his我们看到了关键字 new ,在面向类的语言汇总构造实例时也会用到它。另一个原因是，看起来我们执行了类的构造函数方法，Foo（） 调用方式很像初始化类时类构造函数调用方式。

处理令人迷惑的“构造函数”语义外，Foo.prototype 还有另一个绝招，思考下面的代码：

```javascript
function Foo(){
    //...
}
Foo.prototype.constructor ==== Foo; // true
var a = new Foo();
a.constructor === Foo; // true
```

Foo.prototype默认有一个共有的并且不可枚举的属性 .constructor，这个属性引用的是对象关联的函数。此外，我们可以看到这个 “够着函数”调用 new Foo() 创建的对象也有一个 .constructor 属性，指向“创建这个对象的函数”。

> 实际上 a 本身并没有 .constructor 属性，而且，虽然 a.constructor 确实指向 Foo 函数，但是这个属性并不是表示 a 由 Foo “构造”。

“类” 首字母要大写。

#### **1.构造函数还是调用**

上一段代码中很容易让人认为 Foo 是一个构造函数，因为我们使用 new 来调用它并看到它 “构造” 了一个对象。

实际上,Foo 和 程序中的其他函数没有任何区别，函数本身并不是构造函数，然而，当你在普通的函数调用加上 new 关键字之后，就会把这个函数调用变成了一个 “构造函数调用”。实际上，new 会劫持所有普通函数并用构造对象的形式来调用它。举例来说：

```javascript
function NothingSpecial(){
    console.log(Do not mind me!);
}
var a = new NothingSpecial();
// Do not mind me!
a; // {}
```

NothingSpecial 只是一个普通函数，但是使用 new 调用时，它就会构造一个对象并赋值给 a，这看起来是 new 的一个副作用（无论如何都会构造一个对象），这个调用是一个构造函数调用，但是 NothingSpecial  本身并不是一个构造函数。

换句话说，在 JavaScript 中对于 “构造函数” 最准确的说法是，所有带 new 的函数调用。

函数不是构造函数，但是当且仅当使用 new 时，函数调用会变成“构造函数调用”。

### 技术

JavaScript 开发者想要模仿类的行为：

```javascript
function Foo(name){
    this.name = name;
}
Foo.prototype.myName = function(){
    return this.name;
}
var a = new Foo("a");
var b = new Foo("b");

a.myName(); // "a"
b.myName(); // "b"
```

这段代码展示了另外两种“面向类”的技巧：

1. this.name = name  给每个对象（也就是 a 和 b ）都添加了一个 .name 属性，有点像类实例封装的数据值
2. Foo.prototype.myName = .. 可能是一个更加有趣的技巧，它会给Foo.prototype 对象添加一个属性（函数），现在，a.myName() 可以正常工作，但是你可能会觉得很惊讶，这是什么原理？

这段代码中，看起来似乎创建 a 和 b 会把 Foo.prototype 对象复制到这两个对象中，然而事实并不是这样的。

在前面介绍 默认[[Get]] 算法的时候我们介绍过 [[Prototype]]链，以及当属性不直接存在于对象中如何通过它来进行查找。

因此，在创建的过程中,a 和 b 的内部 [[Prototype]] 都会关联到 Foo.prototype 上，当 a 和 b 中都无法找到 myName 的时候，它会（通过委托）在 Foo.prototype 上找到。

#### **回顾”构造函数“**

之前讨论 .constructor 属性的时候说过，a.constructor  === Foo 为真意味着 a 确实有一个指向 Foo 的 .constructor  属性，但是事实并不是这样的。

实际上，.constructor  引用同样被委托给 Foo.prototype，而 Foo.prototype.constructor  默认指向了 Foo。

把 .constructor  属性指向 Foo 看作是 a 对象由 Foo "构造" 是非常容易理解的，但这只不过是一种虚假的安全感，a.constructor  只是通过默认的 [[Prototype]] 委托指向了 Foo,这和“构造”毫无关系。相反，对于 .constructor  的错误理解很容易对自己产生误导。

举例来说，Foo.prototype 的 .constructor   属性只是 Foo 函数在声明时的默认属性，如果你创建了一个新对象并替换了函数默认的 .prototype 对象引用，那么新对象并不会自动获得 .constructor   属性。

思考下面的代码：

```javascript
function Foo(){/*..*/}
Foo.prototype = {/*..*/} // 创建一个新原型对象
var a1 = new Foo();
a1.constructor === Foo; // false
a1.constructor === Object; // true
```

Object(...) 并没有 “构造” a1，看起来应该是 Foo() “构造”了它。大部分开发者都认为是 Foo() 执行了构造工作，但是问题在于，如果你认为 “constructor” 表示 “由...构造”的话，a1.constructor 应该是 Foo,但是它并不是 Foo.

a1 并没有 .constructor 属性，它会委托 [[prototype]] 链上的 Foo.prototype。但是这个对象也没有 .constructor 属性（不过默认的Foo.prototype 对象有这个属性！），所以它会继续委托，这次会委托链顶端的 Object.prototype，这个对象有 .constructor 属性，指向内置的 Object(..) 函数。

当然，你可以给 Foo.prototype 添加一个 .constructor 属性，不过这需要手动添加一个符合正常的不可枚举的属性。

```javascript
function Foo(){/*..*/}
Foo.prototype = {/*..*/} // 创建一个新原型对象
// 需要在 Foo.prototype 上 “修复”丢失的 .constructor 属性
// 新对象属性起到 Foo.prototype 的作用
Object.defineProperty(Foo.prototype,"constructor",{
    enumerable:false,
    writable:true,
    configurable:true,
    value:Foo // 让 .constructor 指向 Foo
})
```

修复 .constructor 需要很多手动操作，所以这些工作都是源于把 “constructor” 错误地理解为 “由...构造”。

实际上，对象的 .constructor 属性默认指向一个函数，而这个函数也有一个叫做 .prototype 的引用指向这个对象。“构造函数” 和 “原型” 这两个词默认只有松散的含义，实际的值可能适用也可能不适用。最好的办法是记住 “constructor 并不表示（对象）被（它）构造”

.constructor 并不是一个不可变的属性，它是不可枚举的，但是它的值是可写的（可以被修改）。此外，可以给任意的 [[Prototype]] 链中的任意对象添加一个名为  constructor  的属性或者对其进行修改，你可以任意对其赋值。

和 [[Get]] 算法查找 [[Prototype]] 链的机制一样， .constructor  属性的引用目标可能和你想的完全不同。

a1.constructor  是一个非常不可靠并且不安全的引用，通常来说要尽量避免使用这些引用。

## （原型）继承

我们已经看过了许多 JavaScript 程序中常用的模拟类行为的方法，但是如果没有 “继承”机制的话，JavaScript 中的类就是一个空架子。

实际上，我们了解了通常被称作原型继承的机制，a 可以 “继承” Foo.prototype 并访问 Foo.prototype 的 myName() 函数。但是之前我们只把继承看做是类和类之间的关系，并没有把它当做是类和实例之间的关系。

下面的代码使用的就是典型的 “原型风格”：

```javascript
function Foo(name){
    this.name = name;
}
Foo.prototype.myName = function(){
    return this.name;
}
function Bar(name,label){
    Foo.call(this,name);
    this.label = label;
}

// 我们创建了一个新的 Bar.prototype 对象并关联到 Foo.prototype 
Bar.prototype = Object.create(Foo.prototype);

// 注意，现在没有 Bar.prototype.constructor 
// 如果你需要这个属性的话可能需要手动修复一下它
Bar.prototype.myLable = function(){
    return this.label;
}
var a = new Bar("a","obj a");
a.myName(); // "a"
a.myLabel(); // "obj a"
```

这段代码的核心是语法 “Bar.prototype = Object.create(Foo.prototype);''调用 object.create(..) 会凭空创建一个“新”对象并把对象内部的 [[Prototype]] 关联到你的指定的对象（本例子是 Foo.prototype）.

换句话说，这个语法的意思就是：“创建一个新的Bar.prototype 对象并把它关联到 Foo.prototype ”。

声明 function Bar(){...}时，和其他函数一样，Bar 会有一个 .prototype 关联到默认的对象，但是这个对象并没有像我们想要的关联到 Foo.prototype,因为我们创建了一个新对象把它关联到我们希望的对象上，直接把原始的关联对象抛弃掉。

注意，下面这两种方式是常见的错误做法，实际上它们都存在一些问题：

```javascript
// 和你想要的机制不一样
Bar.prototype = Foo.prototype;
// 基本上可以满足你的需求，但是可能会产生一些副作用
Bar.prototype = new Foo();
```

Bar.prototype = Foo.prototype; 并不会创建一个关联到 Bar.prototype 的新对象，它只是让 Bar.prototype 直接引用了 Foo.prototype 的对象，因此当你执行类似 Bar.prototype.myLabel = ... 的赋值语句会直接修改 Foo.prototype 对象本身。显然这不是我们想要的结果，否则根本不需要 Bar 对象，直接使用 Foo 就可以了，这样代码也会简单一些。

Bar.prototype = new Foo(); 的确会创建一个关联到 Bar.prototype 的新对象，但是它使用了 Foo(..) 的 “构造函数调用”，如果函数有些副作用（比如写日志、修改状态、注册到其他对象、给 this 添加数据属性等等）的话，就会影响新对象然后把旧对象抛弃掉，不能直接修改已有的默认对象。

如果能有一个标准并且可靠的方法来修改对象的 [[Prototype]] 关联就好了。在 ES6 之前，我们只能通过设置 `__proto__ `属性来实现，但是这个方法并不是标准并且无法兼容所有浏览器。ES6 添加了辅助函数 Object.setPrototypeOf(..) ,可以用标准并且可靠的方法来修改关联。

我们来对比两种把 Bar.prototype 关联到 Foo.prototype 的方法：

```javascript
// ES6 之前需要抛弃默认的 Bar.prototype
Bar.prototype = Object.create(Foo.prototype);
// ES6 开始可以直接修改现有的 Bar.prototype
Object.setPrototypeOf(Bar.prototype,Foo.prototype);
```

如果忽略掉 Object.create(..) 方法带来的轻微性能损失（抛弃的对象需要进行垃圾回收），它实际比ES6 及其之后的方法更短而且可读性更高，不过无论如何，这是两种完全不同的语法。

#### **检查 “类”关系**

假设有对象 a,如何寻找对象 a 委托的对象（如果存在的话）呢？在传统的面向类的环境中，检查一个实例（JavaScript 中的对象）的继承祖先（JavaScript 中的委托关联）通常被称为内省（或者反射）。

```javascript
function Foo(){
    // ..
}
Foo.prototype.blah = ....；
var a = new Foo();
```

我们如何通过内省找出 a 的 “祖先”（委托关联）呢？第一个方法就是站在 “类”的角度来判断：

```javascript
a instanceof Foo;// true
```

instanceof 操作符的左边是一个普通对象，右边是一个函数，instanceof 回答的问题是：在 a 的整条 [[Prototype] 是否有 Foo.prototype 指向的对象？

可惜，这个方法只能处理对象（a） 和 函数 （带 .prototype引用的 Foo）之间的关系。如果想要判断两个对象（比如 a 和 b ）是否通过 [[Prototype]] 链关联，只用 instanceof 无法实现。

> 如果使用内置的 .bind(...) 函数来生成一个硬绑定函数的话，该函数是没有 .prototype 属性的，在这样的函数上使用 instanceof 的话，目标函数的 .prototype 会代替硬绑定函数的 .prototype 。通常我们不会在 “构造函数调用” 中使用硬绑定函数，不过如果这么做的话，相对于直接调用目标函数。同理，在硬绑定函数上使用 instanceof  也相当于直接在目标函数上使用 instanceof 

下面这段荒谬的代码试图站在 “类”的角度来使用 instanceof 来判断两个对象的关系：

```javascript
// 用来判断 o1 是否关联到（委托）o2 的辅助函数
function isRelatedTo(o1,o2){
    function F(){}
    F.prototype = o2;
    return o1 instanceof F;
}
var a = {}
var b = Object.create(a);
isRelatedTo(b,a);
```

在 isRelatedTo 内部声明了一个一次性函数 F,把它的 .prototype重新赋值并指向 对象 o2,然后判断 o1 是否是 F 的一个实例。显而易见，o1 实际上并没有继承 F 也不是由 F 构造的，所以这种方法非常愚蠢且容易造成误解。问题的关键在于思考的角度，强行在 JavaScript 中应用类的语义就会造成这种尴尬的局面。

下面是第二种判断 [[Prototype]] 反射的方法，它更加简洁。

```javascript
Foo.prototype.isPrototypeOf(a); // true
```

我们只需要一个用来判断的对象就行，isPrototypeOf 回答的问题是：在 a 的整条 [[Prototype] 是否有 出现过 Foo.prototype 。

我们只需要两个对象就可以判断它们之间的关系，前者的prototype 会自动被访问

```javascript
b.isPrototypeOf(a);
```

我们也可以直接获取一个对象的 [[Prototype]] 链，在 ES5 中，标准的方法是：

```javascript
Object.getPrototypeOf(a);
Object.getPrototypeOf(a) === Foo.prototype; // true
```

绝大多数的浏览器也支持一种非标准的方法来访问内部的 [[Prototype]] 属性

```javascript
a.__proto__ === Foo.prototype; // true
```

这个奇怪的 `__proto__ `在 ES6 之前不是标准的属性神奇地引用了内部的 [[Prototype]] 对象，如果你想直接查找原型链的话，这个非常有用。和我们说过的 .constructor 一样， `__proto__ ` 实际上并不存在与正在使用的对象中，实际上，它和其他常用的函数（toString()、isPrototypeOf() 等等）一样，都存在于内置的 Object.prototype 中（它们是不可枚举的）。

此外， `__proto__ ` 很像一个属性，但是实际上它更想一个 getter/setter。它的内部实现大致是这样的

```javascript
Object.defineProperty(Object.prototype,"__proto__",{
    get:function(){
        return Object.getPrototype(this);
    },
    set:function(){
        // ES6 中的方法
        Object.setPrototypeOf(this,o);
        return o;
    }
});
```

因此，访问（获取值） `a.__proto__ `的时候，实际上是调用了  `a.__proto__ ()`（调用了 getter 函数）。虽然 getter 函数存在于 Object.prototype对象中，但是它的 this 指向了对象a。所以和 Object.getPrototypeOf(a) 结果相同。

 `__proto__ ` 是可设置属性，之前的代码使用 ES6 的 Object.setPrototypeOf(..)进行设置。然而，通常来说不需要修改已有对象的 [[Prototype]]。

我们只有在一些特殊情况下，需要设置函数默认 .prototype 对象的 [[Prototype]] ，让它引用其他对象（除了 Object.prototype）。这样可以避免使用全新的对象替换默认对象。此外，最好把 [[Prototype]] 对象关联看作是只读特性，从而增加代码的 可读性。

## 对象关联

 [[Prototype]] 机制技术存在于对象中的一个内部链接，它会引用其他对象。

通常来说，这个链接的作用是：如果在对象上没有找到需要的属性或者方法引用，引擎就会继续在  [[Prototype]] 关联的对象上进行查找，同理，如果在后者中也没有找到需要的引用就会继续查找它的 [[Prototype]] ，依次类推。这一系列对象的链接被称为 "原型链"。

### 创建关联

用 [[Prototype]] 机制的意义是什么？为什么 JavaScript 开发者费这么大力气在代码中创建这些关联？

```javascript
var foo = {
    something:function(){
        console.log("Tell me something good...");
    }
}
var bar = Object.create(foo);
bar.something(); // Tell me something good...
```

Object.create(..) 会创建一个新对象并把它关联到我们指定的对象中，这样我们就可以充分发挥  [[Prototype]]  的威力（委托）并且避免不必要的麻烦（比如使用 new 构造函数调用会生成 .prototype 和 .constructor 引用）。

> Object.create(null)会创建一个 拥有空（或者说 null） [[Prototype]] 链接的对象，这个对象无法进行委托。由于这个对象没有原型链，所以 instanceof 操作符无法进行帕努安东尼，因此总会返回 false.这些特殊的 [[Prototype]] 对象通常被称为字典，它们完全不会受到原型链的干扰，因此非常适合用来存储数据

我们并不需要类创建两个对象之间的关系，只需要通过委托来关联对象就足够了。而Object.create(..) 不包含任何类的诡计，所以它可以完美地创建我们想要的关联关系。

**Object.create()的 polyfill 代码**

Object.create(..) 是在 ES5 中新增的函数，所以在这之前如果要支持这个功能的话就需要一些简单的 polyfill 代码，它部分实现了 Object.create(null) 的功能：

```javascript
if(!Object.create){
    Object.create = function(){
		function F(){}
        F.prototype = o;
        return new F();
    }
}
```

这段代码使用了一个一次性的函数 F,我们改写了它的.prototype 属性使其指向想要关联的对象，然后再使用 new F() 来构造一个新对象进行关联。

由于Object.create(..) 可以被模仿，因此这个函数被应用得非常广泛，标准的ES5 内置的Object.create(..) 函数还提供了一系列附加功能，但是 ES5 之前的版本不支持这些功能，通常来说，这些功能的范围要小得多，但是由于完整性考虑，还是介绍一下：

```javascript
var anotherObject = {
    a:2
}
var myObject = Object.create(anotherObject,{
    b:{
        enumerable:false,
        writable:true,
        configurable:false,
        value:3
    },
    c:{
        enumerable:true,
        writable:false,
        configurable:false,
        value:4
    },    
});
myObject.hasOwnProperty("a"); // false
myObject.hasOwnProperty("b"); // true
myObject.hasOwnProperty("c"); // true
```

Object.create(..) 的第二个餐胡搜指定了需要添加到新对象中的属性名以及这些属性的属性描述符。因为 ES5 之前的版本无法模拟属性操作符，所以 pilyfill 代码无法实现这个附加功能。

通常来说并不会使用Object.create(..) 的附加功能，所以对于很多人来说，上面的那段 polyfill 代码就足够了。

有些开发者更加严谨，他们认为只有能被完全模拟的函数才应该使用 polyfill 代码由于 Object.create(..) 是只能部分模拟的函数之一，所以这些狭隘的人认为如果你需要在 ES5 之前的环境中使用 这个功能的特性，那不要使用 polyfill 代码，而是使用给一个自定义函数并名字不是 Object.create 。你可以把你自己的函数定义成这样：

```javascript
function createAllLinkObject(o){
    function F(){}
    F.prototype = o;
    return new F();
}
var anotherObject = {
    a:2
}
var myObject = createAllLinkObject(anotherObject);
myObject.a; // 2
```



### 关联关系是备用

看起来对象之间的关联是处理“缺失”属性或者方法时的一种备用选项。这个说法有点道理，但是作者认为这并不是 [[Prototype]]  的本质：

```javascript
var anotherObject = {
    cool:function(){
        console.log("cool!");
    }
}
var myObject = Object.create(anotherObject);
myObject.cool(); // cool!
```

由于存在[[Prototype]]  机制，这段代码可以正常运行，但是如果你这样写只是为了让 myObject 在无法处理属性或者方法的时候可以使用备用的 anotherObject，那么你的软件就会变得有点神奇，而且很难理解和维护。

这并不是说任何情况下都不应该选择备用这种设计模式，但是在 JavaScript 中并不是很常见。所以你使用的是这种模式，那么或许应该退后一步重新思考一下这种模式是否合适。

你可以让你的 API设计不那么神奇，同时也可以发挥[[Prototype]]   关联的威力

```javascript
var anotherObject = {
    cool:function(){
        console.log("cool!");
    }
}
var myObject = Object.create(anotherObject);
myObject.doCool = function(){
    this.cool();// 内部委托！
}
myObject.doCool(); // cool!
```

这里调用 doCool 是实际存在于 myObject 中的，这让我们的 API 设计更加清晰。从内部来说，我们实现的是遵循委托设计模式，通过[[Prototype]]  委托到 anotherObject.cool() 

换句话说，内部委托比起直接委托可以让 API 接口设计更加清晰。

## 小结

如果要访问的堆对象中不存在的一个属性，[[Get]] 操作就会查找对象内部[[Prototype]]  关联的对象，这个关联关系实际上定义了一条“原型链”（有点像嵌套作用域链），在查找属性时会对它进行遍历。

所有普通对象都有内置的 Object.prototype，指向原型链的顶端（比如说全局作用域），如果在原型链中查找不到指定的属性就会停止。toString()、valueOf 等函数和其他一些通用的功能都存在于 Object.prototype 对象上，因此语言中所有的对象都可以使用它们。

关联两个对象最常用的方法是使用 new 关键字进行函数调用，在调用的4个步骤会创建一个关联其他对象的新对象。

使用 new 调用函数会把新对象的 .prototype 属性到“其他对象”，带 new 的函数调用通常被称为 “构造函数嗲用”，尽管它们实际上和传统的面向类语言中的类构造函数不一样。

虽然这些 JavaScript 机制和传统面向类的语言中的“类初始化”和“类继承”很相似，但是 机制有一个核心区别，就是不会进行赋值，对象之间是通过内部的 [[Prototype]]  链进行关联的。

相比之下，“委托”是一个更合适的术语，因为对象之间的关系不是复制而是委托。