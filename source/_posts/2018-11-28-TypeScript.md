---
title: TypeScript 入门
date: 2018-11-28 17:30:54
tags: TypeScript
categories: 
	- TypeScript
---

一直以来，都希望可以掌握一门强类型语言，大学学的java、c语言都因为当时兴致不浓慢慢都丢得差不多了。TypeScript 是 JavaScript 的一个超集，白鹭引擎中的wing3 开发也是用ts，所以学习这本强类型语言。
由于是刚入门，在百度搜索上面看到有一个阮一峰老师点评的 [TypeScript入门教程](https://ts.xcatliu.com/introduction/what-is-typescript.html)

上面教程作者是：[xcatliu](https://github.com/xcatliu/)

根据这个教程来学习 TypeScript 并做相关记录。

## 简介

### 什么是TypeScript

官网的定义：

> TypeScript is a typed superset of JavaScript that compiles to plain JavaScript. Any browser. Any host. Any OS. Open source.

翻译：

> TypeScript 是 JavaScript 的类型的超集，它可以编译成纯 JavaScript。编译出来的 JavaScript 可以运行在任何浏览器上。TypeScript 编译工具可以运行在任何服务器和任何系统上。TypeScript 是开源的。

### 为什么选择 TypeScript

TypeScript 增加了代码的可读性和可维护性

- 类型系统实际上是最好的文档，大部分的函数看看类型的定义就可以知道如何使用了
- 可以在编译阶段就发现大部分错误，这总比在运行时候出错好
- 增强了编辑器和 IDE 的功能，包括代码补全、接口提示、跳转到定义、重构等

#### **TypeScript 非常包容**

- TypeScript 是 JavaScript 的超集，`.js` 文件可以直接重命名为 `.ts` 即可
- 即使不显式的定义类型，也能够自动做出[类型推论](https://ts.xcatliu.com/basics/type-inference.html)
- 可以定义从简单到复杂的几乎一切类型
- 即使 TypeScript 编译报错，也可以生成 JavaScript 文件
- 兼容第三方库，即使第三方库不是用 TypeScript 写的，也可以编写单独的类型文件供 TypeScript 读取

#### **TypeScript 拥有活跃的社区**

- 大部分第三方库都有提供给 TypeScript 的类型定义文件
- Google 开发的 Angular2 就是使用 TypeScript 编写的
- TypeScript 拥抱了 ES6 规范，也支持部分 ESNext 草案的规范

#### **TypeScript 的缺点**

任何事物都是有两面性的，作者认为 TypeScript 的弊端在于：

- 有一定的学习成本，需要理解接口（Interfaces）、泛型（Generics）、类（Classes）、枚举类型（Enums）等前端工程师可能不是很熟悉的概念
- 短期可能会增加一些开发成本，毕竟要多写一些类型的定义，不过对于一个需要长期维护的项目，TypeScript 能够减少其维护成本
- 集成到构建流程需要一些工作量
- 可能和一些库结合的不是很完美

### 安装 TypeScript

TypeScript 的命令行工具安装方法如下：

```bash
npm install -g typescript
```

以上命令会在全局环境下安装 `tsc` 命令，安装完成之后，我们就可以在任何地方执行 `tsc` 命令了。

编译一个 TypeScript 文件很简单：

```bash
tsc hello.ts
```

我们约定使用 TypeScript 编写的文件以 `.ts` 为后缀，用 TypeScript 编写 React 时，以 `.tsx` 为后缀。

### Hello TypeScript

简单例子

```ts
function sayHello(person:string){
    return 'Hello,' + person;
}

let user = 'Tom';
console.log(sayHello(user));
```

然后执行

```shell
tsc hello.ts
```

会编译成一个好的文件，hello.js

```ts
function sayHello(person) {
    return 'Hello,' + person;
}
var user = 'Tom';
console.log(sayHello(user));

```

TypeScript 中，使用 `:`指定变量类型， `:`的前后有没有空格都可以

上述例子中，用 `:`指定 person 参数类型为 `string`。**TypeScript只会进行静态检查，如果有错误，编译的时候就会报错。**

编译下面的内容

```ts
function sayHello(person:string){
    return 'Hello,' + person;
}

let user = [0, 1, 2];
console.log(sayHello(user));
```

编辑器会报错

![编译报错](/images/2018-11-28-TypeScript-编译报错.png)

但js文件还是编译出来了

```ts
function sayHello(person) {
    return 'Hello,' + person;
}
var user = [0, 1, 2];
console.log(sayHello(user));

```

**TypeScript 编译的时候即使报错了，还是会生成编译结果**，我们仍然可以使用这个编译之后的文件。

如果要在报错的时候终止 js 文件的生成，可以在 `tsconfig.json` 中配置 `noEmitOnError` 即可。关于 `tsconfig.json`，请参阅[官方手册](http://www.typescriptlang.org/docs/handbook/tsconfig-json.html)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/tsconfig.json.html)）。

## 基础

本部分介绍了 TypeScript 中的常用类型和一些基本概念，旨在让大家对 TypeScript 有个初步的理解。具体内容包括：

- 原始数据类型
- 任意值
- 类型推论
- 联合类型
- 对象的类型——接口
- 数组的类型
- 函数的类型
- 类型断言
- 声明文件
- 内置对象

### 原始数据类型

JavaScript 的类型分为两种：原始数据类型（[Primitive data types](https://developer.mozilla.org/en-US/docs/Glossary/Primitive)）和对象类型（Object types）。

原始数据类型包括：布尔值、数值、字符串、`null`、`undefined`以及 [ES6 中的新类型 `Symbol`](http://es6.ruanyifeng.com/#docs/symbol)。

#### **布尔值**

布尔值是最基础的类型，在TypeScript 中，使用 `boolean`定义布尔值类型

```ts
let isDone:boolean = false;
// var isDone = false;
```

注意，使用构造函数 `Boolean`创造的对象不是布尔值

```ts
let createdByNewBoolean:boolean = new Boolean(1);
// error TS2322: Type 'Boolean' is not assignable to type 'boolean'.'boolean' is a primitive, but 'Boolean' is a wrapper object. Prefer using 'boolean' when possible.
```

事实上 `new Boolean()`返回的是一个 `Boolean`对象：

```ts
let createdByNewBoolean:Boolean = new Boolean(1);
```

直接调用 `Boolean`，也可以返回一个 `boolean`类型：

```ts
let createByBoolean:boolean = Boolean(1);
```

在 TypeScript中，`boolean`是 JavaScript 中的基本类型，而 `Boolean`是 JavaScript 中的构造函数。其他基本类型（除了`null`和`undefined`）一样。

#### **数值**

使用 `number`定义数值类型：

```ts
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
// ES6 中的二进制表示法
let binaryLiteral: number = 0b1010;
// ES6 的八进制表示法
let octalLiteral: number = 0o733;
let notANumber: number = NaN;
let infinityNumber: number = Infinity;
```

编译结果：

```ts
var decLiteral = 6;
var hexLiteral = 0xf00d;
// ES6 中的二进制表示法
var binaryLiteral = 10;
// ES6 的八进制表示法
var octalLiteral = 475;
var notANumber = NaN;
var infinityNumber = Infinity;

```

其中 `0b1010` 和 `0o744` 是 [ES6 中的二进制和八进制表示法](http://es6.ruanyifeng.com/#docs/number#%E4%BA%8C%E8%BF%9B%E5%88%B6%E5%92%8C%E5%85%AB%E8%BF%9B%E5%88%B6%E8%A1%A8%E7%A4%BA%E6%B3%95)，它们会被编译为十进制数字。

#### **字符串**

使用 `string`定义字符串类型

```ts
let myName: string = 'Tom';
let myAge: number = 25;
// 模块字符串
let sentence: string = `Hello,my name is ${myName},
I'll be ${myAge + 1} years old next month.`
```

编辑结果

```ts
var myName = 'Tom';
var myAge = 25;
// 模块字符串
var sentence = "Hello,my name is " + myName + ",\nI'll be " + (myAge + 1) + " years old next month.";
```

 [ES6 中的模板字符串](http://es6.ruanyifeng.com/#docs/string#%E6%A8%A1%E6%9D%BF%E5%AD%97%E7%AC%A6%E4%B8%B2)，`${expr}` 用来在模板字符串中嵌入表达式。

#### **空值**

JavaScript 没有空值（Void）的概念，在 TypeScript 中，可以用 `void`表示没有任何的返回值的函数：

```ts
function alertName() :void{
    alert('HAHAHAHA');
}
```

声明一个 `void`类型的变量没有什么用，因为你只能将它赋值为 `undefined`或者是 `null`

```ts
let unusable: void = undefined;
```

#### **Null 和 Undefined**

在 TypeScript 中，可以使用 `null` 和 `undefined`来定义两个原始数据类型:

```ts
let u: undefined = undefined;
let n: null = null;
```

与 `void` 的区别是，`undefined` 和 `null` 是所有类型的子类型。也就是说 `undefined` 类型的变量，可以赋值给 `number` 类型的变量：

```ts
// 都不会报错
let u: undefined = null;
let n: null = undefined;

let num1: number = undefined;
let num2: number = null;

let num3:undefined;
let num4:null;
```

而 `void` 类型的变量不能赋值给 `number` 类型的变量：

```ts
let u: void;
let num: number = u;
// TS2322: Type 'void' is not assignable to type 'number'.
```

### 任意值

任意值（Any）用来表示允许赋值为任意类型

#### **什么是任意值类型**

如果是一个普通类型，在赋值过程中改变类型是不给允许的：

```ts
let myFavoriteNumber: string = 'seven';
myFavoriteNumber = 7;
// error TS2322: Type '7' is not assignable to type 'string'.
```

但如果是 `any`类型，则运行被赋值为任意类型

```ts
let myFavoriteNumber: any = 'seven';
myFavoriteNumber = 7;
```

#### **任意值的属性和方法**

在任意值上访问任何属性都是被运行的：

```ts
let anyThing: any = 'hello';
console.log(anyThing.myName);
console.log(anyThing.myName.firstName);
```

也运行调用任何方法

```ts
let anyThing: any = 'Tom';
anyThing.setName('Jerry');
anyThing.setName.sayHello('Jerry');
anyThing.setName.setFirstName('Jerry');
```

可以认为，**声明一个变量为任意值之后，对它的任何操作，返回的内容的类型都是任意值**。

#### **未声明类型的变量**

**变量如果在声明的时候，未指定其类型，那么它会被识别为任意值类型**：

```ts
let something;
something = 'seven';
something = 7;

something.setName('Tom');
```

等价于

```ts
let something: any;
something = 'seven';
something = 7;

something.setName('Tom');
```

### 类型推论

如果没有明确的指定类型，那么 TypeScript 会依照类型推论（Type Inference）的规则推断出来一个类型

#### **什么是类型推论**

一下代码没有指定类型，但是在编译的时候会报错

```ts
let number = 'seven';
number = 7;
// Type '7' is not assignable to type 'string'.
```

事实上，它等价于：

```ts
let number: string = 'seven';
number = 7;
// Type '7' is not assignable to type 'string'.
```

TypeScript 会在没有明确的指定类型的时候推测出一个类型，这就是类型推论。

**如果定义的时候没有赋值，不管之后有没有赋值，都会被推断成 `any` 类型而完全不被类型检查：**

```ts
let number;
number = 'seven'
number = 7;
```

### 对象的类型——接口

在TypeScript中，我们使用接口（interfaces）来定义对象的类型

#### **什么是接口**

在面向对象语言中，接口（Interfaces）是一个很重要的概念，它是对行为的抽象，而具体如何行动需要由类（classes）去实现（implements）

TypeScript 中的接口是一个灵活的概念，除了可用于[对类的一部分行为进行抽象](https://ts.xcatliu.com/advanced/class-and-interfaces.html#%E7%B1%BB%E5%AE%9E%E7%8E%B0%E6%8E%A5%E5%8F%A3)以外，也常用于对「对象的形状（Shape）」进行描述。

简单的例子

```ts
interface Person{
    name: string;
    age: number;
}

let tom: Person = {
    name: 'Tom',
    age: 25
}
```

上面的例子，我们定义了一个接口 `Person`，接着定义了一个变量 `tom`,它的类型是 `Person`。这样，我们就约束了 `tom`的形状必须和 `Person`一样了。

接口一般首字母大写。[有的编程语言中会建议接口的名称加上 `I` 前缀](https://msdn.microsoft.com/en-us/library/8bc1fexb%28v=vs.71%29.aspx)。

定义的变量比接口少了一些属性是不允许的：

```ts
interface Person{
    name: string;
    age: number;
}

let tom: Person{
    name: 'Tom'
}
// error TS2322: Type '{ name: string; }' is not assignable to type 'Person'.
// Property 'age' is missing in type '{ name: string; }'.
```

多了一些属性是不允许的：

```ts
interface Person{
    name: string;
    age: number;
}

let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male',
}
// error TS2322: Type '{ name: string; age: number; gender: string; }' is not assignable to type 'Person'.
 // Object literal may only specify known properties, and 'gender' does not exist in type 'Person'.
```

可见，**赋值的时候，变量的形状必须和接口的形状保持一致**

#### **可选属性**

有时候，我们希望不要完全匹配一个形状，那么可以用可选属性：

```ts
interface Person{
    name: string;
    age?: number;
}

let tom: Person = {
    name: 'Tom'
}
```

```ts
interface Person {
    name: string;
    age?: number;
}

let tom: Person = {
    name: 'Tom',
    age: 25
};
```

可选属性的含义是该属性可以不存在。

这时仍然不允许添加未定义的属性：

```ts
interface Person {
    name: string;
    age?: number;
}

let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
}
//  error TS2322: Type '{ name: string; age: number; gender: string; }' is not assignable to type 'Person'.
// Object literal may only specify known properties, and 'gender' does not exist in type 'Person'.
```

#### **任意属性**

有时候我们希望一个接口允许有任意的属性，可以使用以下的方式：

```ts
interface Person {
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    name: 'Tom',
    gender: 'male'
}
```

使用 `[propName: string]`定义了任意属性取 `string` 类型的值

需要注意的是，**一旦定义了任意属性，那么确定属性和可选属性都必须是它的子属性**

```ts
interface Person {
    name: string;
    age?: number;
    [propName: string]: string;
}

let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male',
}
// error TS2322: Type '{ name: string; age: number; gender: string; }' is not assignable to type 'Person'.
// Property 'age' is incompatible with index signature.
// Type 'number' is not assignable to type 'string'.
```

上例中，任意属性的值允许是 `string`，但是可选属性 `age` 的值却是 `number`，`number` 不是 `string` 的子属性，所以报错了。

另外，在报错信息中可以看出，此时 `{ name: 'Tom', age: 25, gender: 'male' }` 的类型被推断成了 `{ [x: string]: string | number; name: string; age: number; gender: string; }`，这是联合类型和接口的结合。

#### **只读属性**

有时候我们希望对象中的一些字段只能在创建的时候被赋值，那么可以用 `readonly`定义只读属性

```ts
interface Person {
    readonly id : number;
    name: string;
    age?: number;
    [propName: string] :any;
}

let tom: Person = {
    id: 89757,
    name: 'Tom',
    gender: 'male'
}

tom.id = 7893;
//  error TS2540: Cannot assign to 'id' because it is a constant or a read-only property.
```

上例中，使用 `readonly`定义的属性 `id`初始化后，又被赋值了，所以报错了。

**注意，只读的约束存在于第一次给对象赋值的时候，而不是第一次给只读属性赋值的时候**

```ts
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    name: 'Tom',
    gender: 'male'
};

tom.id = 89757;
// error TS2322: Type '{ name: string; gender: string; }' is not assignable to type 'Person'.
// Property 'id' is missing in type '{ name: string; gender: string; }'.
// error TS2540: Cannot assign to 'id' because it is a constant or a read-only property.
```

上例中，报错信息有两处，第一处是在对 `tom` 进行赋值的时候，没有给 `id` 赋值。

第二处是在给 `tom.id` 赋值的时候，由于它是只读属性，所以报错了。

### 数据的类型

在 TypeScript 中，数组类型有多种定义方式，比较灵活。

#### **[类型+方括号]表示法**

最简单的方法是使用 [类型+方括号]来表示数组：

```ts
let fibonacci: number[] = [1,2,3,4,5];
```

数组的项中不允许出现其他的类型

```ts
let fibonacci: number[] = [1,2,'3',4,5];
// error TS2322: Type 'string' is not assignable to type 'number'.
```

上例中，`[1, '1', 2, 3, 5]` 的类型被推断为 `(number | string)[]`，这是联合类型和数组的结合。

数组的一些方法的参数也会根据数组在定义时约定的类型进行限制：

```ts
let fibonacci: number[] = [1,2,3,4,5];
fibonacci.push('8');
//error TS2345: Argument of type '"8"' is not assignable to parameter of type 'number'.
```

上例中，`push` 方法只允许传入 `number` 类型的参数，但是却传了一个 `string` 类型的参数，所以报错了。

#### **数组泛型**

也可以使用数组泛型（Array Generic）`Array<elemType>`来表示数组：

```ts
let fibonacci: Array<number> = [1,1,2,3,5];
```

#### **用接口表示数组**

接口也可以用来描述数组：

```ts
interface NumberArray {
    [index: number]: number;
}
let fibonacci: NumberArray = [1,2,3,4,5];
```

`NumberArray` 表示：只要 `index` 的类型是 `number`，那么值的类型必须是 `number`。

#### **any 在数组中的应用**

一个比较常见的做法是， 用 `any`表示数组中允许出现任意类型：

```ts
let list: any[] = ['lbh',32,{website:'http://laibh.top'}]
```

#### **类数组**

类数组（Array-like Object）不是数组类型，比如 `arguments`:

```ts
function sum(){
    let args: number[] = arguments;
}
// error TS2322: Type 'IArguments' is not assignable to type 'number[]'.
// Property 'pop' is missing in type 'IArguments'.
```

事实上常见的类数组都有自己的接口定义，如 `IArguments`, `NodeList`, `HTMLCollection` 等：

```ts
function sum(){
    let args: IArguments  = arguments;
}
```

### 函数的类型

#### **函数声明**

在 JavaScript 中，有两种常见的定义函数的方式——函数声明（Function Declaration）和函数表示式（Function Expression）

```ts
// 函数声明（Function Declaration）
function sum(x, y){
    return x + y;
}

// 函数表达式（Function Expression）
let mySum = function(x, y){
    return x + y;
}
```

一个函数有输入和输出，要在 TypeScript 中对其进行约束，需要把输入和输入都考虑到，其中函数声明的类型定义较简单：

```ts
function sum(x:number, y:number) :number {
    return x + y;
}
```

注意，**输入多余的（或者少于要求的）参数，是不被允许的**：

```ts
function sum(x:number, y:number) :number {
    return x + y;
}

sum(1,2,3);
//  error TS2554: Expected 2 arguments, but got 3.
```

```ts
function sum(x:number, y:number) :number {
    return x + y;
}

sum(1);
// error TS2554: Expected 2 arguments, but got 1.
```



#### **函数表达式**

写一个对函数表达式（Function Expression）的定义：

```ts
let mySum = function(x: number, y: number) : number{
    return x + y;
}
```

这是可以通过编译的，不过事实上，上面的代码只对等号右侧的匿名函数进行了类型定义，而等号左边的 `mySum`，是通过赋值操作进行类型推论而推断出来的。如果需要我们手动给 `mySum` 添加类型，则应该是这样：

```ts
let mySum:(x:number, y:number) => number = function(x: number, y: number) : number{
    return x + y;
}
```

注意不要混淆了 TypeScript 中的 `=>` 和 ES6 中的 `=>`。

在 TypeScript 的类型定义中，`=>` 用来表示函数的定义，左边是输入类型，需要用括号括起来，右边是输出类型。

在 ES6 中，`=>` 叫做箭头函数，应用十分广泛，可以参考 [ES6 中的箭头函数](http://es6.ruanyifeng.com/#docs/function#%E7%AE%AD%E5%A4%B4%E5%87%BD%E6%95%B0)。

#### **用接口定义函数的形状**

也可以用接口的方式来定义一个函数需要符合的形式：

```ts
interface SearchFunc {
    (source: string, subString: string): boolean;
}
let mySearch: SearchFunc;
mySearch = function(source: string, subString: string){
    return source.search(subString) !== -1;
}
```

#### **可选参数**

与接口中的可选属性类似，我们可以用 `？`表示可选参数：

```ts
function buildName(firstName: string, lastName?: string){
    if(lastName){
        return `${firstName} ${lastName}`;
    }
    return firstName;
}
let tomcat = buildName('Tom','cat');
let tom = buildName('Tom');
```

需要注意的是，可选参数必须接在必需参数后面。换句话说，**可选参数后面不允许再出现必须参数了**：

```ts
function buildName(firstName?: string, lastName: string) {
    if (firstName) {
        return firstName + ' ' + lastName;
    } else {
        return lastName;
    }
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName('Tom', 'Cat');
// error TS1016: A required parameter cannot follow an optional parameter.
```

#### **参数默认值**

**TypeScript 会将添加了默认值的参数识别为可选参数**：

```ts
function buildName(firstName?: string, lastName: string = 'Cat') {
    if (firstName) {
        return firstName + ' ' + lastName;
    } else {
        return lastName;
    }
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName('Tom');
```

此时就不受「可选参数必须接在必需参数后面」的限制了：

```ts
function buildName(firstName: string = 'Tom', lastName: string ) {
    if (firstName) {
        return firstName + ' ' + lastName;
    } else {
        return lastName;
    }
}
let tomcat = buildName('Tom', 'Cat');
let cat = buildName(undefined, 'Cat');
```

#### **剩余参数**

ES6中，可以使用 `...rest`的方式获取函数的剩余参数（rest参数）

```ts
function push(array,...items){
    items.forEach(function(item){
        array.push(item);
    });
}
let a = [];
push(a,1,2,3);
```

事实上， `items`是一个数组，所以我们用数组的类型来定义它：

```ts
function push(array: any[],...items: any[]){
    items.forEach(function(item){
        array.push(item);
    });
}
let a = [];
push(a,1,2,3);
```

注意，rest 参数只能是最后一个参数，关于 rest 参数，可以参考 [ES6 中的 rest 参数](http://es6.ruanyifeng.com/#docs/function#rest%E5%8F%82%E6%95%B0)。

#### **重载**

重载允许一个函数接受不同的数量或者类型的参数，做不同的处理

比如，我们需要实现一个函数 `reverse`，输入数字 `123` 的时候，输出反转的数字 `321`，输入字符串 `'hello'` 的时候，输出反转的字符串 `'olleh'`。

利用联合类型，我们可以这么实现：

```ts
function reverse(x: number| string): number| string{
    if(typeof x === 'number'){
        return Number(x.toString().split('').reverse().join(''))
    }else if(typeof x === 'string'){
        return x.split('').reverse().join('');
    }
}
```

然而这样有一个缺点，就是不能够精确的表达，输入为数字的时候，输出也应该为数字，输入为字符串的时候，输出也应该为字符串。

这时，我们可以使用重载定义多个 `reverse` 的函数类型：

```ts
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string) : number | string{
    if(typeof x === 'number'){
        return Number(x.toString().split('').reverse().join(''));
    }else if(typeof x === 'string'){
        return x.split('').reverse().join('');
    }
}
```

 上例中吗，我们重复了多次函数 `reverse`的定义，前几次都是函数的定义，最后一次是函数的实现。

注意，TypeScript 会优先从最前面的函数定义开始匹配，所以多个函数如果有包含关系，需优先把精确的定义写在前面。

### 类型断言

类型断言（Type Assertion） 可以用来手动指定一个值的类型

#### **语法**

```
<类型> 值
```

或

```
值 as 类型
```

在 tsx 语法中必须使用后一种

#### **例子：将一个联合类型的变量指定为一个更加具体的类型**

当TypeScript 不确定一个联合类型的变量到底是那个类型的的时候，我们只能访问次联合类型的所有类型里共有的属性或方法:

```ts
function getLength(something: string|number) :number{
    return something.length;
}
// rror TS2339: Property 'length' does not exist on type 'string | number'.
// Property 'length' does not exist on type 'number'.
```

而有时候，我们确实需要在还不确定类型的时候就访问一个类型的属性或者是方法：比如：

```ts
function getLength(something: string|number) :number{
    if(something.length){
        return something.length;
    }else{
        return something.toString().length;
    }       
}
// 还是报上面的错，不过报错的位置是两处
```

上例中，获取 `something.length`的时候会报错

此时可以使用类型断言，将 `something`断言成 `string`

```ts
function getLength(something: string|number) :number{
    if((<string>something).length){
        return (<string>something).length;
    }else{
        return something.toString().length;
    }
        
}
```

类型断言的用法如上，在需要断言的变量上加上 `<type>`即可。

**类型断言不是类型转换，断言成一个联合类型中不存在的类型是不允许的**

```ts
function toBoolean(something: string | number): boolean {
    return <boolean>something;
}
// Conversion of type 'string | number' to type 'boolean' may be a mistake because neither type sufficiently overlaps with the other. If this was intentional, convert the expression to 'unknown' first.Type 'number' is not comparable to type 'boolean'.
```

### 声明文件

当使用第三方库时，我们需要引用它的声明文件

**声明语句**

例如是 jQuerty ，我们通常这样获取一个 `id`是 `foo`的元素：

```ts
$('#foo');
// or
jQuery('#foo');
```

但是在 TypeScript 中，我们并不知道 `$`或 `jQuery`是什么东西：

```ts
jQuery('#foo');
// Cannot find name 'jQuery'.
```

这时，我们需要使用 `declare`关键字来定义它的类型，帮助 TypeScript 判断我们传入的参数类型对不对：

```ts
declare var jQuery: (selector: string) => any;
jQuery('#foo');
```

`declare` 定义的类型只会用于编译时的检查，编译结果中会被删除。

上例的编译结果是：

```ts
jQuery('#foo');
```

**声明文件**

通常我们会在类型声明放到一个单独的文件中，这就是声明文件：

```ts
// jQuery.d.ts
declare var jQuery:(string) => any;
```

**我们约定声明文件是以 `.d.ts`为后缀**

然后在使用的文件开头，用「三斜线指令」表示引用了声明文件：

```ts
/// <reference path='./jQuery.d.ts'/>

jQuery('#foo');
```

#### **第三方声明文件**

当然，jQuery 的声明文件不需要我们定义了，已经有人帮我们定义好了：[jQuery in DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/jquery/index.d.ts)。

我们可以直接下载下来使用，但是更推荐的是使用工具统一管理第三方库的声明文件。

社区已经有多种方式引入声明文件，不过 [TypeScript 2.0 推荐使用 @types 来管理](https://blogs.msdn.microsoft.com/typescript/2016/06/15/the-future-of-declaration-files/)。

@types 的使用方式很简单，直接用 npm 安装对应的声明模块即可，以 jQuery 举例：

```shell
npm i @types/jquery --S -D
```

可以在[这个页面](http://microsoft.github.io/TypeSearch/)搜索你需要的声明文件。

### 内置对象

JavaScript 中有很多[内置对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects)，它们可以直接在 TypeScript 中当做定义好了的类型。

内置对象是指根据标准在全局作用域（Global）上存在的对象。这里的标准是指 ECMAScript 和其他环境（比如 DOM）的标准。

#### **ECMAScript 的内置对象**

ECMAScript 标准提供的内置对象有：

`Boolean`、`Error`、`Date`、`RegExp`等。

我们可以在 TypeScript 中将变量定义为这些类型：

```ts
let b : Boolean = new Boolean(1);
let e : Error = new Error('Error occurred');
let d : Date = new Date();
let r : RegExp = /[a-z]/;
```

更多的内置对象，可以查看 [MDN 的文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects)。

而他们的定义文件，则在 [TypeScript 核心库的定义文件](https://github.com/Microsoft/TypeScript/tree/master/src/lib)中。

**DOM 与 BOM 的内置对象**

DOM 和 BOM 提供的内置对象：

`Document`、`HTMLElement`、`Event`、`NodeList`等。

TypeScript 中会经常使用到这些类型：

```ts
let body: HTMLElement = document.body;
let allDiv: NodeList = document.querySelectorAll('div');
document.addEventListener('click',function(e:MouseEvent){
    // do something
});
```

它们的定义文件同样在 [TypeScript 核心库的定义文件](https://github.com/Microsoft/TypeScript/tree/master/src/lib)中。

#### **TypeScript 核心库的定义文件**

[TypeScript 核心库的定义文件](https://github.com/Microsoft/TypeScript/tree/master/src/lib)中定义了所有浏览器环境需要用到的类型，并且是预置在 TypeScript 中的。

当你在使用一些常用的方法的时候，TypeScript 实际上已经帮你做了很多类型判断的工作了，比如：

```ts
Math.pow(10,'2');
// error TS2345: Argument of type '"2"' is not assignable to parameter of type 'number'.
```

上面的例子中，`Math.pow` 必须接受两个 `number` 类型的参数。事实上 `Math.pow` 的类型定义如下：

```ts
interface Math {
    /**
     * Returns the value of a base expression taken to a specified power.
     * @param x The base value of the expression.
     * @param y The exponent value of the expression.
     */
    pow(x: number, y: number): number;
}
```

再举一个 DOM 中的例子：

```ts
document.addEventListener('click',function(e:MouseEvent){
    console.log(e.targetCurrent);
});
// error TS2339: Property 'targetCurrent' does not exist on type 'MouseEvent'.
```

上面的例子中，`addEventListener` 方法是在 TypeScript 核心库中定义的：

```ts
interface Document extends Node, GlobalEventHandlers, NodeSelector, DocumentEvent {
    addEventListener(type: string, listener: (ev: MouseEvent) => any, useCapture?: boolean): void;
}
```

所以 `e` 被推断成了 `MouseEvent`，而 `MouseEvent` 是没有 `targetCurrent` 属性的，所以报错了。注意，TypeScript 核心库的定义中不包含 Node.js 部分。

#### **用 TypeScript 写 Node.js**

Node.js 不是内置对象一部分，如果想用 TypeScript 写 Node.js，则需要引入第三方声明文件：

```shell
npm i @types/node -S -D
```



## 进阶

本部分介绍一些高级的类型与技术，具体内容包括：

- 类型别名
- 字符串字面量类型
- 元组
- 枚举
- 类
- 类与接口
- 泛型
- 声明合并



### 类型别名

类型别名可以用个一个类型起新名字

**简答的例子**

```ts
type Name = string;
type NameResolver = ()=> string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name{
    if(typeof n === 'string'){
        return n;
    }else {
        return n();
    }
}
```

上例中，我们使用 `type`创建类型别名。

类型别名常用于联合类型。

### 字符串字面量类型

字符串字面量类型用来约束取值只能是某几个字符串中的一个

#### **简单的例子**

```ts
type EventNames = 'click' | 'scroll' | 'mouseover';
function handleEvent(ele:Element,event:EventNames){
    // do something
}
handleEvent(document.getElementById('hello'),'scroll');
handleEvent(document.getElementById('world'),'dbclick');
// Argument of type '"dbclick"' is not assignable to parameter of type 'EventNames'.
```

上例中，我们使用 `type`定了字符串字面量类型 `EventNames`,它只能取三种字符串中的一种

注意，**类型别名与字符串字面量类型都是使用 `type`进行定义**

### 元组

数组合并了相同类型的对象，而元组（Tuple）合并了 不同类型的对象

元组起源于函数编程语言（如 F#），在这些语言中频繁地使用元组。

#### **简单的例子**

定义一值分别为 `string` 和 `number`的元组：

```ts
let lbh : [string, number] = ['lbh',23];
```

当赋值或访问一个已知索引的元素时，会得到正确的类型：

```ts
let lbh : [string, number];
lbh[0] = 'l bh'
lbh[1] = 25;
lbh[0].slice(1);
lbh[1].toFixed(1);
```

也可以只赋值其中一项：

```ts
let lbh : [string, number];
lbh[0] = 'l bh'
```

但是当直接对元组类型的变量进行初始化或者赋值的时候，需要提供所有元组类型中指定的项：

```ts
let lbh: [string,number] = ['l bh'];
// error TS2322: Type '[string]' is not assignable to type '[string, number]'.Property '1' is missing in type '[string]'.
```

```ts
let lbh: [string,number];
lbh = ['l bh'];
xcatliu[1] = 25;
// TS2322: Type '[string]' is not assignable to type '[string, number]'.Property '1' is missing in type '[string]'.
```

#### **越界的元素**

当添加越界的元素时，它的类型会被限制为元组中的每个类型的联合类型：

```ts
let lbh: [string,number];
lbh = ['lbh',23];
lbh.push('http://laibh.top');
lbh.push(true);
// error TS2345: Argument of type 'true' is not assignable to parameter of type 'string | number'.
```

### 枚举

枚举（Enum）类型用于取值被限定在一定的范围内的场景，比如一周只能有七天，颜色限定为红蓝绿等。

#### **简单的例子**

枚举使用 `enum`关键字来定义：

```ts
enum Days{Sun,Mon,Tue,Web,Thu,Fri,Sat};
```

编译后：

```ts
var Days;
(function (Days) {
    Days[Days["Sun"] = 0] = "Sun";
    Days[Days["Mon"] = 1] = "Mon";
    Days[Days["Tue"] = 2] = "Tue";
    Days[Days["Web"] = 3] = "Web";
    Days[Days["Thu"] = 4] = "Thu";
    Days[Days["Fri"] = 5] = "Fri";
    Days[Days["Sat"] = 6] = "Sat";
})(Days || (Days = {}));
```

枚举对象会被赋值为从 `0`开始递增的数字，同时也会被枚举值到枚举名进行反向映射:

```ts
enum Days {Sun, Mon, Tue, Wed, Thu, Fri, Sat};

console.log(Days["Sun"] === 0); // true
console.log(Days["Mon"] === 1); // true
console.log(Days["Tue"] === 2); // true
console.log(Days["Sat"] === 6); // true

console.log(Days[0] === "Sun"); // true
console.log(Days[1] === "Mon"); // true
console.log(Days[2] === "Tue"); // true
console.log(Days[6] === "Sat"); // true
```

**手动赋值**

我们也可以给枚举项手动赋值：

```ts
enum Days {Sun = 7, Mon = 1, Tue, Wed, Thu, Fri, Sat};

console.log(Days["Sun"] === 7); // true
console.log(Days["Mon"] === 1); // true
console.log(Days["Tue"] === 2); // true
console.log(Days["Sat"] === 6); // true
```

上面例子中，未手动赋值的枚举项会接着上一个枚举递增。

如果未手动赋值的枚举项与手动赋值的重复了，TypeScript 是不会察觉到这一点的：

```ts
enum Days {Sun = 3, Mon = 1, Tue, Wed, Thu, Fri, Sat};

console.log(Days["Sun"] === 3); // true
console.log(Days["Wed"] === 3); // true
console.log(Days[3] === "Sun"); // false
console.log(Days[3] === "Wed"); // true
```

上面的例子中，递增到 `3` 的时候与前面的 `Sun` 的取值重复了，但是 TypeScript 并没有报错，导致 `Days[3]` 的值先是 `"Sun"`，而后又被 `"Wed"` 覆盖了。编译的结果是：

```tsx
var Days;
(function (Days) {
    Days[Days["Sun"] = 3] = "Sun";
    Days[Days["Mon"] = 1] = "Mon";
    Days[Days["Tue"] = 2] = "Tue";
    Days[Days["Wed"] = 3] = "Wed";
    Days[Days["Thu"] = 4] = "Thu";
    Days[Days["Fri"] = 5] = "Fri";
    Days[Days["Sat"] = 6] = "Sat";
})(Days || (Days = {}));

```

所以使用的时候需要注意，最好不要出现这种覆盖的情况。

手动赋值的枚举项可以不是数字，此时需要使用类型断言来让tsc无视类型检查 (编译出的js仍然是可用的)：

```ts
enum Days {Sun = 7, Mon, Tue, Wed, Thu, Fri, Sat = <any>"S"};
```

```ts
var Days;
(function (Days) {
    Days[Days["Sun"] = 7] = "Sun";
    Days[Days["Mon"] = 8] = "Mon";
    Days[Days["Tue"] = 9] = "Tue";
    Days[Days["Wed"] = 10] = "Wed";
    Days[Days["Thu"] = 11] = "Thu";
    Days[Days["Fri"] = 12] = "Fri";
    Days[Days["Sat"] = "S"] = "Sat";
})(Days || (Days = {}));

```

当然，手动赋值的枚举项也可以为小数或者是负数，此时后续未手动赋值的低增长仍为 `1`:

```ts
enum Days {Sun = 7,Mon = 1.5,Tue, Wed, Thu, Fri, Sat}
```

```ts
var Days;
(function (Days) {
    Days[Days["Sun"] = 7] = "Sun";
    Days[Days["Mon"] = 1.5] = "Mon";
    Days[Days["Tue"] = 2.5] = "Tue";
    Days[Days["Wed"] = 3.5] = "Wed";
    Days[Days["Thu"] = 4.5] = "Thu";
    Days[Days["Fri"] = 5.5] = "Fri";
    Days[Days["Sat"] = 6.5] = "Sat";
})(Days || (Days = {}));
```

**常数项和计算所得项**

枚举项有两种类型：常数项（constant member）和 （computed member）

前面我们所举的例子都是常数项，一个典型的计算所得项的例子：

```ts
enum Color { Red, Green, Blue = 'blue'.length}
```

上面的例子中，`"blue".length` 就是一个计算所得项。

上面的例子不会报错，但是**如果紧接着在计算所得项后面是未手动赋值的项，那么它就会因为无法取得初始值而报错：**

```ts
enum Color { Blue = 'blue'.length,Red, Green }
//  error TS1061: Enum member must have initializer.
```

下面是常数项和计算所得项的完整定义，部分引用自[中文手册 - 枚举](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/Enums.html)：

当满足以下条件时，枚举成员被当作是常数：

- 不具有初始化函数并且之前的枚举成员是常数。在这种情况下，当前枚举成员的值为上一个枚举成员的值加 `1`。但第一个枚举元素是个例外。如果它没有初始化方法，那么它的初始值为 `0`。
- 枚举成员使用常数枚举表示式初始化。常数枚举表达式是 TypeScript 表达式的子集，它可以在编译阶段求值。当一个表示式满足下面的之一时，它就是一个常数枚举表达式：
  - 数字字面量
  - 应用之前定义的常数枚举成员（可以在不同的枚举类型中定义的）如果这个成员是在同一枚举类型中定义的，可以使用非限定名来引用
  - 带括号的常数枚举表达式
  - `+`, `-`, `~` 一元运算符应用于常数枚举表达式
  - `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `>>>`, `&`, `|`, `^` 二元运算符，常数枚举表达式做为其一个操作对象。若常数枚举表达式求值后为NaN或Infinity，则会在编译阶段报错

所有其它情况的枚举成员被当作是需要计算得出的值。

#### **常数枚举**

常数枚举是使用 `const enum`定义的枚举类型：

```ts
const enum Directions {
    Up,
    Down,
    Left,
    Right
}

```

常数枚举与普通枚举的区别是，它会在编译阶段被删除，并且不能删除计算成员

上面编译的结果是：

```ts
var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
```

  假如包含了计算成员，则会在编译阶段报错：

```ts
const enum Color { Red, Green, Blue = 'blue'.length };
// error TS2474: In 'const' enum declarations member initializer must be constant expression.
```

#### **外部枚举**

外部枚举（Ambient Enums）是使用 `declare enum`定义的枚举类型：

```ts
declare enum Directions {
    Up,
    Down,
    Left,
    Right
}
let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```

`declare`定义的类型只会用于编译时的检查，编译结果中会被删除

```ts
var directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```

外部枚举与声明语句一样，常出现在声明文件中。

同时使用 `declare`和 `const`也是可以的：

```ts
declare const enum Directions {
    Up,
    Down,
    Left,
    Right
}
let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```

编译结果：

```ts
var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
```

TypeScript 的枚举类型的概念[来源于 C#](https://msdn.microsoft.com/zh-cn/library/sbbt4032.aspx)。

### 类

传统的方法中，JavaScript 通过构造函数实现类的概念，通过原型链实现继承。

ypeScript 除了实现了所有 ES6 中的类的功能以外，还添加了一些新的用法。

这一节主要介绍类的用法，下一节再介绍如何定义类的类型。

#### **类的概念**

- 类（Class）:定义一件事物的抽象特点，包含它的属性和方法
- 对象（Object）:类的实例，通过 `new`生成
- 面向对象（OOP）的三大特性：封装、继承、多态
- 封装（Encapsulation）:将对数据的操作细节隐藏起来，只暴露对外的接口。外界调用端不需要知道细节，就能通过对外提供的接口来访问该对象，同时也保证了外界无法任意更改对象内部的数据。
- 继承（Inheritance）:子类继承父类，子类除了拥有父类的所有特征外，还有一些更具体的特性
- 多态（Polymorphism）:由继承而产生了相关的不同的类，对同一个方法可以有不同的响应。比如 `cat`和 `Dog`都继承自 `Animal`，但是分别实现了自己的 `eat` 方法。此时针对某一个实例，我们无需了解它是 `Cat` 还是 `Dog`，就可以直接调用 `eat` 方法，程序会自动判断出来应该如何执行 `eat`
- 存取器（getter&setter）:用以改变属性的读取和赋值行为
- 修饰符（Modifiers）:修饰符是一些关键字，用于限定成员或类型的性质。比如 `public`表示公共属性或方法
- 抽象类（Abstraces）:抽象类是供其他类继承的基类，抽象类不允许被实例化。抽象类中的抽象方法必须在子类中被实现
- 接口（Interfaces）:不同类之间共有的属性或方法，可以抽象成一个接口。接口可以被实现（implements）。一个类只能继承自另一个类，但是可以实现多个接口。

#### **ES6中类的用法**

下面我们先回顾一下 ES6 中类的用法，更详细的介绍可以参考 [ECMAScript 6 入门 - Class](http://es6.ruanyifeng.com/#docs/class)。

##### **属性和方法**

使用 `class`定义类，使用 `constructor`定义构造函数。

通过 `new`生成新的实例的时候，会自动调用构造函数。

```ts
class Animal {
    constructor(name) {
        this.name = name;
    }
    sayHi() {
        return `My name is ${this.name}`;
    }
}

let a = new Animal('Jack');
console.log(a.sayHi()); // My name is Jack
```

##### **类的继承**

使用 `extends`关键字实现继承，子类中使用 `super`关键字来调用父类的构造函数和方法

```ts
class Cat extends Animal {
    constructor(name){
        super(name); // 调用父类的 constructor(name)
        console.log(this.name);
    }
    sayHi(){
        return `Meow,`+super.sayHi(); // 调用父类的 sayHi()
    }
}

let c = new Cat('Tom');
console.log(c.sayHi()); // Meow,My name is Tom
```

##### **存储器**

使用 getter 和 setter 可以改变属性的赋值和读取行为：

```ts
class Animal {
    constructor(name){
        this.name = name;
    }
    sayHi(){
        return `My name is ${this.name}`
    }
    get name(){
        return this.name;
    }
    set name(value){
        console.log('setter: '+ value);
    }
}
let a = new Animal('Kitty'); // setter: Kitty
a.name = 'Tom'; // setter: Tom
console.log(a.name); // Jack
```

##### **静态方法**

使用  `static`修饰符修饰的方法被称为静态方法，他们不需要实例化，而是直接通过类来调用：

```ts
class Animal {
    static isAnimal(a) {
        return a instanceof Animal;
    }
}

let a = new Animal('Jack');
Animal.isAnimal(a); // true
a.isAnimal(a); // TypeError: a.isAnimal is not a function
```

#### **ES7中类的用法**

ES7中有一些关于类的提案，TypeScript 也实现了它们。

##### **实例属性**

ES6中实例的属性只能通过构造函数中的 `this.xx`来定义，ES7提案可以直接在类里面定义：

```ts
class Animal{
    name = 'Jack';
    constructor(){
        // ...
    }
}
let a = new Animal();
console.log(a.name); // Jack
```

##### **静态属性**

ES7提案中，可以使用 `static`定义一个静态属性

```ts
class Animal {
    static num = 42;
    constructor(){
        //...
    }
}
console.log(Animal.num); // 42
```

#### **TypeScript 中 类的用法**

##### **public private 和 protected**

TypeScripy 可以使用三种访问修饰符（Access Modifiers），分别是 `public`、`private`和 `protected`。

- `public`修饰的属性或方法是共有的，可以在任何地方被访问到，默认所有的属性和方法都是 `public`
- `private`修饰的属性和方法都是私有的，不能在声明它的类的外部访问
- `protected`修饰的属性和方法是受保护的，它和 `private`类似，区别是它在子列中也是允许被访问的。

例子：

```ts
class Animal{
    public name;
    public constructor(name){
        this.name = name;
    }
}

let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom';
console.log(a.name); // Tom
```

上面的例子中， `name`被设置为 `public`，所以直接访问实例的 `name`属性是被允许的。很多时候，我们希望的属性是无法直接存取的，这时候就可以用 `private`了：

```ts
class Animal {
    private name;
    public constructor(name){
        this.name = name;
    }
}
let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom';

// index.ts(9,13): error TS2341: Property 'name' is private and only accessible within class 'Animal'.
// index.ts(10,1): error TS2341: Property 'name' is private and only accessible within class 'Animal'.
```

需要注意的是，TypeScirpt 编译之后的代码中，并没有限制 `private`属性在外部的可访问性；

上面代码的编译结果是：

```ts
var Animal = (function () {
    function Animal(name) {
        this.name = name;
    }
    return Animal;
}());
var a = new Animal('Jack');
console.log(a.name);
a.name = 'Tom';
```

使用 `private`修饰的属性或方法，在子类中不允许访问的：

```ts
class Animal {
    private name;
    public constructor(name){
        this.name = name;
    }
}
class Cat extends Animal {
    constructor(name){
        super(name);
        console.log(this.name);
    }
}
// error TS2341: Property 'name' is private and only accessible within class 'Animal'.
```

而如果是用 `protected`修饰，则运行在子类中访问：

```ts
class Animal {
    protected name;
    public constructor(name){
        this.name = name;
    }
}

class Cat extends Animal {
    constructor(name){
        super(name);
        console.log(this.name);
    }
}
```

##### **抽象类**

`abstract`用于定义抽象类和其中的抽象方法。

什么是抽象类？

首先，抽象类是不允许被实例化的:

```ts
abstract class Animal {
    public name;
    public constructor(name){
        this.name = name;
    }
    public abstract sayHi();
}

let a = new Animal('jack');
// error TS2511: Cannot create an instance of an abstract class.
```

上面的例子中，我们定义了一个抽象类 `Animal`，并且定义了一个抽象的方法 `sayHi`。在实例化类的时候报错了。

其次，抽象类中的抽象方法必须被子类实现：

```ts
abstract class Animal {
    public name;
    public constructor(name){
        this.name = name;
    }
    public abstract sayHi();
}

class Cat extends Animal {
    public eat(){
        console.log(`${this.name} is eating`);
    }
}

let cat = new Cat('Tom');
// error TS2515: Non-abstract class 'Cat' does not implement inherited abstract member 'sayHi' from class 'Animal'.
```

上面的例子中，我们定义了一个类 `cat`继承了抽象类 `Animal`，但是没有实现抽象方法 `sayHi`，所以编译报错了。

下面是一个正确使用抽象类的例子：

```ts
abstract class Animal {
    public name;
    public constructor(name){
        this.name = name;
    }
    public abstract sayHi();
}

class Cat extends Animal {
    public sayHi(){
        console.log(`My name is ${this.name}`);
    }
    public eat(){
        console.log(`${this.name} is eating`);
    }
}

let cat = new Cat('Tom');

```

上面的例子中，我们实现了抽象方法 `sayHi`，编译通过了。

需要注意的是，即便是抽象方法，TypeScript 的编译结果中，仍然会有这个类，上面的代码的编译结果是：

```ts
var __extends = (this && this.__extends) || (function () {
    var extendStatics = function (d, b) {
        extendStatics = Object.setPrototypeOf ||
            ({ __proto__: [] } instanceof Array && function (d, b) { d.__proto__ = b; }) ||
            function (d, b) { for (var p in b) if (b.hasOwnProperty(p)) d[p] = b[p]; };
        return extendStatics(d, b);
    }
    return function (d, b) {
        extendStatics(d, b);
        function __() { this.constructor = d; }
        d.prototype = b === null ? Object.create(b) : (__.prototype = b.prototype, new __());
    };
})();
var Animal = /** @class */ (function () {
    function Animal(name) {
        this.name = name;
    }
    return Animal;
}());
var Cat = /** @class */ (function (_super) {
    __extends(Cat, _super);
    function Cat() {
        return _super !== null && _super.apply(this, arguments) || this;
    }
    Cat.prototype.sayHi = function () {
        console.log("My name is " + this.name);
    };
    Cat.prototype.eat = function () {
        console.log(this.name + " is eating");
    };
    return Cat;
}(Animal));
var cat = new Cat('Tom');

```

#### **类的类型**

给 类加上 TypeScript 的类型很简单，与接口相似：

```ts
class Animal {
    name: string;
    constructor(name: string){
        this.name = name;
    }
    sayHi():string{
        return `My name is ${this.name}`
    }
}
let a:string Animal = new Animal('Jack');
console.log(a.sayHi()); // My name is Jack
```

### 类与接口

接口（Interfaces）可以用于对「对象的形状（Shape）」进行描述。

这一章主要介绍接口的另一个用途，对类一部分行为进行抽象。

#### **类实现接口**

实现（implements）是面向对象中的一个重要的概念。一般来讲，一个类继承自另一个类，有时候不同类之间可以有一些共有的特性，这时候就可以把特性提取成接口（interfaces），用 `implements`关键字来实现，这个特性大大提高了面向对象的灵活性。

举例来说，门是一个类，防盗门是门的子类。如果防盗门有个报警器的功能，我们可以简单的给防盗门加一个报警方法。这时候如果有另外一个类，车，也有报警的功能，就可以考虑把报警器提取出来，作为一个接口，防盗门和车都去实现它:

```ts
interface Alarm {
    alert();
}
class Door{}
class SecurityDoor extends Door implements Alarm {
    alert(){
        console.log('SecurityDoor alert');
    }
}

class Car implements Alarm {
    alert(){
        console.log('car alert');
    }
}
```

一个类可以实现多个接口：

```ts
interface Alarm {
    alert();
}
interface Light {
    lightOn();
    lightOff();
}
class Door{}
class SecurityDoor extends Door implements Alarm {
    alert(){
        console.log('SecurityDoor alert');
    }
}

class Car implements Alarm,Light {
    alert(){
        console.log('car alert');
    }
    lightOff(){
        console.log('Car light off');
    }
    lightOn(){
        console.log('Car lightOn');
    }
}
```

上例中，`Car`实现了 `Alarm`和 `Light`接口，即能报警，也能开关车灯。

#### **接口继承接口**

接口与接口之间也是可以继承的：

```ts
interface Alarm {
    alert();
}
interface LightableAlarm extends Alarm {
    lightOn();
    lightOff();
}
```

上例中，我们使用 `extends`使 `LightableAlarm`继承 `Alarm`

#### **接口继承类**

接口也可以继承类：

```ts
class Point {
    x: number;
    y: number;
}
interface Point3d extends Point {
    z: number;
}

let point3d: Point3d = {x:1, y:2, z:3}
```

#### **混合类型**

可以使用接口的方式来顶一个函数需要符合的形状：

```ts
interface SearchFunc {
    (source: string, subString: string) : boolean;
}
let mySearchFunc:SearchFunc;
mySearchFunc = function(source: string, subString: string) : boolean{
    return source.search(subString) !== -1;
}
```

有时候，一个函数还可以有自己的属性和方法：

```ts
interface Couter {
    (start: number): string;
    interval :number;
    reset(): void;
}

function getCounter() :Couter {
    let counter = <Couter>function (start: number){}
    counter.interval = 123;
    counter.reset = function(){}
    return counter;
}

let c = getCounter();
c(10);
c.reset();
c.interval = 0.5;
```

### 泛型

泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。

#### **简单的例子**

首先，我们实现一个函数 `createArray`，它可以创建一个指定长度的数组，同时将每一项都填充一个默认值：

```ts
function createArray(length: number, value: any):Array<any>{
    let result = [];
    for(let i = 0; i < length; i++){
        result[i] = value;
    }
    return result;
}
createArray(3,'x');
```

上例中，我们使用了之前提到过的数组泛型来定义返回值的类型。

这段代码的编译不会报错，但是一个显而易见的错误是，它并没有准确的定义返回值的类型：

`Array<any>`运行数组的每一项都为任意类型。但是我们预期的是，数组中每一项都应该是输入 `value`的类型；

这时候，泛型就派上用处了：

```ts
function createArray<T>(length: number,value:T):Array<T>{
    let result = [];
    for(let i = 0; i < length; i++){
        result[i] = value;
    }
    return result;
}
createArray<string>(3, 'x');// ['x', 'x', 'x']
```

上例中，我们在函数后面添加了 `<T>`,其中 `T`用来指代任意输入的类型，在后面的输入 `value：T`和 输出的 `Array<T>`中即可使用了。

接着在调用到时候，可以指定它具体的类型为 `string`,当然，也可以不手动指定，而然类型自动推算出来：

```ts
function createArray<T>(length: number,value:T):Array<T>{
    let result = [];
    for(let i = 0; i < length; i++){
        result[i] = value;
    }
    return result;
}
createArray(3, 'x');// ['x', 'x', 'x']
```

#### **多个类型参数**

定义泛型的时候，可以一次定义多个类型参数：

```ts
function swap<T,U>(tuple:[T,U]):[U,T]{
    return [tuple[1],tuple[0]];    
}
swap([7,'seven']); // ['seven',7]

```

上面我们定义了一个 `swap`函数用来交换输入的元组。

#### **泛型约束**

在函数内部使用泛型变量的时候，由于事先不知道它是哪种类型，所以不能随意的操作它的属性或方法：

```tsx
function loggingIdentity<T>(arg:T):T{
    console.log(arg.length);
    return arg;
}
//  error TS2339: Property 'length' does not exist on type 'T'.
```

上例中，泛型 `T`不一定包含属性 `length`，所以编译的时候报错了。

这时，我们可以对泛型进行约束，只允许这个函数传入那些包含 `length`属性的变量。这就是泛型约束：

```ts
interface Lengthwise {
    length: number;
}
function loggingIdentity<T extends Lengthwise>(arg:T):T{
    console.log(arg.length);
    return arg;
}
```

上例中，我们使用 `extends`约束了泛型 `T`必须符合接口 `Lengthwise`的形状，也就是必须包含 `length`属性。

此时如果调用了 `loggingIdentity`的时候，出入的 `arg`不包含 `length`，那么在编译阶段就会报错了：

```ts
interface Lengthwise {
    length: number;
}
function loggingIdentity<T extends Lengthwise>(arg:T):T{
    console.log(arg.length);
    return arg;
}

loggingIdentity(7);
// error TS2345: Argument of type '7' is not assignable to parameter of type 'Lengthwise'.
```

多个类型参数之间也可以互相约束：

```ts
function copyFields<T extends U,U>(target: T, source: U):T{
    for(let id in source){
        target[id] = (<T>source)[id];
    }
    return target;
}

let x = { a: 1, b: 2, c: 3,d: 4 };
copyFields(x,{b:10, d:20}); // {a: 1, b: 10, c: 3, d: 20}
```

上例中，我们使用了两个类型参数，其中要求 `T` 继承 `U`，这样就保证了 `U` 上不会出现 `T` 中不存在的字段。

#### **泛型接口**

可以使用接口的方式来定义一个函数需要符合的形状：

```ts
interface SearchFunc {
    (source: string, subString: string): boolean;
  }
  
  let mySearch: SearchFunc;
  mySearch = function(source: string, subString: string) {
      return source.search(subString) !== -1;
  }
```

当然也可以使用含有泛型的接口来定义函数的形状：

```ts
interface CreateArrayFunc {
    <T>(length: number, value: T): Array<T>;
}
let createArray: CreateArrayFunc;
createArray = function<T>(length: number, value: T): Array<T>{
    let result: T[]  = [];
    for(let i = 0; i < length; i++){
        result[i] = value;
    }
    return result;
}

createArray(3, 'x');
```

进一步，我们可以把泛型参数提前到接口名上：

```ts
interface CreateArrayFunc<T> {
    (length: number, value: T): Array<T>;
}
let createArray: CreateArrayFunc<any>;
createArray = function<T>(length: number, value: T): Array<T>{
    let result: T[]  = [];
    for(let i = 0; i < length; i++){
        result[i] = value;
    }
    return result;
}

createArray(3, 'x');
```

注意，此时在使用泛型接口的时候，需要定义泛型的类型。

#### **泛型类**

与泛型接口类似，泛型也可以用于类的类型定义中：

```ts
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x,y){return x + y }
```

#### **泛型参数的默认类型**

在 TypeScript 2.3 以后，我们可以为泛型中的类型参数指定默认类型。当使用泛型时没有在代码中直接指定类型参数，从实际值参数中也无法推测出时，这个默认类型就会起作用。

```ts
function createArray<T = string>(length: number, value: T){
    let result: T[] = [];
    for(let i = 0; i < length; i++){
        result[i] = value;
    }
    return result;
}

```

### 声名合并

如果定义了两个相同名字的函数、接口或类，那么它们会合并成一个类型：

#### **函数的合并**

我们可以使用重载定义多个函数类型：

```ts
function reverse(x:number):number;
function reverse(x:string):string;
function reverse(x:number|string):number|string{
    if(typeof x === 'number'){
        return Number(x.toString().split('').reverse().join(''));
    }else if(typeof x === 'string'){
        return x.split('').reverse().join('');
    }
}
```

#### **接口的合并**

接口中的属性在合并时会简单的合并到一个接口中：

```ts
interface Alarm {
    price: number;
}
interface Alarm {
    weight: number;
}
```

相当于：

```ts
interface Alarm {
    price: number;
    weight: number;
}
```

注意，**合并的属性的类型必须是唯一的**

```ts
interface Alarm {
    price: number;
}
interface Alarm {
    price: number; // 虽然重复了，但是类型都是 'string',不会报错
    weight: number;
}
```

```ts
interface Alarm {
    price: number;
}
interface Alarm {
    price: string; // 类型不一致，报错
    weight: number;
}
// error TS2717: Subsequent property declarations must have the same type.  Property 'price' must be of type 'number', but here has type 'string'.
```

接口中方法和合并，与函数的合并一样。

```ts
interface Alarm {
    price: number;
    alert(s: string): string;
}
interface Alarm {
    weight: number;
    alert(s: string, n: number): string;
}
```

相当于

```ts
interface Alarm {
    price: number;
    weight: number;
    alert(s: string): string;
    alert(s: string, n: number): string;
}
```

#### **类的合并**

类的和并与接口的合并规则一致

#### **代码检查**

 [点击这里](https://ts.xcatliu.com/engineering/lint.html)