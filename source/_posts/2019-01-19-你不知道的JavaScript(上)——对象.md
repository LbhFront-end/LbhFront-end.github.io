---
title: 你不知道的JavaScript(上)——对象
date: 2019-01-19 18:30:00
tags: 你不知道的JavaScript
categories: 
	- JavaScript
---

这个系列的作品是上一次当当网有活动买的，记得是上一年九月份开学季的时候了。后面一直有其他的事情，或者自身一些因素，迟迟没有开封这本书。今天立下一个 flag，希望可以在两个月内看完并记录这个系列的三本书，保持学习的激情，不断弥补自己的基础不够扎实的缺点。

[作者的github](https://github.com/getify/You-Dont-Know-JS)

书籍的购买链接，自己搜。

# 你不知道的JavaScript(上)——对象

上一章讲了函数调用位置的不同会造成 this 绑定对象的不同，那么对象是什么呢？为什么我们要绑定它？

## 语法

对象可以通过两种形式定义：声明（文字）形式和构造形式。

对象的文字语法大概是这样：

```javascript
var myObj = {
    key:value
    // ...
}
```

构造形式大概是这样的：

```javascript
var myObj = new Object();
myObj.key = value;
```

构造形式和文字形式生成的对象是一样的。唯一的区别是，在文字声明中你可以添加多个键/值对，但是在构造形式中你必须逐个添加属性。

> 用构造形式创建出来的对象是非常少的，一般来说会使用文字语法，绝大多数内置也是这样做的。

## 类型

对象是 JavaScript 的基础，在 JavaScript 中一共有六种主要类型（术语是“语言类型”）

- string
- number
- boolean
- null
- undefined
- object

注意，简单基本类型（string,boolean,number,null 和 undefined）本身并不是对象。null 有时被当做一种对象类型，但是这其实只是语言本身的一个 bug,即对 null 执行 typeof null 时会返回字符串 “object”(原理是这样的，不同的对象在底层都表示为二进制，在 JavaScript 中二进制前三位都是0的话会被判断为 object 类型，null 的二进制表示全是0，自然前三位也是0，所以执行 typeof 是会返回 “object”)。实际上，null 本身是基本类型。

有一种常见的错误说是“JavaScript 中万物是对象”，这显然是错误的。

实际上，JavaScript 中有许多特殊的对象子类型，我们可以称之为复杂基本类型。

函数就是对象的一个子类型（从技术角度来说就是“可调用的对象”）。JavaScript 中的函数是“一等公民”，因为它们本质上和普通函数的对象是一样（只是可以调用）。所以可以像操作其他对象一样操作函数（比如当做另一个函数的参数）。

数组也是对象的一种类型，具备一些额外的行为。数组中内容的组织方式比一般的对象要稍微复杂一些。

#### **内置对象**

JavaScript 中还有一些对象子类型，通常被称为内置对象，有些内置对象的名字看起来和简单基础类型一样，不过它们的关系更复杂.

- Stting
- Number
- Boolean
- Object
- Function
- Array
- Date
- RegExp
- Error

这些内置对象从表现形式来说很像其他语言中的类型（type）或者类（class）,比如 Java 中的 String 类。

但是在 JavaScript 中，它们实际只是一些内置函数，这些内置函数可以当做构造函数（由 new 产生的函数）来使用，从而可以构造一个对应子类的新对象。举例来说：

```javascript
var strPrimitive = "I am a string";
typeof strPrimitive; // string
strPrimitive instanceof String; // false

var strObject = new String("I am a string");
typeof strObject; // object
strObject instanceof String; // true

// 检查 sub-type 对象
Object.prototype.toString.call(strObject); //[Object String]
```

Object.prototype.toString 简单来说，可以认为子类型在内部借用了 Object 中的 toString 方法，从代码中可以看到，strObject 是由 String 构造函数创建的一个对象。

原始值 “I am a string” 并不是一个对象，它只是一个字面量，并且是一个不可变的值，如果要在这个字面量上执行一些操作，比如获取长度，访问其中的某个字符等，那需要将其转换为 String 对象。

幸好，在必要时语言自动把字符字面量转换成了一个 String 对象，也就是说你并不需要显示创建一个对象，JavaScript 社区汇中的大多数人都认为能够使用文字形式时就不要使用构造形式

思考下面的代码：

```javascript
var strPrimitive = "I am a string";
console.log(strPrimitive.length); // 13
console.log(strPrimitive.charAt(3)); // 'm'
```

使用以上两种方法，我们可以直接在字面量上面访问属性或者方法，之所以可以这样做，是因为引擎自动把字面量转换成了 String 对象，所以可以访问属性或者方法。同样的事情也会发生在数值字面量上，如果使用类似 42.457.toFiexd(2)的方法，引擎会把 42 转换成 new Number(42)，对于布尔字面量来说也是如此。

null 和 undefined 没有对应的构造形式，它们只有文字形式。相反，Date 只有构造，没有文字形式。

对于 Object、Array、Function 和 RegExp 来说，无论是使用文字形式还是使用构造形式，它们都是对象，不是字面量。在某些情况下，相比用文字形式创建对象，构造形式可以提供额外的一些选项。由于这两种形式都可以创建对象，所以我们首要选的是更简单文字形式。建议只在需要额外选项的时候使用构造形式。

Error 对象很少在代码中显示创建，一般是在抛出异常的时候被自动创建的，也可以使用 new Error(..) 这种构造形式来创建，不过一般来说是用不到的。

### 内容

之前我们提到过的，对象的内容是由一些存储在特定命名位置的（任意类型的）值组成的，我们称之为属性。

需要强调一点是，当我们说“内容”时，似乎在暗示这些值实际上被存储在对象内部，但是这只是它的表现形式，在引擎内部，这些值的存储方式是多种多样的，一般并不会存在对象容器内部。存储在对象容器内部的是这些属性的名称，它们就像指针（引用）一样，指向这些值的真正的存储位置。

思考下面的代码：

```javascript
var myObject = {
    a:2
}
myObject.a // 2
myObject["a"] // 2
```

如果要访问 myObject 中 a 的位置上的值，我们需要使用.操作符或者[] 操作符。.a 语法通常被称为“属性访问”。["a"]语法通常被称为 “键访问”。实际上它们访问的是同一个位置，并且会返回相同的值2，所以这两个术语是可以互换的。

这两种语法的去呗主要是，操作符要求属性名满足标识符的命名规范，而[".."]语法可以接受任意 UTF-8/Unicode 字符串作为属性名。举例来说，如果要引用名称为 “Super-Fun!”的属性，就必须使用 ["Super-func!""]语法来访问，因为 Super-Fun! 并不是一个有效的标识符属性名。

此外，由于 [".."]语法使用字符串来访问属性，所以可以在程序中构造这个字符串。比如说：

```javascript
var myObject = {
    a:2
}
var idx;
if(wantA){
   idx = "a"
}
// 之后
console.log(myObject[idx]); // 2
```

在对象中，属性名永远字符串，如果你是用 string (字面量)以外的其他值作为属性名，那它首先会转换为一个字符串。即使是数字也不例外，虽然在数组下标中使用的是数字，但是在对象属性名中数字会被转换成字符串，所以当心不要搞混对象和数组中数字的用法：

```javascript
var myObject = {};
myObject[true] = "foo";
myObject[3] = "bar";
myObject[myObject] = "baz"

myObject["true"]; // foo
myObject["3"]; // bar
myObject["object object"]; // baz
```

### 可计算属性名

如果需要通过表达式来计算属性名，那么刚刚讲到的 myObject[..]这种属性访问语法就有用了，如可以使用 myObject[prefix + name]。但是使用文字形式来访问对象时这样做是不行的。

ES6 增加了可计算属性名，可以在文字形式中 使用 []包裹一个表达式来作为属性名：

```javascript
var prefix = "foo";
var myObject = {
    [prefix + "bar"]："hello",
    [prefix + "baz"]："world",
}
myObject["foobar"]; // hello
myObject["foobaz"]; // world
```

可计算属性名最常用的场景是 ES6 的符号（Symbol），简单来说，它们是一种新的基础数据类型，包含一个不透明并且无法预测的值（从技术角度来说就是一个字符串）。一般来说你不会用到的符号的实际值（因为理论上来说在不同的  JavaScript 引擎中值是不同的），所以通常你接触的是符号的名称，比如 Symbol.Something 

````javascript
var myObject = {
    [Symbol.Something]:"hello world"
}
````

### 属性与方法

如果访问的对象属性是一个函数，有些开发者喜欢使用不一样的叫做以作区分，由于函数很容易被认为是属于某个对象，在其他语言中，属于对象（也被称为 “类”）的函数通常被称为“方法”，因此把“属性访问”说成“方法访问”也就不奇怪了。

JavaScript 的语法规范也作出了同样的区分。从技术角度来说，函数永远不会“属于”一个对象，所以把对象内部引用的函数称为“方法”似乎有点不妥。确实，有些函数具有 this 引用，有时候这些 this 确实会指向调用位置的对象引用，但是这种用法从本质上说并没有把这个函数变成一个“方法”，因为 this 是在运行时根据调用位置动态绑定的，所以函数和对象的关系最多也只能说是间接关系。

无论返回值是什么类型，每次访问对象的属性就是属性访问，如果属性访问放回的是一个函数，那它也并不是一个“方法”。属性访问返回的函数和其他函数没有任何区别（处理可能发生的隐式绑定 this）、

举个例子：

```javascript
function foo(){
    console.log("foo");
}
var someFoo = foo; // 对 foo 变量引用
var myObject = {
    someFoo:foo
}
foo(); // function foo(){...}
someFoo(); // function foo(){...}
myObject.someFoo; // function foo(){...}
```

someFoo 和 myObject.someFoo 只是对于同一个函数的不同引用，并不能说明这个函数是特别的或者“属于”某个对象。如果 foo()定义时在内部有一个 this 引用，那这两个函数引用的唯一区别就是 myObject.someFoo 中的 this 会被隐式绑定到一个对象。无论哪种引用形式否不能称之为"方法"

最保险的说法是，“函数”和“方法”在 JavaScript 中是可以互换的。

>  ES6 中增加了 super 引用，一般来说会被用在 class 中。super 的行为似乎更有理由把 super 绑定的函数称为 “方法”，但是，这些只是一些语义（和技术）上的微妙差别，本质上是一样的

即使在对象的文字形式声明了一个函数表达式，这个函数也不会"属于"这个对象——它们只是对相同函数对象的多个引用

```javascript
var myObject = {
    foo:function(){
        console.log("foo");
    }
}
var someFoo = myObject.foo;
someFoo; // function foo(){...}
myObject.foo; //function foo(){...}
```

### 数组

数组也支持[]访问形式，数组有一套更加结构化的值存储机制（不过仍然不限制值的类型）。数组期望的是数值下标，也就是说值存储位置（通常被称为索引）是非负整数，比如说 0 和 42

```javascript
var myArray = ["foo",42,"bar"];
myArray.length; //3
myArray[0]; // "foo"
myArray[2]; // "bar"
```

数组也是对象，所以虽然每个小标都是整数，你仍然可以给数组添加属性：

```javascript
var myArray = ["foo",42,"bar"];
myArray.baz = "baz";
myArray.length; // 3
myArray.baz; // "baz"
```

可以看到虽然添加了命名属性（无论是通过.语法还是[]语法），数组的 length 值并未发生变化。完全可以把数组当做一个普通的键/值对象来使用，并且不添加任何数值索引，但是这并不是一个好主意。数组和普通的对象都根据其对应的行为和用途进行了优化，所以最好只用对象来存储/值对，只用数组来存储数值下标/值对。

注意：如果你试图向数组添加一个属性，但是属性名“看起来”像一个数字，那它会变成一个数值下标（因为你会修改数组的内部而不是添加一个属性），例如

```javascript
var myArray = ["foo",42,"bar"];
myArray["3"] = "baz";
myArray.length; // 4
myArray[3]; // "baz"
```

### 复制对象

JavaScript 初学者最常见的问题之一就是如何复制一个对象，看起来应该有一个内置的 copy() 方法，实际上事情比你想象的更复杂，因为我们无法选择一个默认的复制算法。

举例子：

```javascript
function anotherFunction(){/*..*/}
var anotherObject = {
    c:true
}
var anotherArray = [];
var myObject = {
    a:2,
    b:anotherObject, // 引用，不是复本
    c:anotherArray, // 另一个引用
    d:anotherFunction
}
anotherArray.push(anotherObject,myObject);
```

如何准确表示 myObject 的复制呢？

首先，我们应该判断它是浅复制还是深复制。对于浅拷贝来说，复制出的新对象中 a 的值会复制旧对象中 a 的值，也就是2，但是新对象中 b、c、d三个属性其实只是三个引用，它们和旧对象中 b、c、d引用的对象是一样的。对于深复制来说，除了复制 myObject 以外还会复制 anotherObject 和 anotherArray 。这时候问题来了，anotherArray 引用了 anotherObject 和 myObject ，所以又需要复制 myObject ，这样就会由于循环引用导致死循环。

我们是应该检测循环引用并终止循环（不复制深层元素）？还是应当直接报错或者是选择其他方法？

除此之外，我们还不确定复制一个函数意味着什么，有些人会通过 toString 来序列化一个函数的源代码（但是结果取决于 JavaScript 的具体实现，而且不同的引擎对于不同类型的函数处理方式不一样）。那么要怎么解决这些棘手的问题？许多 JavaScript标准都提出了自己的解决办法，但是 JavaScript 应当采用哪种方法作为标准？在很长的一段时间内一直没有一个标准。

对于 JSON 安全（也就是说可以被序列化为一个 JSON 字符串并且可以根据这个字符串解析出一个结构和值完全一样的对象）的对象来说，有一种巧妙的复制方法：

```javascript
var newObj = JSON.parse(JSON.stringfy(someObj));
```

当然 ，这种方法需要保证对象是 JSON 安全的，所以只使用部分情况。

相比深复制，浅复制非常易懂并且问题要少得多，所以 ES6 定义了 Object.assign(..)方法来实现浅复制。Object.assign(..)方法的第一个参数是目标对象，之后还可以跟一个或多个源对象的所有可枚举（enumerable）的自有键（owned key）并把它们复制（使用 = 操作符赋值）到目标对象，最后返回目标对象，就像这样：

```javascript
var newObj = Object.assign({},myObject);
newObj.a; // 2
newObj.b === anotherObject; // true
newObj.c === anotherArray; // true
newObj.d === anotherFunction; // true
```

> 由于Object.assign(..)就是使用 = 操作符来赋值，所以源对象属性的一些特性（比如 writable）不会被复制到目标对象

### 属性描述符

在 ES5 之前，JavaScript 语言本身并没有提供可以直接检测属性特性的方法，比如判断属性是否可读。

但是从 ES5 开始，所有属性都具备了属性描述符

思考下面的代码：

```javascript
var myObject = {
    a:2
}
Object.getOwnPropertyDescriptor(myObject,"a");
/*
{
    value: 2,
    writable: true,
    enumerable: true,    
	configurable: true,    
}
*/
```

上面这个普通对象属性对应的属性描述符（已被称为“数据描述符”，因为它只保存了一个数据值）可不仅仅只是一个2，它还包含了另外三个特性：writable(可写)、enumerable(可枚举)、configurable(可配置)

在创建普通属性时属性描述符会使用默认值，我们也可以使用 Object.defineProperty(..)来添加一个新属性或者修改一个已有属性（如果它是 configurabel）并对特性进行设置。举个例子：

```javascript
var myObject = {};
Object.defineProperty(myObject,"a",{
    value:2,
    writable: true,
    enumerable: true,    
	configurable: true,       
});
myObject.a; // 2
```

我们使用 defineProperty(..)给 myObject 添加了一个普通的属性并显示指定了一些特性。然后，一般来说你不会使用这种方式，除非你想修改属性描述符。

#### **1.Writabel**

writable 决定了是否可以修改属性的值，举个例子：

```javascript
var myObject = {};
Object.defineProperty(myObject,"a",{
    value:2,
    writable: false, // 不可写
    enumerable: true,    
	configurable: true,       
});
myObject.a = 3;
myObject.a; // 2
```

可以看到我们对于属性值的修改静默失败（sliently failed），在严格模式下，这种方法还会报错。

```javascript
"use strict";

var myObject = {};
Object.defineProperty(myObject,"a",{
    value:2,
    writable: false, // 不可写
    enumerable: true,    
	configurable: true,       
});
myObject.a = 3; // TypeError
```

TypeError 错误表示我们无法修改一个不可写的属性

> 可以把 writable：false 看作是属性不可改变，相当于你定义了一个空的 setter严格来说，如果要和 writable：false一致的话，你的 setter 被调用时应当抛出一个 TypeError 错误

#### **2.Configurable**

只要属性是可配置的，就可以使用 defineProperty(..)方法来修改属性描述符

```javascript
var myObject = {
    a:2
};
myObject.a = 3;
myObject.a; // 3
Object.defineProperty(myObject,"a",{
    value:4,
    writable: true,
    enumerable: true,    
	configurable: false, // 不可配置 
});
myObject.a; // 4
myObject.a = 5;
myObject.a; // 5

Object.defineProperty(myObject,"a",{
    value:6,
    writable: true,
    enumerable: true,    
	configurable: true,
}); // TypeError
```

最后一个 defindProperty(..)会产生一个 TypeError 错误，不管是不是处于严格模式，尝试修改一个不可配置的属性描述符都会出错。把 configurable 修改成 false 是单向操作，无法撤销。

> 有一个例外，即便属性是 configurable：false,我们还是可以把 writable 的状态为 true 改成 false,但是无法从 false 改为 true.

除了无法修改之外，configurabel：false 还会禁止删除这个属性：

```javascript
var myObject = {
    a:2
};
myObject.a; // 2

delete myObject.a;
myObject.a; // undefined

Object.defineProperty(myObject,"a",{
    value:2,
    writable: true,
    enumerable: true,    
	configurable: false, // 不可配置 
});
myObject.a; // 2

delete myObject.a;
myObject.a; // 2
```

最后一个语句 delete 语句（静默）失败了，因为属性是不可配置的。

在上面的例子中，delete 只用来直接删除对象的（可删除）属性，如果对象的某个属性是某个对象/函数的最后一个引用者，对这个属性执行 delete 操作之后，这个未引用的对象/函数就可以被垃圾回收。但是，不要把 delete 看做一个释放内存的工具，它就是一个删除对象属性的操作，仅此而已。

#### **3.Enumerable **

最后一个属性描述符是 enumerable，从名字可以看出，这个描述符控制的是属性是否会出现在对象的属性枚举中，比如说 for..in 循环，如果把 enumerable 设置成 false，这个属性就不会出现在枚举中，虽然仍然可以正常访问它。相对的，设置成 true 就会让他出现在枚举中。

用户定义的所有普通函数默认都是可枚举的，如果不希望某些特殊的属性出现在枚举中，就可以手动设置为 false.

### 不变形

有时候，希望属性或者对象是不可改变的（无论是有意还是无意的），在 ES5 中可以通过很多方法来实现。

很重要的一点是，所有的方法创建的都是浅不变性，也就是说，它们只会影响目标对象和它的直接属性，如果目标对象引用了其他对象（数组、对象、函数等），其他对象的内容不受影响，仍然是可变的

```javascript
myImmutableObject.foo; // [1,2,3]
myImmutableObject.foo.push(4); // [1,2,3]
myImmutableObject.foo; // [1,2,3,4]
```

假设代码中的 myImmutableObject 已经被创建而且是不可变的，但是为了保护它的内容 myImmutableObject.foo ，你还需使用下面方法让 foo 也是不可变的。

#### **1.对象常量**

结合 writable：false 和 configurable ：false 就可以创建一个真正的常量属性（不可修改、重定义或删除）

```javascript
var myObject = {};
Object.defineProperty(myObject,"FAVORITE_NUMBER",{
    value:42,
    writable: false, 
	configurable: false, 
});
```

#### **2.禁止扩展**

禁止一个对象添加新的属性并且保留已有的属性，可以使用 Object.preventExtensions(...)

```javascript
var myObject = {
    a:2
};
Object.preventExtensions(myObject);
myObject.b = 3;
myObject.b; // undefined 
```

#### **3.密封**

Object.seal(..)会创建一个“密封”的对象。这个方法实际上会在一个现有对象上调用 Object.preventExtensions(..)并把所有现有属性标记为 configurable：false.

所以，密封之后不仅不能添加属性，也不能重新配置或者删除现有的属性（虽然可以修改属性的值）

#### **4.冻结**

Object.freeze(..) 会创建一个冻结对象，这个方法实际上会在一个现有对象上调用 Object.seal(..)把所有数据访问属性标记为 writable：false.这样就无法修改它们的值了。

这个方法是你可以应用在对象上的级别最高的不可变性，它会禁止对于对象本身及其任意直接属性的修改（不过这个对象引用的其他对象是不受影响的）

### [[Get]]

属性访问在实现时有一个微妙却非常重要的细节：

```javascript
var myObject = {
    a:2
};
myObject.a; // 2
```

myObject.a 是一次属性访问，但是这条语句不仅仅是在 myObject 中查找名字为 a 的属性，在语言规范中，myObject.a 在 myObject 实际上是实现了 [[Get]] 操作（有点像函数调用： [[Get]]（））。对象默认的内置 [[Get]] 操作首先在对象中查找是否有名称相同的属性，如果找到就会返回这个属性的值。

然后，如果没有的话，按照 [[Get]]算法的定义会执行另外一种非常重要的行为，后面会讲到（其实就是变量可能存在的 [[Prototype]]链，也就是原型链）

但是无论如何都没有找到名称相同的属性，那么  [[Get]] 属性就会返回 undefined。一种方法和访问变量时是不一样的，如果你引用了一个当前词法作用域中不存在的变量，并不会像对象属性一样返回 undefined ，而是会抛出一个 ReferenceError 异常：

```javascript
var myObject = {
    a:undefined 
};
myObject.a; // undefined 
myObject.b; // undefined 
```

从返回值的角度来看，这两个引用没有什么区别——它们都返回了 undefined,实际上底层的 [[Get]]操作对 myObject.b 进行了更复杂的处理，仅通过返回值，无法判断一个属性是存在并且持有一个 undefined 值，还是变量不存在，所以 [[Get]] 无法返回某个特定值而返回默认的 undefined ，后面会将如何区分。

### [[Put]]

既然有可以获取属性值的 [[Get]] 就有一定对应的 [[Put]] 操作。

可能会认为给对象的属性赋值会触发 [[Put]] 来设置或者创建这个属性，但是实际情况并不完全 是这样的。

[[Put]]被触发时，实际的行为取决于许多因素，包括对象中是否已经存在这个属性（这是最重要的因素）；

如果已经存在这个属性， [[Put]] 算法大致会检查下面这些内容。

1. 属性是否是访问描述符，如果是并且存在 setter 就调用 setter
2. 属性的数据描述符中 writable 是否是 false,如果是，在非严格模式下静默失败，在严格模式下抛出 TypeError 异常
3. 如果都不是，将该值设置为属性的值

如果对象中不存在这个属性， [[Put]] 操作会更加复杂，后面会说到。

### Getter 和 Setter

对象默认的 [[Put]] 和 [[Get]] 操作分别可以控制属性值的设置和获取

在 ES5 中可以使用 getter 和 setter 部分改写默认操作，但是只能应用在单个属性上，无法应用在整个对象上。getter 是一个隐藏函数，会在获取属性值时调用。setter 也是一个隐藏函数，会在设置属性值时调用。

当给一个属性定义getter、setter 或者两者都有时，整个属性会被定义为 ”访问描述符“（和”数据描述符“相对）。对于访问描述符来说，JavaScript 会忽略它们的 value 和 writable 特性，取而代之的是关心 set 和 get （还有 configurable 和 enumerable）特性.

```javascript
var myObject = {
    // 给 a 定义一个 getter
    get a(){
        return 2;
    }
}

Object.defineProperty(myObject/*目标对象*/,"b"/*属性名*/，{
    // 给 b 设置一个 getter
    get:function(){return this.a * 2},
    // 确保 b 会出现在对象属性列表中
    enumerable:true
})
myObject.a; // 2
myObject.b; // 4
```

不管是对象文字语法中的 get a(){...}还是 defineProperty(..) 中的显式定义，二者都会在对象中创建一个不会包含值的属性，对于整个属性的访问会自动调用一个隐藏函数，它的返回值会被当做属性访问的返回值：

```javascript
var myObject = {
    // 给 a 定义一个 getter
    get a(){
        return 2;
    }
}
myObject.a = 3;
myObject.a; // 2
```

由于我们只定义了 a 的 getter,所以对 a 的值进行设置时 set 操作会忽略赋值操作，不会抛出错误。而且即便有合法的 setter,由于我们定义的 getter 只会返回 2，所以 set 操作是没有意义的。

为了让属性更加合理，还应该定义 setter,和期望的一样，setter 会覆盖单个属性默认的 [[Put]] （也称为赋值）操作。通常来说，getter 和 setter 是成对出现的（只定义一个的话通常会产生意料之外的行为）：

```javascript
var myObject = {
    // 给 a 定义一个 getter
    get a(){
        return this._a_;
    },
    set a(val){
        this._a_ = val*2;
    },    
}
myObject.a = 2;
myObject.a; // 4
```

> 上例中，实际把赋值（[[Put]]）操作中的值2存储到了另一个变量`_a_`中，这个名称的命名方式是一个惯例，没有任何特殊的行为——和普通属性一样。

### 存在性

前面说到，myObject.a 的属性访问返回值可能是 undefined ，但是这个值有可能是属性中存储的 undefined ，也可能是因为属性不存在所以返回 undefined ，那么如何区分这两种情况？

我们可以在不访问属性值的情况下判断对象是否存在这个属性：

```javascript
var myObject = {
    a:2 
};
("a" in myObject); // true 
("b" in myObject); // false 

myObject.hasOwnProperty("a"); // true 
myObject.hasOwnProperty("b"); // false 
```

in 操作符会检查属性是否在对象及其 [[Prototype]]原型链中。相比之下，hasOwnProperty（..）只会检查属性是否在 myObject 对象中，不会检查 [[Prototype]] 链。

所有的普通对象都可以通过对 Object.prototype 的委托来访问 hasOwnProperty(..)，但是有的对象可能没有连接到 Object.prototype(通过 Object.create(null) 来创建的)。在这种情况下，hasOwnProperty函数就可能会失败

这时候可以使用一种更加强硬的方法来判断：Object.prototype.hasOwnProperty.call(myObject,"a");直接显示绑定。

> in 操作符可以检测容器内是否有某个值。但是它实际上检查的是某个属性名是否存在。对于数组来说，这个区别很重要，4 in [2,4,6] 的结果是 false,因为这个数组中包含的属性名没有4.

#### **1.枚举**

```javascript
var myObject = {}
Object.defineProperty(myObject/*目标对象*/,"a"/*属性名*/，{
    value:2,
    // 让 a 像普通属性一样可以枚举
    enumerable:true
})
Object.defineProperty(myObject/*目标对象*/,"b"/*属性名*/，{
    value:3,
    // 让 a 不可枚举
    enumerable:false
})
myObject.b; // 3
("b" in myObject); // true 
myObject.hasOwnProperty("b"); // true 

for(var k in myObject){
    console.log(k,myObject[k]);
}
// "a" 2
```

可以看到，myObject.b 确实有值，可是不会出现 for ... in 循环中，原因就是 “可枚举”  相当于 “可以出现在对象属性的遍历中”

也可以通过另外一种方式来区分属性是否可以枚举：

```javascript
var myObject = {}
Object.defineProperty(myObject/*目标对象*/,"a"/*属性名*/，{
    value:2,
    // 让 a 像普通属性一样可以枚举
    enumerable:true
})
Object.defineProperty(myObject/*目标对象*/,"b"/*属性名*/，{
    value:3,
    // 让 a 不可枚举
    enumerable:false
})
myObject.propertyIsEnumerable("a"); // true
myObject.propertyIsEnumerable("b"); // false

Object.keys(myObject); // ["a"]
Object.getOwnPropertyNames(myObject); // ["a","b"]
```

propertyIsEnumerable(..) 会检查给定的属性名是否直接存在于对象中（而不是原型链上）并且满足 enumerable：true.

Object.keys(..) 会返回一个数组，包含所有可枚举的属性，Object.getOwnPropertyNames(..) 会返回一个数组，包含所有属性，无论是否可枚举。

in 和 hasOwnProperty 函数的区别在于是否查找 [[Prototype]]链，然后，Object.keys(...)和 getOwnPropertyNames(..) 都只会查找对象直接包含的属性。

并没有内置的方法可以获取 in 操作符使用 的属性列表，不过可以递归遍历某个对象的整条[[Prototype]] 链并保存每一层中使用 Object.keys(...)得到的属性列表——只包含可枚举属性。

## 遍历

对于数值索引的数组，可以使用标准的 for 循环来遍历值：

```javascript
var myArray = [1,2,3];
for(var i = 0; i < myArray.length; i++){
    console.log(myArray[i]);
}
// 1 2 3
```

这实际上并不是在遍历值，而是遍历下标来指向值，如 myArray[i]。

ES5 中增加了一些数组的辅助迭代器，包括 forEach(..)、every(..)和some(..)。每种辅助迭代器都可以接受一个回调函数并把它应用到数组的每个元素上，唯一的区别就是它们对于回调函数的返回值的处理方式不同。

forEach(..)会遍历数组中所有值并忽略回调函数的返回值。every(..)会一直运行直到回调函数返回 false(或者 “假”值)，some(..)会一直运行直到回调函数返回 true(或者 ‘真“值)

every 和 some 中特殊的返回值和普通 for 循环中的 break 语句类似，它们会提前终止遍历。

使用 for..in 遍历对象是无法直接获取属性值的，因为它实际上遍历的是对象中所有可枚举的属性。

ES6 中增加一种用来遍历数组的 for..of 循环语法，可以直接遍历值而不是下标：

```javascript
var myArray = [1,2,3];
for(var v of myArray){
    console.log(v);
}
// 1 
// 2 
// 3
```

for..of 循环首先会想被访问对象请求一个迭代器对象，然后通过调用迭代器对象的 next() 方法来遍历所有返回值。

数组有内置的 @@iterator,因此 for..of 可以直接应用在 数组上，我们使用内置的 @@iterator来手动遍历数组：

```javascript
var myArray = [1,2,3];
var it = myArray[Symbol.iterator]();
it.next(); // {value:1, done:false}
it.next(); // {value:2, done:false}
it.next(); // {value:3, done:false}
it.next(); // {done:true}
```

> 我们使用  ES6 的符号 Symbol.iterator 来获取对象的  @@iterator内部属性。引用类似 iterator 的特殊属性时要使用符号名，而不是符号包含的值。此外，虽然看起来很像一个对象，但是  @@iterator本身并不是一个迭代器对象，而是一个迭代器对象的函数——这点很精妙并且非常重要

和数组不同，普通的对象并没有内置的@@iterator，所以无法自动完成 for..of 遍历。之所以这样，简单来说是为了避免影响未来的对象类型。

当然，可以个任何想遍历的对象定义 @@iterator ，举个例子：

```javascript
var myObject = {
    a:2,
    b:3
}
Object.defineProperty(myObject,Symbol.iterator,{
    enumerable:false,
    writable:false,
    configurable:true,
    value:function(){
        var o = this;
        var idx = 0;
        var ks = Object.keys(o);
        return{
            next:function(){
                return{
                    value:o[ks[idx++]],
                    done:(idx > ks.length)
                }
            }
        }
    }
});

// 手动遍历 myObject
var it = myObject[Symbol.iterator]();
console.log(it.next()); // {value: 2, done: false}
console.log(it.next()); // {value: 3, done: false}
console.log(it.next()); // {value: undefined, done: true}

// 用 for...of 遍历 myObject
for(var v of myObject){
    console.log(v);
}
// 2
// 3
```

> 这里使用 Object.defineProperty(...) 定义了我们自己的 @@iterator(主要是为了让它不可枚举)，不过注意。我们把符号当做可计算属性名。此外，也可以直接在定义对象时进行声明，比如：var myObject = {a:2,b:3,[Symbol.iterator:function(){/* .. */}]}

for .. of 循环每次调用 myObject 迭代器对象的 next 方法时，内部的指针都会向前移动并返回对象属性类别下一个值。

代码中的遍历非常简单，只是传递了属性本身的值，也可以在自定义数据结构上实现各种复杂的遍历，对于用户定义的对象来说，结合 for..of  循环和自定义迭代器可以组成非常强大的对象操作工具。

比如说：一个 Pixel 对象（有x 和 y 坐标值）列表可以按照距离原点的直线距离来决定遍历顺序，也可以过滤掉太远的点，只要迭代器的 next() 调用会返回 {value:...} 和 {done:true} ，ES6 中的 for...of 就可以遍历它。

实际上，甚至可以定义个无限迭代器，它永远不会结束并且总会返回一个新值（比如随机数、递增值、唯一标识符等等），你永远不会在 for..of 循环中使用这样的迭代器，因为它永远不会结束，你的程序会被挂起：

```javascript
var randoms = {
    [Symbol.iterator]:function(){
        return {
            next:function(){
                return{
                    value:Math.random();
                }
            }
        }
    }
}

var randoms_pool = [];
for(var n of randoms){
    randoms_pool.push(n);
    // 以防止无限运行！
    if(randoms_pool.length === 100) break;
}
```

这个迭代器会生成“无限个”随机数，因为我们添加了一个 break 语句，防止程序被挂起。

## 小结

JavaScript 中的对象有字面形式（var a = {}）和构造形式（var a = new Array(...)）。字面形式更常用，不过有时构造形式可以提供更多选项。

很多人都认为 “JavaScript 中万物都是对象”，这是错误的，对象是6个（或者7个）基础类型之一。对象有包括 function 在内的子类型，不同 子类型具有不同的行为，比如内部标签 [Object Array] 表示这是对象的子类型数组。

对象就是键/值对的集合，可以通过 .propName 或者 ["propName"] 语法来获取属性值。访问属性时，引擎实际上会调用内部的默认 [[Get]] 操作（在设置属性值是 [[Put]]），[[Get]] 操作符会检查对象本省是否包含这个属性，如果没有找到的话还是会查找 [[Prototype]]链。

属性的特性可以通过属性描述符来控制，比如 writable 和 configurable ，此外，可以使用 Object.preventExtensions(..)、Object.seal(..) 和 Object.freeze(..) 来设置对象（及其属性）的不可变性界别。

属性不一定包含值——它们可能是具备 getter/setter 的”访问描述符“。此外，属性可以是可枚举或者不可枚举的，这决定了它们是否会出现在 for .. in 循环中。

可以使用 ES6 的 for..of 语法来遍历对象数据结构（数组、对象等等）中的值，for..of 会寻找内置或者自定义的 @@iterator 对象并调用它的 next() 方法来遍历数据值。