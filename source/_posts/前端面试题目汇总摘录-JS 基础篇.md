---
title: 前端面试题目汇总摘录（JS 基础篇）
date: 2019-01-14  09:30:54
tags: 前端面试题
categories: 
	- 前端面试


---



温故而知新，保持空杯心态

## **JS 基础**

### **JavaScript 的 typeof 返回那些数据类型**

object number function boolean undefined string

```javascript

typeof null; *// object*

typeof isNaN; *// function*

typeof isNaN(123); *//boolean*

typeof []; *// object*

Array.isArray(); *// false*

toString.call([]); *// [object Array]*

var arr = [];

arr.constructor; *// ƒ Array() { [native code] }*

```

### 强制类型转换和隐式类型转换？

#### 显示转换（强制类型转换）

js 提供了以下几种转型函数：

| 转换的类型 | 函数                                                   |
| ---------- | ------------------------------------------------------ |
| 数值类型   | Number(mix),parseInt(string,radix),parseFloat(string); |
| 字符串类型 | toString(radix),String(mix)                            |
| 布尔类型   | Boolean(mix)                                           |

##### Number（mix） 函数，可以将任意类型的参数 mix 转换为数值类型，规则为

1. 如果是布尔值，`true` 和 `false`分别被转换为 1 和 0
2. 如果是数字值，返回本身
3. 如果是 `null`,返回 0.  如果是 `undefined`，返回 `NaN`
4. 如果是字符串，遵循以下规则：

        1. 如果字符串中只包含数字，则将其转换为十进制（忽略前导0，前面正负号有效）如果字符串中包含有效的浮点格式，则将其转换为对应的浮点数值（忽略前导0，前面正负号有效）
        2. 如果字符串是空的（不包含任何字符），则将其转换为 0
        3. 如果字符串中包含有效的十六进制格式，则转换为相同大小的十进制整数值
        4. 如果字符串中包含上述格式之后的字符，则将其转换为 NaN
5. 如果是对象，则调用对象的 `valueOf()` 方法，然后按照前面的规则进行转换返回的值，如果是转换结果是 `NaN`,则调用对象的 `toString()` 方法，然后再一次按照前面的规则进行返回的字符串值的转换

下表是对象的 valueOf() 的返回值

| 对象     | 返回值                                                       |
| -------- | ------------------------------------------------------------ |
| Array    | 数组的元素被转换为字符串，这些字符串由逗号分隔，连接在一起。其操作与 Array.toString 和 Array.join 方法相同。 |
| Boolean  | Boolean 值。                                                 |
| Date     | 存储的时间是从 1970 年 1 月 1 日午夜开始计的毫秒数 UTC       |
| Function | 函数本身                                                     |
| Number   | 数字值                                                       |
| Object   | 对象本身。这是默认情况                                       |
| String   | 字符串值                                                     |

由于 Number（）函数在转换字符串时原理比较复杂，且不够合理，因此在处理字符串时，更常用的是 `parseInt()` 函数

##### parstInt(string,radix)
将字符串转换为整数类型的数值，其规则：

1. 忽略前面字符串前面的空格，直至找到第一个非空格字符
2. 如果第一个字符不是数字字符或者负号，就会返回 `NaN`（也就是遇到空字符会返回 NaN）
3. 如果第一个字符是数字字符，会继续解析第二个字符，知道解析完所有后续的字符或者是遇到一个非数字字符
4. 如果字符串中第一个字符是数字字符，也能够识别各种进制
5. 最好在 第二个参数指定转换的基数（进制），就不会有所歧义。

##### parseFloat(string)

将字符串转换为浮点数类型的数值,规则：

与parseInt()函数类似，parseFloat()也是从第一个字符(位置0)开始解析每个字符。而且也是一直解析到字符串末尾，或者解析到遇见一个无效的浮点数字字符为止。也就是说，字符串中的第一个小数点是有效的，而第二个小数点就是无效的了，因此它后面的字符串将被忽略。

##### toString(radix)

除 `undefined` 和 `null`之外的所有类型的值都具有 `toString()`方法，其作用是返回对象的字符串表示。

    多数情况下，调用toString()方法不必传递参数。但是，在调用数值的toString()方法时，可以传递一个参数：输出数值的基数。默认情况下，toString()方法以十进制格式返回数值的字符串表示。

| 对象     | 操作                                                         |
| -------- | ------------------------------------------------------------ |
| Array    | 将 Array 的元素转换为字符串。结果字符串由逗号分隔，且连接起来 |
| Boolean  | 如果 Boolean 值是 true，则返回 “true”。否则，返回 “false”    |
| Date     | 返回日期的文字表示法                                         |
| Error    | 返回一个包含相关错误信息的字符串                             |
| Function | 返回如下格式的字符串，其中 functionname 是被调用 toString 方法函数的名称：function functionname( ) { [native code]} |
| Number   | 返回数字的文字表示                                           |
| String   | 返回 String 对象的值                                         |
| 默认     | 返回 “[object objectname]”，其中 objectname 是对象类型的名称 |

在不知道要转换的值是不是null或undefined的情况下，还可以使用转型函数String()，这个函数能够将任何类型的值转换为字符串。

##### String(mix)

将任何类型的值转换为字符串，其规则为：

1. 如果有toString()方法，则调用该方法（不传递radix参数）并返回结果
2. 如果是null，返回”null”
3. 如果是undefined，返回”undefined”

##### Boolean(mix)

将任何类型的值转换为布尔值。

以下值会被转换为`false`：false、””、0、NaN、null、undefined，其余任何值都会被转换为`true`。

#### 隐式转换（非强制转换类型）

在某些情况下，即使我们不提供显示转换，Javascript也会进行自动类型转换，主要情况有：

##### isNaN(mix)

用于检测是否为非数值的函数.

经测试发现，该函数会尝试将参数值用 `Number()` 进行转换，如果结果为“非数值”则返回 `true`，否则返回 `false`。

##### 递增递减操作符（包括前置和后置）、一元正负符号操作符（经过对比发现，其规则与Number()规则基本相同）

1. 如果是包含有效数字字符的字符串，先将其转换为数字值（转换规则同 `Number()`），再执行加减1的操作，字符串变量变为数值变量。
2. 如果是不包含有效数字字符的字符串，将变量的值设置为 `NaN`，字符串变量变成数值变量。
3. 如果是布尔值 `false`，先将其转换为0再执行加减1的操作，布尔值变量编程数值变量。
4. 如果是布尔值 `true`，先将其转换为1再执行加减1的操作，布尔值变量变成数值变量。
5. 如果是浮点数值，执行加减1的操作。
6. 如果是对象，先调用对象的 `valueOf()` 方法，然后对该返回值应用前面的规则。如果结果是 `NaN`，则调用 `toString()` 方法后再应用前面的规则。对象变量变成数值变量。

##### 加法运算操作符

加号运算操作符在Javascript也用于字符串连接符，所以加号操作符的规则分两种情况：

如果两个操作值都是数值，其规则为：

1. 如果一个操作数为 `NaN`，则结果为 `NaN`
2. 如果是 `Infinity+Infinity`，结果是 `Infinity`
3. 如果是 `-Infinity+(-Infinity)`，结果是 `-Infinity`
4. 如果是 `Infinity+(-Infinity)`，结果是 `NaN`.
5. 如果是 `+0+(+0)`，结果为 `+0`
6. 如果是 `(-0)+(-0)`，结果为 `-0`.  
7. 如果是 `(+0)+(-0)`，结果为 `+0`

如果有一个操作值为字符串，则：

1. 如果两个操作值都是字符串，则将它们拼接起来 如果只有一个操作值为字符串，则将另外操作值转换为字符串，然后拼接起来2.  如果一个操作数是对象、数值或者布尔值，则调用toString()方法取得字符串值，然后再应用前面的字符串规则。
2. 对于undefined和null，分别调用String()显式转换为字符串。

可以看出，加法运算中，如果有一个操作值为字符串类型，则将另一个操作值转换为字符串，最后连接起来。

##### 乘除、减号运算符、取模运算符

这些操作符针对的是运算，所以他们具有共同性：如果操作值之一不是数值，则被隐式调用`Number()`函数进行转换。具体每一种运算的详细规则请参考ECMAScript中的定义。

##### 逻辑操作符（!、&amp;&amp;、||）

逻辑非（！）操作符首先通过Boolean()函数将它的操作值转换为布尔值，然后求反。

逻辑与（&amp;&amp;）操作符，如果一个操作值不是布尔值时，遵循以下规则进行转换：

1. 如果第一个操作值经 `Boolean()` 转换后为 `true`，则返回第二个操作值，否则返回第一个值（不是`Boolean()` 转换后的值）
2. 如果第一个操作值是对象，则返回第二个操作值
3. 如果两个操作值都是对象，则返回第二个操作值
4. 如果有一个操作值为 `null`，返回 `null`
5. 如果有一个操作值为 `NaN`，返回 `NaN`
6. 如果有一个操作值为 `undefined`，返回 `undefined`

逻辑或（||）操作符，如果一个操作值不是布尔值，遵循以下规则

1. 如果第一个操作值经 `Boolean()` 转换后为 `false`，则返回第二个操作值，否则返回第一个操作值（不是`Boolean()` 转换后的值）
2. 如果两个操作值都是对象，则返回第一个操作值
3. 如果两个操作值都为 `null`，返回 `null`
4. 如果两个操作值都为 `NaN`，返回 `NaN`
5. 如果两个操作值都为 `undefined`，返回 `undefined`

##### 关系操作符（&lt;, &gt;, &lt;=, &gt;=）

与上述操作符一样，关系操作符的操作值也可以是任意类型的，所以使用非数值类型参与比较时也需要系统进行隐式类型转换：

1. 如果两个操作值都是数值，则进行数值比较
2. 如果两个操作值都是字符串，则比较字符串对应的字符编码值
3. 如果只有一个操作值是数值，则将另一个操作值转换为数值，进行数值比较
4. 如果一个操作数是对象，则调用 `valueOf()` 方法（如果对象没有 `valueOf()` 方法则调用`toString()` 方法），得到的结果按照前面的规则执行比较
5. 如果一个操作值是布尔值，则将其转换为数值，再进行比较

注：`NaN` 是非常特殊的值，它不和任何类型的值相等，包括它自己，同时它与任何类型的值比较大小时都返回 `false`。

##### 相等操作符（==）

相等操作符会对操作值进行隐式转换后进行比较：

1. 如果一个操作值为布尔值，则在比较之前先将其转换为数值
2. 如果一个操作值为字符串，另一个操作值为数值，则通过Number()函数将字符串转换为数值
3. 如果一个操作值是对象，另一个不是，则调用对象的 `valueOf()` 方法，得到的结果按照前面的规则进行比较
4. null` 与 `undefined` 是相等的` 
5. 如果一个操作值为 `NaN`，则相等比较返回 `false`
6. 如果两个操作值都是对象，则比较它们是不是指向同一个对象

### split()、join()的区别

前者是切割成数组的形式

后者是将数组转换为字符串

### 数组方法pop/push/unshift/shift

| 数组方法  | 描述                                                         |
| --------- | ------------------------------------------------------------ |
| pop()     | 删除原数组最后一项，并返回删除元素的值；如果数组为空则返回undefined |
| push()    | 将参数添加到原数组末尾，并返回数组的长度                     |
| unshift() | 将参数添加到原数组开头，并返回数组的长度                     |
| shift()   | 删除原数组第一项，并返回删除元素的值；如果数组为空则返回undefined |

### 事件绑定和普通事件有什么区别

普通事件中的onclick是DOM0级事件只支持单个事件，会被其他onclick事件覆盖，而事件绑定中的addEventListener是DOM2级事件可以添加多个事件而不用担心被覆盖

**普通添加事件的方法：**

```javascript
const btn = document.getElementById("hello");
btn.onclick = function(){
	alert(1);
}
btn.onclick = function(){
	alert(2);
}
```

执行上面的代码只会alert 2 

**事件绑定方式添加事件：**

```javascript
const btn = document.getElementById("hello");
btn.addEventListener("click",function(){
	alert(1);
},false);
btn.addEventListener("click",function(){
	alert(2);
},false);
```

执行上面的代码会先alert 1 再 alert 2

### IE 和 DOM 事件流有什么区别

**事件**

HTML元素事件是浏览器内在自动产生的,当有事件发生时html元素会向外界(这里主要指元素事件的订阅者)发出各种事件,如click,onmouseover,onmouseout等等。

**DOM事件流**

DOM(文档对象模型)结构是一个树型结构，当一个HTML元素产生一个事件时，该事件会在元素结点与根结点之间的路径传播，路径所经过的结点都会收到该事件，这个传播过程可称为DOM事件流。

**冒泡型事件(Bubbling)**

    这是IE浏览器对事件模型的实现。冒泡，顾名思义，事件像个水中的气泡一样一直往上冒，直到顶端。从DOM树型结构上理解，就是事件由叶子结点沿祖先结点一直向上传递直到根结点；从浏览器界面视图HTML元素排列层次上理解就是事件由具有从属关系的最确定的目标元素一直传递到最不确定的目标元素.

**捕获型事件(Capturing)**

Netscape Navigator的实现，它与冒泡型刚好相反，由DOM树最顶层元素一直到最精确的元素，直观上的理解应该如同冒泡型，事件传递应该由最确定的元素，即事件产生元素开始。

![冒泡和捕获](http://www.th7.cn/Article/UploadFiles/200907/20090730233130592.jpg)

**DOM标准事件模型**

    因为两个不同的模型都有其优点和解释，DOM标准支持捕获型与冒泡型，可以说是它们两者的结合体。它可以在一个DOM元素上绑定多个事件处理器，并且在处理函数内部，this关键字仍然指向被绑定的DOM元素，另外处理函数参数列表的第一个位置传递事件event对象。

首先是捕获式传递事件，接着是冒泡式传递，所以，如果一个处理函数既注册了捕获型事件的监听，又注册冒泡型事件监听，那么在DOM事件模型中它就会被调用两次。

**实例**：

```html
<body>
    <div>
        <button>点击这里</button>
    </div>
</body>
```

冒泡：`button` -&gt; `div` -&gt; `body` （IE 事件流）

捕获：`body` -&gt; `div` -&gt; `button` （Netscape事件流）

DOM: `body` -&gt; `div` -&gt; `button` -&gt; `button`
                      -&gt; `div` -&gt; `body`（先捕获后冒泡）

**事件侦听函数的区别 **

```javascript
// IE使用: 
[Object].attachEvent("name_of_event_handler", fnHandler); //绑定函数 
[Object].detachEvent("name_of_event_handler", fnHandler); //移除绑定 

// DOM使用： 
[Object].addEventListener("name_of_event", fnHandler, bCapture); //绑定函数 
[Object].removeEventListener("name_of_event", fnHandler, bCapture); //移除绑定
```

**如何取消浏览器事件的传递与事件传递后浏览器的默认处理**

取消事件传递是指，停止捕获型事件或冒泡型事件的进一步传递。

事件传递后的默认处理是指，通常浏览器在事件传递并处理完后会执行与该事件关联的默认动作（如果存在这样的动作）。例如，如果表单中input type 属性是“submit”，点击后在事件传播完浏览器就就自动提交表单。又例如，input 元素的 keydown 事件发生并处理后，浏览器默认会将用户键入的字符自动追加到 input 元素的值中。

要取消浏览器的事件传递,IE与DOM标准又有所不同。

在IE下,通过设置 `event` 对象的 `cancelBubble` 为 `true` 即可。

```javascript
function someHandle() { 
   window.event.cancelBubble = true; 
}
```

DOM标准通过调用 `event`对象的 `stopPropagation()` 方法即可。

```javascript
function someHandle(event) {
    event.stopPropagation(); 
}
```

因些，跨浏览器的停止事件传递的方法是:

```javascript
function someHandle(event) { 
  event = event || window.event; 
  if(event.stopPropagation) 
     event.stopPropagation(); 
  else event.cancelBubble = true; 
}
```

取消事件传递后的默认处理，IE与DOM标准又不所不同。

在IE下,通过设置 `event` 对象的 `returnValue` 为 `false` 即可。



```javascript
function someHandle() { 
   window.event.returnValue = false; 
}
```
因些，跨浏览器的取消事件传递后的默认处理方法是：

```javascript
function someHandle(event) { 
   event.preventDefault(); 
}
```
### IE 和标准下有哪些兼容性的写法

```javascript
var ev = ev || window.event
document.documentElement.clinetWidth || document.body.clientWidth
var target = ev.srcElement || ev.target
```
<span class="line">

### call 和 apply 的区别

call 和 apply 相同点：
都是为了用一个本不属于一个对象的方法，让这个对象去执行

**基本使用**

**call()**

```javascript
function.call(obj[,arg1[, arg2[, [,.argN]]]]])
```
*   调用call的对象必须是个函数function
*   `call`的第一个参数将会是function改变上下文后指向的对象.如果不传，将会默认是全局对象`window`
*   第二个参数开始可以接收任意个参数，这些参数将会作为function的参数传入function
*   调用`call`的方法会立即执行

**apply()**

```javascript
function.apply(obj[,argArray])
```
与`call`方法的使用基本一致，但是只接收两个参数，其中第二个参数必须是一个**数组**或者**类数组**，这也是这两个方法很重要的一个区别

**数组与类数组小科普**

数组我们都知道是什么，它的特征都有哪些呢？

1.  可以通过角标调用，如 `array[0]`2.  具有长度属性`length`3.  可以通过 for 循环和`forEach`方法进行遍历类数组顾名思义，具备的特征应该与数组基本相同，那么可以知道，一个形如下面这个对象的对象就是一个类数组

```javascript
const arrayLike = {
    0: 'item1',
    1: 'item2',
    2: 'item3',
    length: 3
}
```
类数组`arrayLike`可以通过角标进行调用，具有`length`属性，同时也可以通过 for 循环进行遍历

我们经常使用的获取dom节点的方法返回的就是一个类数组，在一个方法中使用 `arguments`关键字获取到的该方法的所有参数也是一个类数组

但是类数组却不能通过`forEach`进行遍历，因为`forEach`是数组原型链上的方法，类数组毕竟不是数组，所以无法使用

**不同点**

`call`方法从第二个参数开始可以接收任意个参数，每个参数会映射到相应位置的func的参数上，可以通过参数名调用，但是如果将所有的参数作为数组传入，它们会作为一个整体映射到func对应的第一个参数上，之后参数都为空

```javascript
function func (a,b,c) {}

func.call(obj, 1,2,3)
// function接收到的参数实际上是 1,2,3

func.call(obj, [1,2,3])
// function接收到的参数实际上是 [1,2,3],undefined,undefined
```


`apply`方法最多只有两个参数，第二个参数接收数组或者类数组，但是都会被转换成类数组传入func中，并且会被映射到func对应的参数上

```javascript
func.apply(obj, [1,2,3])
// function接收到的参数实际上是 1,2,3

func.apply(obj, {
    0: 1,
    1: 2,
    2: 3,
    length: 3
})
// function接收到的参数实际上是 1,2,3
```
### b 继承 a 的方法

**方法一：对象冒充**

```javascript
function Parent(username){
    this.username = username;
    this.hello = function(){
        console.log(this.username);
    }
}
function Child(username,password){
    this.method = Parent; // this.method 作为一个临时的属性，并且指向了 Parent所指向的对象函数
    this.method(username); // 执行 this.method 方法，即执行了 Parent 所指向的对象函数
    delete this.method; // 销毁 this.method 属性，即此时 Child 就已经拥有了 Parent 的所有方法和属性		
    this.password = password;
    this.world = function(){
        console.log(this.password);
    }
}
const parent = new Parent('hello parent');
const child = new Child('hello child','123456');
console.log(child);
parent.hello();
child.hello();
child.world();
```
**方法二：call() **

call 方法是 Function 类中的方法
call 方法的第一个参数的值赋值给类（即方法）中出现的 this
call 方法的第二个参数开始依次赋值给类（即方法）所接受的参数

```javascript
function Parent(username){
    this.username = username;
    this.hello = function(){
        console.log(this.username);
    }
}
function Child(username,password){
    Parent.call(this,username);
    this.password = password;
    this.world = function(){
        console.log(this.password);
    }
}
const parent = new Parent('hello parent');
const child = new Child('hello child','123456');
parent.hello();
child.hello();
child.world();
```
**方法三：apply()**

apply方法接受2个参数

第一个参数与call方法的第一个参数一样，即赋值给类（即方法）中出现的this

第二个参数为数组类型，这个数组中的每个元素依次赋值给类（即方法）所接受的参数

```javascript
function Parent(username){
    this.username = username;
    this.hello = function(){
        console.log(this.username);
    }
}
function Child(username,password){
    Parent.apply(this,new Array(username));
    this.password = password;
    this.world = function(){
        console.log(this.password);
    }
}
const parent = new Parent('hello parent');
const child = new Child('hello child','123456');
parent.hello();
child.hello();
child.world();
```
**方法四：原型链**

即子类通过 prototype 将所有在父类中通过 prototype 追加的属性和方法都追加到 Child ,从而实现继承

```javascript
function Parent(){}
Parent.prototype.hello = "hello";
Parent.prototype.sayHello = function(){
    console.log(this.hello);
}
function Child(){}
Child.prototype = new Parent();// 将 Parent 中所有通过 prototype 追加的属性和方法都追加到 Child 从而实现了继承
Child.prototype.world = "world";
Child.prototype.sayWorld = function(){
    console.log(this.world);
}
const child = new Child();
child.sayHello();
child.sayWorld();
```
**方法五：混合方式，call（）+ 原型链**

```javascript
function Parent(hello){
    this.hello = hello;
}
Parent.prototype.sayHello = function(){
    console.log(this.hello);
}
function Child(hello,world){
    Parent.call(this,hello); // 将父类的属性继承过来
    this.world = world;
}
Child.prototype = new Parent(); //将父类的方法继承过来
Child.prototype.sayWorld = function(){ // 新增方法
    console.log(this.world);
}
const child = new Child("hello","world");
child.sayHello();
child.sayWorld();
```
### JavaScript this 指针、闭包、作用域

#### **js 中的this 指针 **

在函数执行时，this 总是指向调用该函数的对象。要判断 this 的指向，其实就是判断 this 所在的函数属于谁。

在《javaScript语言精粹》这本书中，把 this 出现的场景分为四类，简单的说就是：

**1）有对象就指向调用对象**

```javascript
var myObject = { value: 123 }
myObject.getValue = function(){
    console.log(this.value); // 123
    console.log(this); // {value: 123, getValue: ƒ}
}
myObject.getValue();
```
**2）没调用对象就指向全局对象**

```javascript
var myObject = { value: 123 }
myObject.getValue = function(){
    var foo = function(){
        console.log(this.value); // undefined
        console.log(this); // Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, frames: Window, …}	

        // foo函数虽然定义在getValue 函数体内，但是不属于 getValue也不属于 myObject,所以调用的时候，它的 this 指针指向了全局对象
    }
    foo();
    return this.value;
}
console.log(myObject.getValue()); // 123
```
**3) 用new构造就指向新对象** 

```javascript
// js 中通过 new 关键词来调用构造函数，此时 this 会绑定杂该新对象上
var someClass = function(){
    this.value = 123;
}
var myCreate = new someClass();
console.log(myCreate.value); // 123
```
**4）通过 apply 或 call 或 bind 来改变 this 的指向**

```javascript
var myObject = { value: 123 };
var foo = function(){
    console.log(this);
}
foo(); // Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, frames: Window, …}
foo.apply(myObject); // {value: 123}
foo.call(myObject); // {value: 123}
var newFoo = foo.bind(myObject);
newFoo(); // {value: 123}
```
#### **闭包**

闭包英文是 Closure ，简而言之，闭包就是

1. 函数的局部集合，只是这些局部变量在函数返回后会继续存在
2. 函数的“堆栈”在函数返回后并不释放，可以理解为这些函数堆栈并不在栈上分配而是在堆上分配
3. 当在一个函数内部定义另外一个函数就会产生闭包

作为局部变量都可以被函数内的代码访问，这个和静态语言是没有差别的，闭包的差别在于局部变量可以在函数执行结束后仍然被函数外的代码访问，这意味着函数必须返回一个指向闭包的“引用”，或将这个“引用”赋值给某个外部变量，才能保证闭包中局部变量被外部代码访问，当然包含这个引用的实体应该是一个对象。但是ES并没有提供相关的成员和方法来访问包中的局部变量，但是在ES中，函数对象中定义的内部函数是可以直接访问外部函数的局部变量，通过这种机制，可以用如下方式完成对闭包的访问。

```javascript
function greeting(name){
    var text = "Hello " + name; // 局部变量
    // 每次调用时，产生闭包，并返回内部函数对象给调用者
    return function(){
        console.log(text);
    }
}
var sayHello = greeting('Closure');
// 通过闭包访问到了局部变量text
sayHello();  // 输出Hello Closure
```



在 ECMAscript 的脚本函数运行时，每个函数关联都有一个执行上下文场景（Exection Context），这个执行上下文包括三个部分

*   文法环境（The LexicalEnvironment）
*   变量环境（The VariableEnvironment）
*   this绑定

其中第三点this绑定与闭包无关，不在本文中讨论。文法环境中用于解析函数执行过程使用到的变量标识符。我们可以将文法环境想象成一个对象，该对象包含了两个重要组件，环境记录(Enviroment Recode)，和外部引用(指针)。环境记录包含包含了函数内部声明的局部变量和参数变量，外部引用指向了外部函数对象的上下文执行场景。全局的上下文场景中此引用值为NULL。这样的数据结构就构成了一个单向的链表，每个引用都指向外层的上下文场景。

例如上面我们例子的闭包模型应该是这样，sayHello函数在最下层，上层是函数greeting，最外层是全局场景。如下图：

![closure](https://coolshell.cn/wp-content/uploads/2012/03/closure.png)

因此当sayHello被调用的时候，sayHello会通过上下文场景找到局部变量text的值，因此在屏幕的对话框中显示出”Hello Closure”

针对一些例子来帮助大家更加深入的理解闭包,下面共有5个样例，例子来自于[JavaScript Closures For Dummies(](http://blog.morrisjohns.com/javascript_closures_for_dummies.html)[镜像](http://web.archive.org/web/20080209105120/http://blog.morrisjohns.com/javascript_closures_for_dummies)[)](http://blog.morrisjohns.com/javascript_closures_for_dummies.html)。

**例子1:闭包中局部变量是引用而非拷贝** 

```javascript
function say667(){
    var num = 666;
    var sayConsole = function(){
        console.log(num);
    }
    num++;
    return sayConsole;
}
var sayConsole = say667();
sayConsole(); // 667
```
**例子2：多个函数绑定同一个闭包，因为他们定义在同一个函数内。**

```javascript
function setupSomeGlobals(){
    var num = 666;
    gConsoleNumber = function() { console.log(num); }
    gIncreaseNumber = function() { num++; }
    gSetNumber = function(x) { num = x; }
}
setupSomeGlobals();
gConsoleNumber(); // 666
gIncreaseNumber();
gConsoleNumber(); // 667
gSetNumber(12);
gConsoleNumber(); // 12
```
**例子3：当在一个循环中赋值函数时，这些函数将绑定同样的闭包**

```javascript
function buildList(list){
    var result = [];
    for(var i = 0; i < list.length; i++){
        var item = 'item' + list[i];
        result.push(function(){
            console.log(item+' '+list[i]);
        })
    }
    return result;
}
function testList(){
    var fnList = buildList([1,2,3]);
    for(var j = 0; j < fnList.length; j++){
        fnListj;
    }
}
testList(); // 输出3次 item3 undefined
```
testList的执行结果是弹出item3 undefined窗口三次，因为这三个函数绑定了同一个闭包，而且item的值为最后计算的结果，但是当i跳出循环时i值为4，所以list[4]的结果为undefined.

**例子4：外部函数所有局部变量都在闭包内，即使这个变量声明在内部函数定义之后。**

```javascript
function sayAlice(){
    var sayConsole = function(){
        console.log(alice);
    }
    var alice = "Hello Alice";
    return sayConsole;
}
var helloAlice=sayAlice();
helloAlice();
```
执行结果输出”Hello Alice”的窗口。即使局部变量声明在函数sayAlert之后，局部变量仍然可以被访问到。

**例子5：每次函数调用的时候创建一个新的闭包**

```javascript
function newClosure(someNum,someRef){
    var num = someNum;
    var anArray = [1,2,3];
    var ref = someRef;
    return function(x){
        num += x;
        anArray.push(num);
        console.log('num: ' + num +'\nanArray ' + anArray.toString() +'\nref.someVar ' + ref.someVar);
    }
}

closure1=newClosure(40,{someVar:'closure 1'});
closure2=newClosure(1000,{someVar:'closure 2'});

closure1(5); // num: 45 anArray 1,2,3,45 ref.someVar closure 1
closure2(-10); // num: 990 anArray 1,2,3,990 ref.someVar closure 2
```
##### **闭包的缺点：**

（1）由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。

（2）闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。

例子：

```javascript
function Cars(){
  this.name = "Benz";
  this.color = ["white","black"];
}
Cars.prototype.sayColor = function(){
  var outer = this;
  return function(){
    return outer.color
  };
};
 
var instance = new Cars();
console.log(instance.sayColor()())
```


改造：

```javascript
function Cars(){
  this.name = "Benz";
  this.color = ["white","black"];
}
Cars.prototype.sayColor = function(){
  var outerColor = this.color; //保存一个副本到变量中
  return function(){
    return outerColor; //应用这个副本
  };
  outColor = null; //释放内存
};
 
var instance = new Cars();
console.log(instance.sayColor()())
```
#### **作用域**

在JS当中一个变量的作用域（scope）是程序中定义这个变量的区域。变量分为两类：全局（global）的和局部的。其中全局变量的作用域是全局性的，即在JavaScript代码中，它处处都有定义。而在函数之内声明的变量，就只在函数体内部有定义。它们是局部变量，作用域是局部性的。函数的参数也是局部变量，它们只在函数体内部有定义。                  

我们可以借助JavaScript的作用域链（scope chain）更好地了解变量的作用域。每个JavaScript执行环境都有一个和它关联在一起的作用域链。这个作用域链是一个对象列表或对象链。当JavaScript代码需要查询变量x的值时（这个过程叫做变量解析（variable name resolution）），它就开始查看该链的第一个对象。如果那个对象有一个名为x的属性，那么就采用那个属性的值。如果第一个对象没有名为x的属性，JavaScript就会继续查询链中的第二个对象。如果第二个对象仍然没有名为x的属性，那么就继续查询下一个对象，以此类推。如果查询到最后（指顶层代码中）不存在这个属性，那么这个变量的值就是未定义的。

```javascript
var a,b;
(function(){
    alert(a); // undefined 
    alert(b); // undefined 
    var a = b = 3;
    alert(a); // 3
    alert(b); // 3
})();
    alert(a); // undefined 
	alert(b); // 3
```
以上代码相当于

```javascript
var a,b;
(function(){
    alert(a);
    alert(b);
    var a = 3;
    b = 3;
    alert(a);
    alert(b);
})();
    alert(a);
```
### 事件委托是什么？

**概述**

什么叫做事件委托，别名叫事件代理，JavaScript 高级程序设计上讲。事件委托就是利用事件冒泡，只指定一个事件处理程序，就可以管理某一类型的所有事件。

实际例子：

有三个同事预计会在周一收到快递，为签收快递，有两种方法：一是三个人在公司门口等快递，二是委托给前台的小姐代为签收。现实生活中，我们大多采用委托的方案（公司也不会容忍那么多人站在门口）。前台小姐收到快递后，会判断收件人是谁，按照收件人的要求签收，甚至是代付。这种方案还有一个好处就是，即使公司来了很多新员工（不管多少），前台小姐也会在收到寄给新员工们的快递后核实代为签收。                  

这里有2层意思：
第一、现在委托前台的小姐是可以代为签收的，即程序中的现有的 DOM 节点是有事件的。

第二、新员工也可以被前台小姐代为签收，即程序中新添加的 DOM 节点也是有事件的。

**为什么要使用事件委托**

一般来说，DOM 需要有事件处理程序，就会直接给它设处理程序，但是如果是很多 DOM 需要添加处理事件呢？例如我们有100个 li，每个 li 都有相同的 click 点击事件，可能我们会用到 for 循环，来遍历所有 li ,然后给它们添加事件，那么会存在什么样的问题？

在 JavsScript 中，添加到页面上的事件处理程序数量将直接影响到整体运行性能，因为需要不断地与 DOM 进行交互，访问 DOM 的次数越多，引起浏览器重绘与重排的次数也就越多，就会延长整个页面的交互就绪时间，这就是为什么性能优化的主要思想之一就是减少 DOM 操作的原因、如果要用到事件委托，就会将所有的操作都放在 js 程序里面，与 DOM 的操作就只需要交互一次，这样就可以大大减少与 DOM 的交互次数，提高性能。

每个函数都是一个对象，是对象就会占用内存，对象越多，内存占用率就越大，自然性能就越差了（内存不够用，是硬伤，哈哈），比如上面的100个li，就要占用100个内存空间，如果是1000个，10000个呢，那只能说呵呵了，如果用事件委托，那么我们就可以只对它的父级（如果只有一个父级）这一个对象进行操作，这样我们就需要一个内存空间就够了，是不是省了很多，自然性能就会更好。                  

**事件委托的原理**

事件委托是利用事件的冒泡原理来实现的，何为事件冒泡？就是事件从最深的节点开始执行，然后逐步向上传播事件，例子：

页面上有一个节点树，div&gt;ul&gt;li&gt;a，比如给最里面的 a 加一个 click 点击事件，那么这个事件就会一层一层的往外执行，执行顺序 a&gt;li&gt;ul&gt;div，有这么一个机制，那么我们给最外面的 div 加点击事件，那么里面的 ul,li,a 做点击事件的时候，都会冒泡到最外层的 div 上面，都会触发，这就是事件委托，委托他们父级代为执行事件。

**事件委托怎么实现**

```html
<ul id="ul">
    <li>111</li>
    <li>222</li>
    <li>333</li>
    <li>444</li>
</ul>
```
实现功能是点击li，弹出123：

```javascript
window.onload = function(){
    var oUl = document.getElementById('ul');
    var aLi = oUl.getElementsByTagName('li');
    for(var i = 0; i < aLi.length; i++){
        aLi[i].onclick = function(){
            alert(123);
        }
    }
}
```
上面的代码的意思很简单，相信很多人都是这么实现的，我们看看有多少次的dom操作，首先要找到ul，然后遍历li，然后点击li的时候，又要找一次目标的li的位置，才能执行最后的操作，每次点击都要找一次li；                  

那么我们用事件委托的方式做又会怎么样呢？

```javascript
window.onload = function(){
    var oUl = document.getElementById('ul');
    oUl.onclick = function(){
        alert(123);
    }
}
```

  这里用父级ul做事件处理，当li被点击时，由于冒泡原理，事件就会冒泡到ul上，因为ul上有点击事件，所以事件就会触发，当然，这里当点击ul的时候，也是会触发的，那么问题就来了，如果我想让事件代理的效果跟直接给节点的事件效果一样怎么办，比如说只有点击li才会触发，不怕，我们有绝招：

Event对象提供了一个属性叫target，可以返回事件的目标节点，我们成为事件源，也就是说，target就可以表示为当前的事件操作的dom，但是不是真正操作dom，当然，这个是有兼容性的，标准浏览器用ev.target，IE浏览器用event.srcElement，此时只是获取了当前节点的位置，并不知道是什么节点名称，这里我们用nodeName来获取具体是什么标签名，这个返回的是一个大写的，我们需要转成小写再做比较（习惯问题）：

```javascript
window.onload = function(){
    var oUl = document.getElementById("ul");
    oUl.onclick = function(ev){
        var ev = ev || window.event;
        var target = ev.target || ev.srcElement;
        if(target.nodeName.toLowerCase() == "li"){
            alert(target.innerHTML);
        }
    }
}
```
这样改下就只有点击li会触发事件了，且每次只执行一次dom操作，如果li数量很多的话，将大大减少dom的操作，优化的性能可想而知！

上面的例子是说li操作的是同样的效果，要是每个li被点击的效果都不一样，那么用事件委托还有用吗？

```javascript
var Add = document.getElementById("add");
var Remove = document.getElementById("remove");
var Move = document.getElementById("move");
var Select = document.getElementById("select");
Add.onclick = function(){
    alert('添加');
};
Remove.onclick = function(){
    alert('删除');
};
Move.onclick = function(){
    alert('移动');
};
Select.onclick = function(){
    alert('选择');
}
```


上面实现的效果我就不多说了，很简单，4个按钮，点击每一个做不同的操作，那么至少需要4次dom操作，如果用事件委托，能进行优化吗？

```javascript
var oBox = document.getElementById("box");
oBox.onclick = function(ev){
    var ev = ev || window.event;
    var target = ev.target || ev.srcElement;
    if(target.nodeName.toLowerCase() == 'input'){
        switch(target.id) {
            case 'add':
            alert('添加');
            break;
            case 'remove':
            alert('删除');
            break;
            case 'move':
            alert('移动');
            break;
            case 'select':
            alert('选择');
            break;
            default :
            alert('业务错误');
            break;
        }
    }
}
```


用事件委托就可以只用一次dom操作就能完成所有的效果，比上面的性能肯定是要好一些的 

 现在讲的都是document加载完成的现有dom节点下的操作，那么如果是新增的节点，新增的节点会有事件吗？也就是说，一个新员工来了，他能收到快递吗？

```html
<input type="button" name="" id="btn" value="添加" />
<ul id="ul1">
    <li>111</li>
    <li>222</li>
    <li>333</li>
    <li>444</li>
</ul>
```
现在是移入li，li变红，移出li，li变白，这么一个效果，然后点击按钮，可以向ul中添加一个li子节点

```javascript
window.onload = function(){
    var oBtn = document.getElementById("btn");
    var oUl = document.getElementById("ul");
    var aLi = oUl.getElementsByTagName("li");
    var num = 4;

    // 鼠标移入变红，移出变白
    for(var i = 0; i < aLi.length; i++){
        aLi[i].onmouseover = function(){
            this.style.background = 'red';
        }
        aLi[i].onmouseout = function(){
            this.style.background = '#fff';
        }
    }

    // 新增节点
    oBtn.onclick = function(){
        num++;
        var oLi = document.createElement('li');
        oLi.innerHTML = 111 * num;
        oUl.appendChild(oLi);
    }
}
```
这是一般的做法，但是你会发现，新增的li是没有事件的，说明添加子节点的时候，事件没有一起添加进去，这不是我们想要的结果，那怎么做呢？一般的解决方案会是这样，将for循环用一个函数包起来，命名为mHover，如下：

```javascript
// 将鼠标移出移入包装为一个函数
window.onload = function(){
    var oBtn = document.getElementById("btn");
    var oUl = document.getElementById("ul");
    var aLi = oUl.getElementsByTagName("li");
    var num = 4;


    // 鼠标移入变红，移出变白
    function mHover(){
        for(var i = 0; i < aLi.length; i++){
            aLi[i].onmouseover = function(){
                this.style.background = 'red';
            }
            aLi[i].onmouseout = function(){
                this.style.background = '#fff';
            }
        }
    }
    mHover();
    // 新增节点
    oBtn.onclick = function(){
        num++;
        var oLi = document.createElement('li');
        oLi.innerHTML = 111 * num;
        oUl.appendChild(oLi);
        mHover();
    }
}
```
虽然功能实现了，看着还挺好，但实际上无疑是又增加了一个dom操作，在优化性能方面是不可取的，那么有事件委托的方式，能做到优化吗？

```javascript
window.onload = function(){
    var oBtn = document.getElementById("btn");
    var oUl = document.getElementById("ul");
    var aLi = oUl.getElementsByTagName("li");
    var num = 4;


    // 事件委托 鼠标移入变红，移出变白
    // 添加的子元素也有事件
    oUl.onmouseover = function(){
        var ev = ev || window.event;
        var target = ev.target || ev.srcElement;
        if(target.nodeName.toLowerCase() == 'li'){
            target.style.background = 'red';
        }
    }		
    oUl.onmouseout = function(){
        var ev = ev || window.event;
        var target = ev.target || ev.srcElement;
        if(target.nodeName.toLowerCase() == 'li'){
            target.style.background = '#fff';
        }
    }
    // 新增节点
    oBtn.onclick = function(){
        num++;
        var oLi = document.createElement('li');
        oLi.innerHTML = 111 * num;
        oUl.appendChild(oLi);
    }
}
```
**另外一个思考的问题**

现在给一个场景 ul &gt; li &gt; div &gt;p，div占满li，p占满div，还是给ul绑定时间，需要判断点击的是不是li（假设li里面的结构是不固定的），那么e.target就可能是p，也有可能是div，这种情况你会怎么处理呢？

```html
  <ul id="test">
    <li>
        <p>11111111111</p>
    </li>
    <li>
        <div>
            22222222
        </div>
    </li>
    <li>
        <span>3333333333</span>
    </li>
    <li>4444444</li>
</ul>
```


如上列表，有4个li，里面的内容各不相同，点击li，event对象肯定是当前点击的对象，怎么指定到li上，下面我直接给解决方案：

```html
var oUl = document.getElementById('test');
oUl.addEventListener('click',function(ev){
    var target = ev.target;
    while (target !== oUl) {
        if(target.tagName.toLowerCase() == 'li'){
            alert(target.innerHTML);
            break;
        }
        target = target.parentNode;
    }
});
```


### 如何阻止事件冒泡和默认事件

在解答这个问题之前，先来看看事件的执行顺序

```html
<div>
    <ul>
        <li>冒泡/捕获</li>
    </ul>
</div>
```


实例：

```javascript
var html = document.documentElement;
var body = document.body;
var div = body.querySelector('div');
var ul = body.querySelector('ul');
var li = body.querySelector('li');


// 捕获
ul.addEventListener('click',captureCallback,true);
li.addEventListener('click',captureCallback,true);
div.addEventListener('click',captureCallback,true);
body.addEventListener('click',captureCallback,true);
html.addEventListener('click',captureCallback,true);	

// 冒泡
ul.addEventListener('click',bubblingCallback,false);
li.addEventListener('click',bubblingCallback,false);
div.addEventListener('click',bubblingCallback,false);
body.addEventListener('click',bubblingCallback,false);
html.addEventListener('click',bubblingCallback,false);

function captureCallback(e){
    // e.stopPropagation();
    var target = e.currentTarget;
    console.log(target.tagName);
}	
function bubblingCallback(e){
    // e.stopPropagation();
    var target = e.currentTarget;
    console.log(target.tagName);
}
```
点击 html 中的冒泡/捕获，可以得到下面的结果

```shell
capturing&bubbling phase.html:36 HTML
capturing&bubbling phase.html:36 BODY
capturing&bubbling phase.html:36 DIV
capturing&bubbling phase.html:36 UL
capturing&bubbling phase.html:36 LI
capturing&bubbling phase.html:40 LI
capturing&bubbling phase.html:40 UL
capturing&bubbling phase.html:40 DIV
capturing&bubbling phase.html:40 BODY
capturing&bubbling phase.html:40 HTML
```



总结就是先捕获，后冒泡，捕获是从上到下，冒泡是从下到上。（形象说法：捕获像石头沉入海底，冒泡像气球冒出水面）

去掉 函数 `bubblingCallback` 中的 `e.stopPropagation()`的注释，则只会进行捕获，输出

```shell
capturing&bubbling phase.html:40 HTML
capturing&bubbling phase.html:40 BODY
capturing&bubbling phase.html:40 DIV
capturing&bubbling phase.html:40 UL
capturing&bubbling phase.html:40 LI
capturing&bubbling phase.html:45 LI
```


而取消 函数 `captureCallback`中的 `e.stopPropagation()` 注释，则只捕获到 最上面的标签

```shell
capturing&bubbling phase.html:40 HTML
```
通过上面，我们可以得知， `event.stopPropagation()`，可以阻止 捕获和冒泡阶段当前事件的进一步传播。

规则：

1.  **在冒泡事件和捕获事件同时存在的情况下，捕获事件优先级高一点**
2.  **在同一个元素的绑定事件中，冒泡和捕获没有次序之分，遵循Javascript的执行顺序。**
3.  **在元素上同时绑定捕获事件和冒泡事件，如果通过此元素的子级元素触发，则优先触发捕获事件，若不通过此元素的子级元素触发，则按照Javascript执行顺序触发。**

**事件不同浏览器处理函数**

*   element.addEventListener(type, listener[, useCapture]); // IE6~8不支持（捕获和冒泡通过useCapture,默认false）
*   element.attachEvent(’on’ + type, listener); // IE6~10，IE11不支持(只执行冒泡事件)
*   element[’on’ + type] = function(){} // 所有浏览器（默认执行冒泡事件）

![事件不同浏览器处理函数](/images/前端面试题目汇总摘录(JS 基础篇)-事件不同浏览器处理函数.png)

W3C 中定义了 3个事件阶段，依次是捕获阶段、目标阶段、冒泡阶段。事件对象按照上图的传播路径依次完成这些阶段。如果某个阶段不支持或事件对象的传播被终止，那么该阶段就会被跳过。举个例子，如果`Event.bubbles`属性被设置为`false`，那么冒泡阶段就会被跳过。如果`Event.stopPropagation()`在事件派发前被调用，那么所有的阶段都会被跳过。

* **捕获** 阶段：在事件对象到达事件目标之前，事件对象必须从 window 经过目标的祖先节点传播到事件目标。这个阶段被我们称为捕获阶段，在这个阶段注册的事件监听器在事件到达其目标前必须先处理事件。

* **目标** 阶段：事件对象到达其事件目标，这个阶段被我们称之为目标阶段。一旦事件对象达到事件目标，该阶段的事件监听器就要对它进行处理。如果一个事件对象类型被标志为不能冒泡。那么对应的事件对象在到此阶段时就会终止传播。

* **冒泡** 阶段：事件对象以一个与捕获阶段相反的方向从事件目标传播经过其祖先节点传播到 window，这个阶段被称之为冒泡阶段，在此阶段注册的事件监听器会对应的冒泡事件进行处理

在一个事件完成了所有阶段的传播路径后，它的Event.currentTarget会被设置为null并且Event.eventPhase会被设为0。Event的所有其他属性都不会改变（包括指向事件目标的Event.target属性）

**跨浏览器的事件处理函数**

```javascript
var EventUtil = {
  addHandler: function(element, type, handler) {
    if (element.addEventListener) {  // DOM2
      element.addEventListener(type, handler, false);
    } else if (element.attachEvent) {  // IE
      element.attachEvent('on' + type, handler);
    } else {  // DOM0
      element['on' + type] = handler;
    }
  },

  removeHandler: function(element, type, handler) {
    if (element.removeEventListener) {
      element.removeEventListener(type, handler, false);
    } else if (element.detachEvent) {
      element.detachEvent('on' + type, handler);
    } else {
      element['on' + type] = null;
    }
  }
};
```


**跨浏览器的事件对象：**

```javascript
var EventUtil = {
  getEvent: function(e) {
    return e ? e : window.e;
  },

  getTarget: function(e) {
    return e.target || e.srcElement;
  },

  preventDefault: function(e) {
    if (e.preventDefault) {
      e.preventDefault();
    } else {
      e.returnValue = false;
    }
  },

  stopPropagation: function(e) {
    if (e.stopPropagation) {
      e.stopPropagation()
    } else {
      e.cancelBubble = true;
    }
  }
}
```
js冒泡和捕获是事件的两种行为，使用event.stopPropagation()起到阻止捕获和冒泡阶段中当前事件的进一步传播。使用event.preventDefault()可以取消默认事件。

**防止冒泡和捕获**

**w3c的方法是e.stopPropagation()，IE则是使用e.cancelBubble = true**

    topPropagation也是事件对象(Event)的一个方法，作用是阻止目标元素的冒泡事件，但是会不阻止默认行为。什么是冒泡事件？如在一个按钮是绑定一个”click”事件，那么”click”事件会依次在它的父级元素中被触发。stopPropagation就是阻止目标元素的事件冒泡到父级元素。如：
```html

<div id='div' onclick='alert("div");'>
    <ul onclick='alert("ul");'>
    	<li onclick='alert("li");'>test</li>
    </ul>
</div>
```
单击时，会依次触发alert(“li”),alert(“ul”),alert(“div”)，这就是事件冒泡。

阻止冒泡

```javascript
window.event? window.event.cancelBubble = true : e.stopPropagation();
```
**取消默认事件**

**w3c的方法是e.preventDefault()，IE则是使用e.returnValue = false**

preventDefault它是事件对象(Event)的一个方法，作用是取消一个目标元素的默认行为。既然是说默认行为，当然是元素必须有默认行为才能被取消，如果元素本身就没有默认行为，调用当然就无效了。什么元素有默认行为呢？如链接`&lt;a&gt;`，提交按钮 `&lt;input type=&quot;submit&quot;&gt;` 等。当Event 对象的cancelable为false时，表示没有默认行为，这时即使有默认行为，调用preventDefault也是不会起作用的。

我们都知道，链接 `&lt;a&gt;` 的默认动作就是跳转到指定页面，下面就以它为例，阻止它的跳转：

```javascript
//假定有链接<a href="http://laibh.top/" id="testA" >laibh.top</a>
var a = document.getElementById("test");
a.onclick =function(e){
if(e.preventDefault){
    	e.preventDefault();
    }else{
    	window.event.returnValue == false;
    }
}
```
**return false**

javascript的return false只会阻止默认行为，而是用[jQuery](http://caibaojian.com/jquery/)的话则既阻止默认行为又防止对象冒泡

下面这个使用原生js，只会阻止默认行为，不会停止冒泡

```html
<div id='div'  onclick='alert("div");'>
<ul  onclick='alert("ul");'>
    <li id='ul-a' onclick='alert("li");'><a href="http://caibaojian.com/"id="testB">caibaojian.com</a></li>
</ul>
</div>
<script>
    var a = document.getElementById("testB");
    a.onclick = function(){
    	return false;
    };
</script>
```
**总结使用方法**

当需要停止冒泡行为时，可以使用

```javascript
function stopBubble(e) { 
//如果提供了事件对象，则这是一个非IE浏览器 
if ( e && e.stopPropagation ) 
    //因此它支持W3C的stopPropagation()方法 
    e.stopPropagation(); 
else 
    //否则，我们需要使用IE的方式来取消事件冒泡 
    window.event.cancelBubble = true; 
}
```


当需要阻止默认行为时，可以使用

```javascript
//阻止浏览器的默认行为 
function stopDefault( e ) { 
    //阻止默认浏览器动作(W3C) 
    if ( e && e.preventDefault ) 
        e.preventDefault(); 
    //IE中阻止函数器默认动作的方式 
    else 
        window.event.returnValue = false; 
    return false; 
}
```
**事件注意点**

*   event代表事件的状态，例如触发event对象的元素、鼠标的位置及状态、按下的键等等；
*   event对象只在事件发生的过程中才有效。

firefox里的event跟IE里的不同，IE里的是全局变量，随时可用；firefox里的要用参数引导才能用，是运行时的临时变量。在IE/Opera中是window.event，在Firefox中是event；而事件的对象，在IE中是window.event.srcElement，在Firefox中是event.target，Opera中两者都可用。

```javascript
                  

function a(e){
    var e = (e) ? e : ((window.event) ? window.event : null); 
    var e = e || window.event; // firefox下window.event为null, IE下event为null
}
```


### 查找 添加 删除 替换 插入到某个节点的方法

```javascript
// 查找节点
document.getElementById('id'); // 通过id查找，返回唯一的节点，如果有多个将会返回第一个，在IE6、7中有个bug，会返回name值相同的元素，所有要做一个兼容
document.getElementsByClassName('class'); // 通过class查找，返回节点数组
document.getElementsByTagName('div'); // 通过标签名

// 创建节点
document.createDocumentFragment(); // 创建内存文档碎片
document.createElement(); // 创建元素
document.createTextNode(); // 创建文本节点

// 添加节点
var oDiv = document.createElement('div');

// 插入 Dom 节点
// 方法1：appendChild() 把节点插入到父节点的末尾
document.body.appendChild(oDiv) // 把 div 插入到 body 中，并且位于末尾
// 方法2：insertBefore() 把节点插入到父节点的某个兄弟节点的前面
var oP = createElement('p'); // 创建一个 p 节点
document.body.insertBefore(oP,oDiv); // 把 p 节点插入到 div 的前面

// 删除节点
document.body.removeChild(oP); // 删除 p 节点

// 替换 Dom 节点
var oSpan = document.createElement('span');
document.body.replaceChild(oSpan,oBox); // 用 span 标签替换 div 标签
```


### javaScript 的本地对象，内置对象和宿主对象

**内部对象**

js中的内部对象包括Array、Boolean、Date、Function、Global、Math、Number、Object、RegExp、String以及各种错误类对象，包括Error、EvalError、RangeError、ReferenceError、SyntaxError和TypeError。 

**本地对象**

Array、Boolean、Date、Function、Number、Object、RegExp、String、Error、EvalError、RangeError、ReferenceError、SyntaxError和TypeError等 new 可以实例化


**内置对象**

其中Global和Math这两个对象又被称为“内置对象”，这两个对象在脚本程序初始化时被创建，不必实例化这两个对象。

**宿主对象**

宿主环境：一般宿主环境由外壳程序创建与维护，只要能提供js引擎执行的环境都可称之为外壳程序。如：web浏览器，一些桌面应用系统等。即由web浏览器或是这些桌面应用系统早就的环境即宿主环境。

那么宿主就是浏览器自带的 document ， window 等

###`==` 和`===` 的不同

前者会自动转换类型，而后者不会。

比较过程：

双等号==：

1. 如果两个值类型相同，再进行三个等号（===）的比较
2. 如果两个值类型不同，也有可能相等，需根据以下规则进行类型转换再比较

      1. 如果一个是 null,一个是 undefined 那么相等

      2. 如果一个是字符串，一个是数值，把字符串转换成数值之后再进行比较

三等号 === ：

1. 如果类型不同，就一定不相等
2. 如果两个都是数值，并且是同一个值，那么相等；如果其中至少一个是 NaN,那么不相等（判断一个值是否是 NaN,只能使用 isNaN() 来判断）
3. 如果两个都是字符串，每个位置的字符都一样，那么相等，否则不相等
4. 如果两个值都是 true,或是 false,那么相等
5. 如果两个值都引用同一个对象或者是函数，那么相等，否则不相等
6. 如果两个值都是 null，或者是 undefined 那么相等。

### javaScript 的同源策略

同源策略的含义：脚本只能读取和所属文档来源相同的窗口和文档的属性。

同源指的是主机名、协议和端口号的组合

![同源策略](/images/前端面试题目汇总摘录(JS 基础篇)-同源策略.png)

**同源策略带来的限制**

1. cookie、LocalStorage 和 IndexDB无法读取
2. DOM 无法获取
3. AJAX请求无法发送

**主流跨域请求解决方案**

**1、JSONP 实现跨域**

为了便于客户端使用数据，逐渐形成了一种非正式传输协议。人们把它称作JSONP。该协议的一个要点就是允许用户传递一个callback参数给服务端，然后服务端返回数据时会将这个callback参数作为函数名来包裹住JSON数据，这样客户端就可以随意定制自己的函数来自动处理返回数据了。

jsonp的核心是动态添加 script标签 来调用服务器提供的js脚本。

[说说JSON和JSONP](http://kb.cnblogs.com/page/139725/)

JSONP实现原理?

1. JS 跨域请求资源会被限制。但是在页面中，script 标签跨域时，却是没有限制的（frame，img同理）。
2. 我们通过，script的src属性，请求服务器，并通过参数（如：？callback=foo，foo为本地一个执行的方法）告诉服务器返回指定格式的JS脚本，并将数据封装在此脚本中。
3. 服务器再配合客户端返回一段脚本 （如：_ foo({“id”: 123, “name” : 张三, “age”: 17});_ ），其实返回的就是 一个客户端本地的一个可执行的方法的方法名, 并将要返回的 数据封装在了参数里
4. 请求到资源后，本地就会执行此方法，通过对参数的处理，也就获取到了我们所要的数据。

**JSONP的局限性** 

JSONP 方式，固然方便强大。但是他的局限性在于，它无法完成POST请求。即是我们将type改为post，在发送请求时，依然会是以**Get**的方式。

**2、CORS跨域**

**CORS原理**

CORS（Cross-Origin-Resource Sharing，跨院资源共享）是一种允许多种资源（图片，Css文字，Javascript等）在一个Web页面请求域之外的另一个域的资源的机制。跨域资源共享这种机制让Web应用服务器支持跨站访问控制，从而使得安全的进行跨站数据传输成为了可能。

通过这种机制设置一系列的**响应头**，这些响应头**允许**浏览器与服务器进行交流，实现资源共享。

[各语言设置响应头的方法](http://enable-cors.org/)

**CORS 解决方案相对于JSONP 更加灵活，而且支持POST请求，是跨域的根源性解决方案**

**3、代理层**

JSONP 和CORS 是主流的 跨域问题 的解决方案。除了他们呐，还有一种解决方案，就是**代理层**。简要说一下

JS 调用本源的后台的方法（这样就不存在跨域的问题），而通过后台（任何具有网络访问功能的后台语言，ASP.NET ,JAVA,PHP等）去跨域请求资源，而后将结果返回至前台。

另外也可以看看

[同源策略](http://laibh.top/2018-05-13-same-origin-policy.html)

[JavaScript的同源策略 Redirect 1](https://developer.mozilla.org/zh-CN/docs/JavaScript%E7%9A%84%E5%90%8C%E6%BA%90%E7%AD%96%E7%95%A5?redirect=no)

### 编写一个数组去重的方法

```javascript
// 方法1 双重循环，逐个判断数组中某个元素是否与其他元素相同，相同则去掉，并且索引减1，重复执行直到双重遍历完成
function DuplicateRemoval(arr){
    for(var i = 0; i < arr.length-1; i++){
        for(var j = i + 1; j < arr.length; j++){
            if(arr[i] == arr[j]){
                arr.splice(j,1);
                j--;
            }
        }
    }
    return arr;
}
var arr=[1,1,3,4,2,4,7];
console.log(DuplicateRemoval(arr));

// 方法2 借助 indexOf() 方法判断此元素在该数组中首次出现位置下标与循环的下标是否相同
function DuplicateRemoval(arr){
    for(var i = 0; i < arr.length; i++){
        if(arr.indexOf(arr[i]) !== i){
            arr.splice(i,1);
            i--
        }
    }
    return arr;
}
var arr=[1,1,3,4,2,4,7];
console.log(DuplicateRemoval(arr));	

// 方法3 利用数组的方法 filter()
var arr=[1,1,3,4,2,4,7];
var result = arr.filter( (element, index, self) => self.indexOf(element) === index );
console.log(result);

// 方法4 利用新数组 通过indexOf判断当前元素在数组中的索引如果与循环相同则添加到新数组中
var arr=[1,1,3,4,2,4,7];
function Add(arr){
    var result = [];
    for(var i = 0; i < arr.length; i++){
        if(arr.indexOf(arr[i]) === i){
            result.push(arr[i]);
        }
    }
    return result;
}
console.log(Add(arr));

// 方法5 ES6方法 Set() Set函数可以接受一个数组（或类似数组的对象）作为参数，用来初始化。
var arr=[1,1,3,4,2,4,7];
console.log([...new Set(arr)]);
```


### JavaScript 的数据类型都有什么

基本数据类型：String,Boolean,number,undefined,object,Null

引用数据类型：Object（Array,Date,RegExp,Function）

#### **类型检测**

##### **typeof**

`typeof` 运算精度只能是基础类型也就是 `number` ， `string` ，`undefined` ， `boolean` ， `object` ，要注意的是 `null` 和数组使用 `typeof` 运算符得到的也是 `object`

```javascript
console.log(typeof 123); // number
console.log(typeof 'type'); // string
console.log(typeof null); // object
console.log(typeof undefined); // undefined
console.log(typeof true); // boolean
console.log(typeof []); // object
console.log(typeof {}); // object
console.log(typeof function(){}); // function
```


##### **instanceof**

用于判断一个变量是否某个对象的实例,或用于判断一个变量是否某个对象的实例

 `instanceof` 运算符可以精确到是哪一种类型的引用,但 `instanceof`也有一个缺陷就是对于直接赋值的数字，字符串，布尔值以及数组是不能将其识别为`Number`，`String`，`Boolean`，`Array`。

```javascript
console.log(123 instanceof Number); // false
console.log('type' instanceof String); // false
console.log(true instanceof Boolean); // false
console.log([] instanceof Array); // true
console.log({} instanceof Object); // true
console.log(function(){} instanceof Function); // true
```


##### **toString.call()**

```javascript
console.log(toString.call(123) ); // [object Number]
console.log(toString.call('type')); // [object String]
console.log(toString.call(null)); // [object Null]
console.log(toString.call(undefined)); // [object Undefined]
console.log(toString.call(true)); // [object Boolean]
console.log(toString.call([])); // [object Array]
console.log(toString.call({})); // [object Object]
console.log(toString.call(function(){})); // [object Function]
```
##### **constructor**

构造函数的属性 用来判别对创建实例对象的函数的引用，另外 constructor 可以被改写

```javascript
var number = 123,
string = 'type',
boolean = true,
arr = [],
obj = {},
fn = function(){};

console.log( number.constructor); // ƒ Number() { [native code] }
console.log( string.constructor); // ƒ String() { [native code] }
console.log( boolean.constructor); // ƒ Boolean() { [native code] }
console.log( arr.constructor); // ƒ Array() { [native code] }
console.log( obj.constructor); // ƒ Object() { [native code] }
console.log( fn.constructor); // ƒ Function() { [native code] }
```
也可以用 `constructor.name`就会返回函数名

##### **Object.prototype.toString.call()**

这个方法其实跟上面 `toString()`是一样的，只是上面那个方法有可能会被重写。所以用这个比较稳妥，所有对象都包含有一个内部属性[[Class]]，这个属性不能直接访问，但是我们可以通过`Object.prototype.toString.call()`

```javascript
console.log(Object.prototype.toString.call(123) ); // [object Number]
console.log(Object.prototype.toString.call('type')); // [object String]
console.log(Object.prototype.toString.call(null)); // [object Null]
console.log(Object.prototype.toString.call(undefined)); // [object Undefined]
console.log(Object.prototype.toString.call(true)); // [object Boolean]
console.log(Object.prototype.toString.call([])); // [object Array]
console.log(Object.prototype.toString.call({})); // [object Object]
console.log(Object.prototype.toString.call(function(){})); // [object Function]
```


需要注意的是IE6/7/8中 Object.prototype.toString.apply(null)返回“[object Object]”。

### js 中innerText/value/innerHTML三个属性的区别
```html
<div value = "3" id="target3">
    <input type="text" value="1" id="target1">
    <span  id="target2" value="2">span</span>
</div>
<script>
var a = document.getElementById("target1");
var b = document.getElementById("target2");
var c = document.getElementById("target3");
console.log('----------');
console.log(a.value); // 1
console.log(a.innerHTML);
console.log(a.innerText);	
console.log('----------');
console.log(b.value); // undefined
console.log(b.innerHTML); // span
console.log(b.innerText); // span	
console.log('----------');
console.log(c.value); // undefined
console.log(c.innerHTML); 
// <input type="text" value="1" id="target1"><span id="target2" value="2">span</span>
console.log(c.innerText); // span
</script>
```
总结：

1. `innerText`是标签内的文本，输出的是字符串，它可以获得某个节点下面的所有标签内的文本
2. `innerHtml` 可以获得某个DOM 节点内部的 HTML 代码
3. `value`是表单元素的属性，获得是表单元素里面的值

### 希望获取到页面中所有的 checkbox
怎么做？( ( 不使用第三方框架) )

html:

```html
<input type="checkbox">
<input type="checkbox">
<input type="checkbox">
```
```javascript
window.onload = function(){
    var domList = document.getElementsByTagName('input');
    var checkBoxList = []; // 返回所有的 checkbox 
    var len = domList.length; // 缓存到局部变量
    while(len--){
        if(domList[len].type = 'checkbox'){
            checkBoxList.push(domList[len]);
        }
    }
    console.log(checkBoxList);	//  [input, input, input]
}
```



### 当一个Dom节点被点击时候，我们希望能够执行一个函数，应该怎么做？

```html
<!-- 直接在 DOM 里绑定事件：-->
<div onlick = "test()">
```
```javascript
// 在js 里面通过 onclick 绑定
xxx.onclick = test
// 在事件里面添加绑定
addEventListener(xxx, 'click', test)
```

**Javascript 的事件流模型都有什么？**

“事件冒泡”：事件开始由最具体的元素接受，然后逐级向上传播

“事件捕捉”：事件由最不具体的节点先接收，然后逐级向下，一直到最具体的

“DOM 事件流”：三个阶段，事件捕捉，目标阶段。事件冒泡。

### 已知有字符串 foo=”get-element-by-id”,写一个 function 将其转化成驼峰表示法”——getElementById”。
```javascript
var foo = "get-element-by-id";
function combo(msg){
    var arr = msg.split('-');
    for(var i=1;i < arr.length; i++){
        arr[i] = arr[i].charAt(0).toUpperCase()+arr[i].substr(1,arr[i].length-1);
    }
    msg = arr.join('');
    return msg;
}
console.log(combo(foo)); // getElementById
```


### 输出今天的日期，以 YYYY-MM-DD 的方式

```javascript
var date = new Date();
var year = date.getFullYear();
var month = date.getMonth() + 1;
month = month < 10 ? '0' + month : month;
var day = date.getDate();
day = day < 10 ? '0' + day : day;
console.log([year,month,day].join('-')) // 2018-11-01
```
### 将字符串 `&lt;tr&gt;&lt;td&gt;{${$name}&lt;/td&gt;&lt;/tr&gt;`” 中的 `{$id}` 替换 成 10， `{$name}` 替换成 Tony （使用正则表达式）
```javascript
var str = '<tr><td>{$id}</td><td>{$name}</td></tr>';
str.replace(/{$id}/g,10).replace(/{$name}/,'Tony')
```


### 为了保证页面输出安全，我们经常需要对一些特殊的字符进行转义，请写一个函数
```javascript
function escapeHtml(str){
    return str.replace(/[<>”&]/g,function(match){
        switch(match) {
            case "<":
            return '&lt';
            break;
            case ">":
            return '&gt'
            break;
            case "\”":
            return '&quot'
            break;	
            case "&":
            return '&amp'
            break;
        }
    });
}

```
### 用 js 实现随机选择 10 -100 之间的 10
```javascript
var result = [];
function getRandom(start,end){
    var choice = end - start + 1;
    return Math.floor(Math.random() * choice + start);
}
for(var i = 0; i < 10; i++){
    var arr = getRandom(10,100);
    result.push(arr);
}

console.log(result.sort( (x,y) => x - y ));
```


### 把两个数组合并，并删除第二个元素

```javascript
var arr1 = ['a','b','c'];
var arr2 = ['d','e','f'];
var result = arr1.concat(arr2);
result.splice(1,1);
```
### 请写一段 JS  程序提取URL中的各个 GET  参数( 参数名和参数个数不确定 ) ， 将其按key-value  形式返回到一个 json  结构中

```javascript
const url = 'http://item.taobao.com/item.htm?a=1&b=2&c=&d=xxx&e';
function serilizeUrl(url){
    const objUrl = {};
    if(/?/.test(url)){
        const urlStr = url.substring(url.indexOf('?') + 1);
        const urlArr = urlStr.split('&');
        for(let i = 0; i < urlArr.length; i++){
            const urlItem = urlArr[i];
            const item = urlItem.split('=');				
            objUrl[item[0]] = item[1];
        }
        return objUrl;
    }
    return null;
}
console.log(serilizeUrl(url)); // {a: "1", b: "2", c: "", d: "xxx", e: undefined}
```


### 正则构造函数与正则表示字面量

Javascript中的正则表达式也是对象，我们可以使用两种方法创建正则表达式：

* 使用new RegExp()构造函数
* 使用正则表达字面量

先说结果，**使用正则表达字面量的效率更高**。

下面的示例代码演示了两种可用于创建正则表达式以匹配反斜杠的方法：

```javascript
// 正则构造函数与正则表达字面量
var re = /\/gm;

// 正则构造函数
var reg = new RegExp('\\','gm');

var foo = 'abc\123';
console.log(re.test(foo)); // true
console.log(reg.test(foo)); // true

```
如上面的代码中可以看到，使用正则表达式字面量表示法时式子显得更加简短，而且不用按照类似类（class-like）的构造函数方式思考。其次，在当使用构造函数的时候，在这里要使用四个反斜杠才能匹配单个反斜杠。这使得正则表达式模式显得更长，更加难以阅读和修改。正确来说，当使用RegExp()构造函数的时候，不仅需要转义引号（即\”表示”），并且通常还需要双反斜杠（即\表示一个\）。使用new RegExp()的原因之一在于，某些场景中无法事先确定模式，而只能在运行时以字符串方式创建。                 

### 写一个函数，清除字符串前后的空格

使用自带接口 trim()，考虑兼容性：

```javascript
if(String.prototype.trim){
    String.prototype.trim = function(){
        return this.replace(/^\s+/,"").replace(/\s+$/,"");
    }
}

var str = '\t\n test string '.trim();
console.log(str); // test string
```


### JavaScript 中 callee 和 caller的作用？                  
`caller`返回一个函数的引用，这个函数调用了当前的函数

`callee`返回正在执行的函数本身的引用，它是arguments的一个属性

**caller**

使用这个属性要注意：

1. 这个属性只有当函数执行时才有用
2. 如果在js 程序中，函数由顶级调用，则返回 null

```javascript
function fn(){
    console.log(fn.caller);
}
fn(); // null
```

包裹一层

```javascript
function a(){
    fn(); 		
    function fn(){
        console.log(fn.caller);
    }
}
a();


/** 
输出：
ƒ a(){
		fn(); 		
		function fn(){
			console.log(fn.caller);
		}
	}
*/
```


结果为 a 函数。

再包裹一层

```javascript
function a(){
    b();
    function b(){
        fn(); 		
        function fn(){
            console.log(fn.caller);
        }			
    }
}
a();
/** 
输出：
ƒ b(){
			fn(); 		
			function fn(){
				console.log(fn.caller);
			}			
		}
*/
```


结果为 b 函数

**callee**

要注意：

1. 这个属性只有函数执行时才有效
2. 它有一个 `length` 属性，可以用来获得形参的个数，因此可以用来比较形参和实参个数是否一致，即比较`arguments.length`是否等于 `argument.callee.length`
3. 它可以用来传递匿名函数

a 在 b 中被调用了，但是它返回了 a 本身的引用。

下面是一个经典的阶乘函数

```javascript
function factorial(num){
    if(num <= 1){
        return 1;
    }else{
        return num * factorial(num - 1)
    }
}
console.log(factorial(120));
```
为了避免函数名修改导致内部有报错，可以修改成以下函数

```javascript
function factorial(num){
    if(num <= 1){
        return 1;
    }else{
        return num * arguments.callee(num - 1)
    }
}
```


获得形参和实参个数

```javascript
function a(num1,num2,num3){
    console.log(arguments.length); // 实参长度为1
    console.log(arguments.callee.length); // 形参长度为3
}
a(1)
```
### 兔子问题

如果一对兔子每月生一对兔子，一对新生兔子，从第二个月起就开始生兔子，假设每对兔子都是一雌一雄，试问一对兔子，第 N 个 月能繁殖多少成对多少对兔子（使用 callee 完成）

```javascript
function rabbit(num){
    if( num === 1){
        return 1;
    }else if(num === 2){
        return 1;
    }else{
        if(result[num]){
            return result[num];
        }else{
            result[num] = arguments.callee(num-1) + arguments.callee(num-2)
            return result[num];
        }
    }
}
```


### 简述创建函数的几种方式

    // 第一种(函数声明)：
    function sum1(num1,num2){
        return num1 + num2;
    }
    // 第二种（函数表达式）
    var sum2 = function(num1,num2){
        return num1 + num2;
    }
    // 匿名函数：只能自己执行自己
    function(){} 
    // 第三种（函数对象方式）
    var sum3 = new Function("num1","num2","return num1 + num2");


### 线程与进程

* 进程是 cpu 资源分配的最小单位，例如一个工厂

* 线程是 cpu 调度的最小单位，例如一个工厂里面的n个工人

#### **浏览器内核**

浏览器内核中有多个进程在同步工作，今天涉及到的浏览器的进程主要有以下进程：

Brower 进程、Render 进程。

**Brower**

主进程，主要负责页面管理以及管理其他进程的创建和销毁等，常驻的进程有：

* GUI 渲染线程
* JS 引擎线程
* 事件触发线程
* 定时器触发线程
* HTTP 请求线程

GUI 渲染线程

* 主要负责页面的渲染，解析 HTML、CSS 、构建 DOM 树、布局和绘制等
* 当界面需要重绘或者由于某种引发回流的时候，将执行该线程
* 该线程与 JS 引擎线程互斥，当执行 JS 引擎线程时，GUI 渲染会被挂起，当任务队列空闲时，JS 引擎才会去执行 GUI 渲染

JS 引擎线程

* 该线程当然是主要负责处理 JavaScript 脚本，执行代码
* 也是主要执行准备好待执行的事件，即定时器计数结束，或者异步请求成功并返回正确时，将依次进入任务队列，等 JS 引擎线程执行
* 当然，该线程与 GUI 渲染线程互斥，当 JS 引擎线程执行 JavaScript脚本时间过长，将导致页面渲染阻塞

事件触发线程

* 主要负责将准备好的事件交给 JS 引擎线程执行
* 比如 setTimeout 定时器计数结束，ajax 等异步请求成功并触发回调函数，或者用户触发点击事件时，该线程会将整装待发的事件依次加入任务队列的队尾，等待 JS 引擎线程的执行。

定时器触发线程

* 负责执行异步定时器一类的线程，如 setTimeout ，setInterval
* 主线程将依次执行代码时，遇到定时器，会将定时器交给该线程处理，当计数完毕后，事件触发线程会将计数完毕后的事件加入到任务队列的尾部，等 JS 引擎线程执行

HTTP 请求线程

* 负责执行异步请求一类的函数请求，如 Promise、axios、ajax 等
* 主线程一次执行代码，遇到异步请求，会将函数交给该线程处理，当监听到状态码的改变，如果有回调函数，事件触发线程会将回调函数加入到任务队列的尾部，等待 JS 引擎线程执行

**Render 进程**

浏览器渲染进程（浏览器内核），主要负责页面的渲染、JS 执行以及事件的循环。

### JS 实现一个简单的 Promise

```javascript
const PENDING = 0;
const RESOLVED = 1;
const REJECTED = 2;
function myPromise(func){
    // 保存 状态（PENDING|FULFILLED|REJECTED ）
    let state = PENDING;
    // 保存 完成后的值或者失败后的错误
    let value = null;
    // 保存 调用成功后的回调或者失败的回调
    let handlers = [];
    function resolve(newValue){	
        if(newValue && (typeof newValue === 'object' || typeof newValue === 'function')){
            let then = newValue.then;
            if(typeof then === 'function'){
                return then.call(newValue,resolve);
            }
        }
        value = newValue;
        state = RESOLVED;
        setTimeout(()=>{
            handlers.forEach(handler => handle(handler));
        },0)				
    }
    function reject(err){
        value = err;
        state = REJECTED;
    }
    function handle(handler){
        if(state === PENDING){
            handlers.push(handler);
            return;
        }
        // console.log(handler.onFullFill);
        // console.log(value);
        handler.resolve(handler.onFullFill(value));
    }
    this.then = function(onFullFill,onReject){
        return new myPromise((resolve,reject) =>{
            handle({
                resolve:resolve,
                onFullFill:onFullFill
            })
        })
    }
    func(resolve,reject);
}
```
### window.onload 和 document.onDOMContentLoaded 有什么区别？

#### 具体文档

[load](https://developer.mozilla.org/zh-CN/docs/Web/Events/load)

[DOMContentLoaded-MDN](https://developer.mozilla.org/zh-CN/docs/Web/Events/DOMContentLoaded)

#### 概括

onload 事件是 DOM 事件，而 onDOMContentLoaded 是 HTML5 事件

onload 事件会被样式表、图像和自框架阻塞，而 onDOMContentLoaded 事件不会。

当加载的脚本内容并不包含立即执行DOM 操作的时候，使用 onDOMContentLoaded 事件是个更好的选择，会比 onload 事件执行时间更早

### 创建10个标签，并弹出对应的序号

```javascript
    for (var i = 0; i < 10; i++) {
      (function (i) {
        var a = document.createElement('a');
        a.innerHTML = i+'<br/>';
        a.addEventListener('click', function (e) {
          console.log(i)
        });
        document.body.appendChild(a);
      })(i)
    }
```

### CSS 与 JS 的阻塞问题

- `CSS` 不会阻塞 `DOM` 的解析。但是会阻塞 `DOM` 的渲染
- `JS` 阻塞 `DOM` 解析，但是浏览器会 “偷看” `DOM`,预先下载相关资源
- 浏览器遇到 `script`且没有 `defer`或者 `async`属性的标签的时候，会触发页面渲染，所有如果前面 `CSS`资源还没有加载完毕时，浏览器会等待它加载完毕再执行脚本。
- `script`最好放底部， `link`放头部，如果头部同时拥有这两个，`script`最好放在`link`前面

### 参考链接：

1. [JS类型转换(强制和自动的规则)](https://www.cnblogs.com/Juphy/p/7085197.html)
2. [JavaScript 数据类型转换（显式与隐式）](https://www.cnblogs.com/tongtian17/p/6265414.html)
3. [DOM标准与IE的html元素事件模型区别](http://www.cnblogs.com/ilexcai/archive/2011/09/05/2168094.html)
4. [ie和dom事件流的区别](https://www.cnblogs.com/luckyXcc/p/5862759.html)
5. [前端面试题——call与apply方法的异同](https://www.jianshu.com/p/131ce0390cf8)
6. [JavaScript中B继承A的方法](https://www.cnblogs.com/fwei/p/6475973.html)[Me丶微笑](https://www.cnblogs.com/fwei/)
7. [Javascript 中 作用域、闭包与 this指针](https://blog.csdn.net/junbo_song/article/details/52261247)
8. [理解JAVASCRIPT的闭包](https://coolshell.cn/articles/6731.html)
9. [深入理解JS中的变量作用域](https://blog.csdn.net/bbs375/article/details/52483862)
10. [解决js函数闭包内存泄露问题的办法](https://www.jb51.net/article/78597.htm)
11. [js中的事件委托或是事件代理详解](https://www.cnblogs.com/liugang-vip/p/5616484.html)
12. [JavaScript捕获和冒泡探讨](http://caibaojian.com/javascript-capture-bubble.html)
13. [JS阻止冒泡和取消默认事件(默认行为)](http://caibaojian.com/javascript-stoppropagation-preventdefault.html)
14. [javascript原生方法对dom节点的操作，创建、添加、删除、替换、插入、复制、移动等操作](https://blog.csdn.net/hxfdarling/article/details/40347207)
15. [JavaScript中本地对象、内置对象和宿主对象](https://www.cnblogs.com/luckyXcc/p/5892896.html)
16. [js中==和===区别](https://www.cnblogs.com/nelson-hu/p/7922731.html)
17. [同源策略与JS跨域请求(图文实例详解)](https://blog.csdn.net/shuai_wy/article/details/51186956)
18. [Js数组去重方法总结](https://www.cnblogs.com/guangyan/articles/6682686.html)
19. [javascript 六种数据类型（一）](https://www.cnblogs.com/starof/p/6368048.html)
20. [Javascript正则构造函数与正则表达字面量&amp;&amp;常用正则表达式](https://www.cnblogs.com/coco1s/p/4008955.html)
21. [js中的caller与callee用法小实例](https://blog.csdn.net/qq_17335153/article/details/52575064)
22. [caller和callee的区别](https://blog.csdn.net/laijieyao/article/details/43404953)
23. [事件循环机制的那些事](https://mp.weixin.qq.com/s?__biz=MzAxODE2MjM1MA==&amp;mid=2651555331&amp;idx=1&amp;sn=5a063db73329a9d8ea5c38d8eeb50741&amp;chksm=802551c2b752d8d4020514337f9987cc6611ba878525c45507cbb9ceb52b92e060f50e76c48d&amp;mpshare=1&amp;scene=1&amp;srcid=1115P2jpgUGueMlTaRe9UFYu#rd)
24. [十几行代码教你实现一个最简版的promise](https://segmentfault.com/a/1190000009809466)
25. [window.onload 和 document.onDOMContentLoaded 有什么区别？](https://www.jianshu.com/p/d5cedce95acc)
26. [原来 CSS 与 JS 是这样阻塞 DOM 解析和渲染的](https://juejin.im/post/59c60691518825396f4f71a1)