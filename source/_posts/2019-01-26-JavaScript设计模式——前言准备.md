---
title: JavaScript设计模式——前言准备
date: 2019-01-28 18:30:00
tags: JavaScript设计模式
categories: 
	- JavaScript设计模式

---

学习曾探的 《JavaScript设计模式与开发实践》并做记录。

[书籍的购买链接](http://www.ituring.com.cn/book/1632/)

设计模式的定义是：在面向对象软件设计过程中针对特定问题的简洁而优雅的解决方案。

通俗一点，设计模式是在某种场合下对某个问题的一种解决方案。是给面向对象软件开发中的一些好的设计取个名字。

# JavaScript设计模式——前言准备

在开始之前，复习一下闭包和高阶函数。

## 闭包和高阶函数

虽然，JavaScript 是一门完整的面向对象的编程语言，但这门语言同时也有许多函数式语言的特性。

函数式语言的鼻祖是 LISP ，JavaScript 在设计之初参考了 LISP 两大方言之一的 Scheme ，引入了 Lambda 表达式、闭包、高阶函数等特性。使用这些特性，可以用一洗灵活而巧妙的方式来编写 JavaScript 代码。

JavaScript 很多模式都用到闭包和高阶函数来实现.

## 闭包

闭包的形成与变量的作用域已经变量的生存周期密切相关。

### 变量的作用域

变量的作用域，就是指变量的有效范围，我们最常说的就是在函数声明的变量作用域。

当在函数中声明一个变量的时候，如果该变量前面没有加上 var 关键字，这个变量就会变成全局变量。

另外一种情况是用 var 关键字在函数声明变量，这时候的变量即是局部变量，只有在该函数内部才能访问到这个变量，在函数外面是访问不到的。

```javascript
var func = function(){
    var a = 1;
    console.log(a); // 输出：1
}
func(); 
console.log(a); // Uncaught ReferenceError: a is not defined
```

搜索的过程会随着代码的执行环节创建的作用域链往外层逐层搜索，一直到全局对象为止。变量的搜索是从内到外。

尝试得出下面代码的答案，可以加深对变量搜索过程的理解：

```javascript
var a = 1;
var func = function(){
    var b = 2;
    var func2 = function(){
        var c = 3;
        console.log(b);
        console.log(a);
    }
    func2();
    console.log(c);
}
func();
```

### 变量的生存周期

除了变量的作用域之外，另外一个跟闭包有关的概念是变量的生存周期。

对于全局变量来说，全局变量的生存周期是永久的，除非我们主动销毁这个全局变量。而对于在函数内用 var 关键字声明的局部变量来说，当退出函数时，这些局部变量即失去了它们的价值，它们会随着函数调用的结束而被销毁：

```javascript
var func = function(){
    var a = 1;
    console.log(a); // 退出函数后，局部变量 a 将被销毁
}
func();

// 下一个例子
var func = function(){
    var a = 1;
    return function(){
        a++;
        console.log(a);
    }
}
var f = func();
f(); // 2
f(); // 3
f(); // 4
f(); // 5
/*
跟上面的结论相反，这是因为当执行了 var f = func() 时候，f 返回了一个匿名函数的引用，它可以访问到 func 被调用时产生的环境，而局部变量一直在这个环境中。既然局部变量所在的环境还能被外界访问，这个局部变量就有了不变销毁的理由。在这里就产生了一个闭包结构，局部变量的声明看起来延续了。
*/
```

闭包的经典案例：

```javascript
/*
假设页面上有5个 div 节点，我们通过循环来给每个 div 绑定 onclick 时间，按照索引的 顺序，点击第一个 div 演出0，第二个弹出1，以此类推
*/
var nodes = document.getElementsByTagName('div');
for(var i = 0; i < nodes.length; i++){
    (function(i){
        nodes[i].onClick = function(){
            console.log(i);
        }
    })(i);
}
```

同样的道理，还有下面的例子：

```javascript
var Type = {};
for(var i = 0; type; type = ['String','Array','Numebr'][i++]){
    (function(type){
        Type["is"+type] = function(obj){
            return Object.prototype.toString.call(obj) === "[Object"+type+"]";
        }
    })(type);
}

Type.isArray([]); // true
Type.isString("str"); // true
```

### 闭包的更多作用

#### **1.封装变量**

闭包可以帮助把一些不需要暴露在全局的变量封装成 “私有变量”。假设有一个计算乘积的简单函数：

```javascript
var mult = function(){
    var a = 1;
    for(var i = 0; l = arguments.length; i < l; i++){
        a  = a* arguments[i]
    }
    return a;
}
/*
	mult 函数接受一些 number 类型的参数，并返回这些参数的乘积，我们觉得对于那些相同的参数来说，每次进行计算是一种浪费，我们可以加入缓存机制来提高这个函数的性能：
*/
var cache = {};
var mult = function(){
    var args = Array.prototype.join.call(arguments,',');
    if(cache[args]){
        return cache[args];
    }
    var a = 1;
    for(var i = 0; l = arguments.length; i < l; i++){
        a  = a* arguments[i]
    }
    return  cache[args] = a;
}
/*
可以看到 cache 这个变量仅仅在 mult 函数内部使用，与其让 cache 变量跟 mult 函数一起平行地暴露在全局作用域下，不如把它封装在 mult 函数内部。可以减少页面中的全局变量，以避免这个变量在其他地方被不小心修改了而引发错误。改进的如下：
*/
var mult = (function(){
    var cache = {};
    return function(){        
        var args = Array.prototype.join.call(arguments,',');
        if(cache[args]){
            return cache[args];
        }
        var a = 1;
        for(var i = 0; l = arguments.length; i < l; i++){
            a  = a* arguments[i]
        }
        return  cache[args] = a; 
    }
})();
```

提炼函数是代码重构的一个常见的技巧，如果在一个大函数中有一些代码能够独立开来，我们常常把这些代码封装在独立的小函数里面。独立出来的小函数有助于代码复用，如果这些小函数有一个良好的命名，它们本身也起到了注释的作用。如果这些小函数不要在程序中其他地方使用，最好用闭包把它们封装起来，如下：

```javascript
var mult = (function(){
    var cache = {};
    var calculate = function(){ // 封闭 calculate 函数
        var a = 1;
        for(var i = 0; l = arguments.length; i < l; i++){
            a  = a* arguments[i]
        }
        return a;
    }
    return function(){
        var args = Array.prototype.join.call(arguments,',');
        if(cache[args]){
            return cache[args];
        }
        return cache[args] = calculate.apply(null,arguments);
    }
})();
```

#### **2.延续局部变量的寿命**

img 对象经常用于进行数据上报，如下所示：

```javascript
var report = function(src){
    var img = new Image();
    img.src = src;
}
report('http://laibh.top/getUserInfo');
```

但是通过查询后台的记录我们得知，因为一些低版本浏览器的实现存在 bug，在这些浏览器下使用 report 函数进行数据上报会丢失 30%左右的数据，也就是说，report 函数并不是每一次都成功发起了 HTTP 请求。

丢失数据的原因是 img 是 report 函数中的局部变量，当 report 函数的调用结束，img 局部变量也会被销毁，而此时或者还没有来得及发出 	HTTP 请求，所以此次请求就会丢失了。

现在我们把 img 变量用闭包封闭起来，便能解决请求丢失的问题：

```javascript
var report = (function(){
    var imgs = [];
    return function(src){        
        var img = new Image();
        imgs.push(img);
        img.src = src; 
    }
})();
```

### 闭包和面向对象设计

过程与数据的结合是 形容面向对象中的“对象”经常使用的表述，对象以方法的形式包含了过程，而闭包则是在过程中以环境的形式包含了数据。通常用面向对象能实现的功能，用闭包也可以实现。反之亦然，在 JavaScript的祖先语言 Scheme 中，甚至都没有提供面向对象的原生设计，但可以用 闭包来实现一个完整的面向对象系统。

```javascript
var extent = function(){
    var value = 0;
    return{
        call: function(){
            value++:
            console.log(value);
        }
    }
}
var extent = extent();
extent.call(); // 1
extent.call(); // 2
extent.call(); // 3

/*
对象的写法：
*/
var extent = {
    value:0,
    call:function(){
        this.value++;
        console.log(this.value);
    }
}
extent.call(); // 1
extent.call(); // 2
extent.call(); // 3

/*
或者：
*/
var Extent = function(){
    this.value = 0;
}
Extent.prototype.call = function(){
    this.value ++;
    console.log(this.value);
}
var extent = new Extend();
extent.call(); // 1
extent.call(); // 2
extent.call(); // 3
```

### 用闭包实现命令模式

在完成闭包实现的命令模式之前，我们先用面向对象的方式来编写一段命令模式的代码

```html
<html>
    <body>
        <button id="execute">点击我执行命令</button>
        <button id="undo">点击我执行命令</button>
    </body>
</html>
```

```javascript
var Tv = {
    open:function(){
        console.log("打开电视机")
    },
    close:function(){
        console.log("关闭电视机")
    },    
}

var OpenTvCommand = function(receiver){
    this.receiver = receiver;
}
OpenTvCommand.prototype.execute = function(){
    this.receiver.open(); // 执行命令，打开电视机
}
OpenTvCommand.prototype.undo = function(){
    this.receiver.colse(); // 执行命令，关闭电视机
}

var setCommand = function(command){
    document.getElementById('execute').onclick = function(){
        command.execute(); // 打开电视机
    }
    document.getElementById('undo').onclick = function(){
        command.undo(); // 关闭电视机
    }    
}
setCommand(new OpenTvCommand(Tv));
```

命令模式的意图是把请求封装为对象，从而分离请求的发起者和请求的接受者（执行者）之间的耦合关系，在命令被执行之前，可以预先往命令对象中植入命令的接受者。

但在 JavaScript 中，函数作为一等对象，本身就可以四处传递，用函数对象而不是普通对象来封装请求显得更加简单和自然。如果需要往函数对象中预先植入命令的接受者，那么闭包可以完成这个工作。在面向对象版本的命令模式中，预先植入的命令接受者被当成对象的属性保存起来，而在闭包版本的命令模式中，命令接受者会被封闭在闭包形成的环境中，代码如下：

```javascript
var Tv = {
    open:function(){
        console.log("打开电视机")
    },
    close:function(){
        console.log("关闭电视机")
    }   
}
var createCommand = function(receiver){
    var execute = function(){
        return receiver.open();
    }
    var undo = function(){
        return receiver.close();
    }  
    return {
        execute,undo
    }
}
var setCommand = function(command){
    document.getElementById('execute').onclick = function(){
    	command.execute(); // 打开电视机
    }
    document.getElementById('undo').onclick = function(){
        command.undo(); // 关闭电视机
    }  
}

setCommand(createCommand(Tv));
```

### 闭包与内存管理

闭包是一个非常强大的特性，但是人们对这个也有诸多误解，一种耸人听闻的说法是闭包会造成内存泄露，所以要尽量减少闭包的使用。

局部变量本来应该在函数退出的时候被解除引用，但如果局部变量被封闭在闭包形成的环境中，那么这个局部变量就能一直生存下去。从这个意义上来看，闭包的确会使得一些数据无法被及时销毁。使用闭包的一部分原因是我们选择主动把一些变量封闭在闭包中，因为可能在以后还需要使用这些变量，把这些变量放在闭包中和全局作用域，对内存方法的影响是一致的，这里不能说成内存泄露，如果在将来需要回收这些变量，我们可以手动把这些变量设置为 null。

跟闭包和内存泄露有关系的是，使用闭包的同时比较容易形成循环引用，如果闭包的作用域链中保存着一些 DOM 节点，这时候就可能造成内存泄露。但这并非闭包的问题，也并非 JavaScript 的问题，在 IE 浏览器中，由于 BOM 和 DOM 中的对象是使用 C++ 以 COM 对象的方式实现的，而 COM 对象的垃圾收集机制采用的是引用计数策略。在基于引用计数策略的垃圾回收机制中，如果两个对象之间形成了循环引用，那么这两个对象都无法被回收，但循环引用造成的内存泄露在本质上也不是闭包造成的。

同样，如果要解决循环引用带来的内存泄露的问题，我们只需要把循环引用中的变量设为 null，将变量设为 null 以为着切断变量与它此前的引用的值连接。当垃圾收集器下次运行时，将会删除这些值并回收它们占用的内存。

## 高阶函数

高阶函数是指至少满足下列条件之一的函数

- 函数可以作为参数被传递
- 函数可以作为返回值输出

JavaScript 语言中的函数显然满足高阶函数的条件，在实际开发中，无论是将函数当做参数传递，还是让函数的执行结果返回一个函数，这两种场景都有很多应用场景。

### 函数作为参数传递

把函数作为参数传递，这代码我们可以抽离出一部分容易变化的业务逻辑，把这部分业务逻辑放在函数形参中，这样一来就可以分离业务中代码变化与不变的部分，其中一个重要的场景就是在常见的回调函数中。

#### **1.回调函数**

在 ajax 异步请求的应用中，回调函数的使用非常频繁，当我们想在 ajax 请求返回之后做一些事情，但又并不知道请求返回的确切时间时，最常见的方案就是把 callback 函数当做参数传入发起 ajax请求的方法中，待请求完成之后执行 callback 函数：

```javascript
var getUserInfo = function(userId,callback){
    $.ajax("http://laibh.top/getUserInfo?"+userId,function(data){
        if(typeof callback === 'function'){
        	callback(data);   
        }
    });
}
getUserInfo(12464,function(data){
    console.log(data.userName);
});
```

回调函数的应用不仅只在异步请求中，当一个函数不适合执行一些请求时，我们也可以把这些请求封装成一个函数，并把它作为参数传递给另一个函数，“委托”给另外一个函数来执行。

比如，我们想在页面中创建100 个 div 节点，然后把这些节点都设置为隐藏，下面是一个方法：

```javascript
var appendDiv = function(){
    for(var i = 0; i< 100; i++){
        var div = document.createElement('div');
        div.innerHTML = i;
        document.body.appendChild(div);
        div.style.display = 'none';
    }
}
appendDiv();
/*
把 div.style.display = 'none' 的逻辑硬编码在  appendDiv 中显然是不合理的，appendDiv 未免有点个性化，称为了一个难以复用的函数，并不是每个人创建了节点之后就希望它们立即被隐藏，于是我们这这段代码抽出来，用回调函数的形式传入 appendDiv 中
*/

var appendDiv = function(callback){
    for(var i = 0; i< 100; i++){
        var div = document.createElement('div');
        div.innerHTML = i;
        document.body.appendChild(div);
        if(typeof callback === 'function'){
        	callback(div);   
        }                
    }
}
appendDiv(function(node){
    node.style.display = 'none';
});
/*
可以看到隐藏节点的请求实际上是由客户发起的，但是客户并不知道节点什么时候会创建好，于是把隐藏节点的逻辑放在回调函数中，”委托“给 appendDiv 方法，appenDiv 方法当然知道节点什么时候创建好，所以在节点创建好的时候，appendDiv 会执行之前客户传入的回调函数。
*/
```

#### **2.Array.prototype.sort**

Array.prototype.sort 可以接受一个函数作为参数，这个函数里面封装了数组元素的排序规则。从 Array.prototype.sort 的使用可以看到，我们目的是对数组进行排序，这是不变的部分，而使用什么规则去排序，则是可变的部分。把可变的部分封装在函数参数里，动态传入 Array.prototype.sort ，使得 Array.prototype.sort 方法成为一个灵活的方法，代码如下：

```javascript
// 从小到大排序
[1,4,3].sort(function(a,b){
    return a - b;
});

// 从大到小排序
[1,4,3].sort(function(a,b){
    return b - a;
});
```

### 函数作为返回值输出

相比把函数作为参数传递，函数作为返回值输出的应用场景也很多。也能体现函数式编程的巧妙，让函数继续返回一个可执行的函数，意味着运算过程是可以延续的。

#### **1.判断数据类型**

 判断一个数据是否是数组，在以往的现实中，可以基于鸭子类型的概念来判断，比如判断这个数据有没有 length 属性，有没有 sort 或者是 slice 方法等。但更好的方式是用 Object.prototype.toString 来计算。Object.prototype.toString.call(obj) 返回一个字符串，比如 Object.prototype.toString.call([1,2,3]) 总是返回 “[Object Array]” ，而 Object.prototype.toString.call("str"); 总是返回 “[Object String]”。所以我们可以编写一个 isType 函数，代码如下：

```javascript
var isString = function(obj){
    return Object.prototype.toString.call(obj) === '[Object String]';
}
var isArray = function(obj){
    return Object.prototype.toString.call(obj) === '[Object Array]';
}
var isNumber = function(obj){
    return Object.prototype.toString.call(obj) === '[Object Number]';
}
/*
上面的代码中，函数的实现大部分都是一样的，不同的只是 Object.prototype.toString.call(obj) 返回的字符串，为了避免多余的代码。我们尝试把这些字符串作为参数提前植入 isType 函数。
*/

var isType = function(type){
    return function(obj){
        return Object.prototype.toString.call(obj) === "[Object" +type+ "]";
    }
}
var isString = isType("String");
var isArray = isType("Array");
var isNumber = isType("Number");
console.log(isArray([1,2,3])); // true

/*
还可以使用循环来注册这些 isType 函数
*/

var Type = {}
for(var i = 0; type; type = ['String','Array','Number'];i++){
    (function(type){
        Type['is'+type] = function(obj){
            return Object.prototype.toString.call(obj) === "[Object" +type+ "]";
        }
    })(type)
}
Type.isArray([1,2,3]); // true
```

#### **2.getSingle**

下面是一个单例模式的例子，这里暂且只了解其代码实现：

```javascript
var getSingle = function(fn){
    var ret;
    return function(){
        return ret || (ret = fn.apply(this,arguments))
    }
}
```

上面这个高阶函数的例子，既把函数当作参数传递，又让函数执行后返回了另外一个函数，我们可以看看 getSingle 函数的效果

```javascript
var getScript = getSingle(function(){
    return document.createElement('script');
});
var script1 = getScript();
var script2 = getScript();

console.log(script1 === script2); // true
```

### 高阶函数实现 AOP

AOP（面向切面编程）的主要作用是把一些核心业务逻辑无关的功能抽离出来，这些跟业务逻辑无关的功能通常包括日志统计、安全控制、异常处理等。把这些功能抽离分来之后，再通过“动态织入”的方式掺入业务逻辑模块中。这样做的好处是首先是可以保持业务逻辑模块的纯净和高内聚性，其次是可以方便地复用日志统计等功能模块。

在 Java 语言中，可以通过反射和动态代理机制来实现 AOP 技术。而在 JavaScript 这种动态语言中，AOP 更加简单，这是 JavaScript 与生俱来的能力。

通常在 JavaScript 中实现 AOP 是指把一个函数 “动态织入”到另一个函数之中，具体的实现技术很多，本节通过扩展 Function.prototype 来做到这一点。代码如下：

```javascript
Function.prototype.before = function(beforefn){
    var _self = this; // 保存原函数的引用
    return function(){ // 返回包含了原函数和新函数的“代理函数”
        beforefn.apply(this,arguments); // 执行新函数，修正 this
        return _self.apply(this,arguments); // 执行原函数
    }
}
Function.prototype.after = function(afterfn){
    var _self = this;
    return function(){
        var ret = _self.apply(this,arguments);
        afterfn.apply(this,arguments);
        return ret;
    }
}

var func = function(){
    console.log(2);
}
func = func.before(function(){
    console.log(1);
}).after(function(){
    console.log(3);
});
func();           
// 1 
// 2
// 3
```

 这种使用 AOP 的方式来给函数添加职责，也是 JavaScript 语言中一种非常也别和巧妙的装饰者模式。这种装饰着模式在实际开发中非常有用。

### 高阶函数的其他应用

#### **1.currying**

函数柯里化（function currying）。currying 的概念是由俄国数学家 Moses Schönfinkel 发明，而后由著名的数理逻辑学家 Haskell Curry 将其丰富和发展，currying 由此得名。

currying 又称为部分求值，一个 currying 的函数首先会接受一些参数，接受了这些参数之后，该函数不会立即求值，而是继续返回另外一个函数，刚才传入的参数在函数形成的闭包中被保存起来。待到函数被真正需要求值的时候，之前传入的所有参数都会被一次性用于求值。

假设我们要编写一个计算每月开销的函数，在每天结束之前，我们都要记录今天花掉了多少钱，代码如下：

```javascript
var monthlyCost = 0;
var cost = function(money){
    monthlyCost += money;
}
cost(100); // 第 1 天开销
cost(100); // 第 2 天开销
cost(300); // 第 3 天开销

console.log(monthlyCost); // 输出开销：500

/*
其实我们并不太关心每天花掉了多少钱，而是只想知道月底的时候花掉了多少钱，也就是说，实际上只需要在月底计算一次。
*/

var cost = (function(){
    var args = [];
    return function(){
        if(arguments.length === 0){
           var money = 0;
            for(var i = 0; i < args.length; i++){
                money += args[i];
            }
            return money;
        }else{
            [].push.apply(args,arguments);
        }
    }
})();

cost(100); // 未真正求值
cost(100); // 未真正求值
cost(300); // 未真正求值

console.log(cost()); // 输出开销：500
```

下面是一个通用的  function currying(){} 。接受一个参数，即将要被 currying 的函数，在这个例子中，这个函数的作用遍历本月每天的开销并求出它们的总和。

```javascript
var currying = function(fn){
    var args = [];
    return function(){
        if(arguments.length === 0){
        	return fn.apply(this,args);
        }else{
            [].push.apply(args,arguments);
            return arguments.callee;
        }
    }
}

var cost = (function(){
    var money = 0;
    return function(){
        for(var i = 0; i < arguments.length; i++){
            money += arguments[i]
        }
        return money;
    }
})();

var cost = currying(cost); // 转换为 curring 函数

cost(100); // 未真正求值
cost(100); // 未真正求值
cost(300); // 未真正求值

console.log(cost()); // 输出开销：500
```

当调用 cost 函数时，如果明确地带上了一些参数，表示该函数此时并不是真正的求值计算，而是把这些参数保存起来，此时让 cost 函数返回另一个函数。只有当我们可以不带参数的形式执行 cost 时，才利用前面保存的采纳数，真正开始进行求值计算。

#### **2.uncurrying**

在 JavaScript 中，当我们调用对象的某个方法的时候，其实不用关系该对象原本是否被 设计为拥有这个方法，这是动态类型语言的特点，也就是常说的鸭子类型。

同理，一个对象也未必只能使用它自身的方法，那么有什么办法可以让对象去借用一个原本不属于它的方法呢？

答案很简单，apply 和 call 都可以。

```javascript
var obj1 = {
    name:'sven'
}

var obj2 = {
    getName:function(){
        return this.name;
    }
}
console.log(obj2.getName.call(obj1)); // sven
```

我们常常让类数组对象去借用 Array.prototype 的方法，这是 call 和 apply 最常见的引用场景之一：

```javascript
(function(){
    Array.prototype.push.call(arguments,4); // arguments 借用 Array.protytype.push 方法
    console.log(arguments);
})(1,2,3);
```

在我们预期中，Array.prototype 上的方法原本只能用来操作 array 对象，但用 call 和 apply 可以把任意对象当做 this 来传入某个方法，这样一来，方法中用到的 this 的方法就不再局限于原来规定的对象了，而是加以泛化并得到更广的适用性。

那么有没有把泛化 this 的过程提取出来呢？uncurrying 就是来解决这个问题的。uncurrying 的话题是 JavaScript 之父 Brendan Eich 在 2011 年发表的一篇 Twitter。以下代码是 uncurrying 的实现方式之一：

```javascript
Function.prototype.uncurrying = function(){
    var self = this;
    return function(){
        var obj = Array.prototype.shift.call(arguments);
        return self.apply(obj,arguments);
    }
}
```

类数组对象 arguments 借用 Array.prototype 的方法之前，先把 Array.prototype.push.call() 这句代码转换为一个通用的 push 函数：

```javascript
var push = Array.prototype.push.uncurrying();
(function(){
    push(arguments,4);
    console.log(arguments);
})(1,2,3);
// [1,2,3,4]
```

通过 uncurrying 的方式，Array.prototype.push.call 变成了一个通用的 push 函数，这样一来，push 函数的作用就跟 Array.prototype.push 一样了，同样不仅仅局限于只能操作 array 对象。而对于使用者来说，调用 push 函数的方式也显得更加简洁和意图明了了。

我们还可以一次性将 Array.prototype 上的方法“复制”到 array 对象，同样这些方法可操作的对象也不仅仅是 array 对象

```javascript
for(var i = 0; fn, ary = ['push','shift','forEach'];fn = ary[i++]){
    Array[fn] = Array.prototype[fn].currying();
}

var obj = {
    "length":3,
    "0":1,
    "1":2,
    "2":3
}

Array.push(obj,4); 
console.log(obj.length); // 4

var first = Array.shift(obj);
console.log(first); // 1
console.log(obj); // {0:2,1:3,2:4,length:3}

Array.forEach(obj,function(i,n){
    console.log(n);
});
// 0
// 1
// 2
```

甚至 Function.prototype.call 和 Function.prototype.apply 本身也可以被 uncurrying ,不过这并没有实用价值，只是使得对函数的调用看起来更像是 JavaScript 语言的前身 Scheme

```javascript
var call = Function.prototype.call.uncurrying();
var fn = function(name){
    console.log(name);
}
call(fn,window,'sven'); // sven
var apply = Function.prototype.apply.uncurrying();
var fn = function(name){
    console.log(name);
    console.log(arguments);
}

apply(fn,{name:'sven'},[1,2,3]);
```

uncurrying 的另外一种实现方式：

```javascript
Function.prototype.uncurrying = function(){
    var self = this;
    return function(){
        return Function.prototype.call.apply(self,arguments);
    }
}
```

#### **3.函数节流**

JavaScript 中函数大多数情况下都是由用户主动调用触发的，除非是函数本身的实现不合理，否则我们不会遇到跟性能相关的问题。但是在一些少数情况下，函数的触发不是由用户直接控制的。在这些场景下，函数有可能会被非常频繁地调用，而造成很大的性能问题。下面列举一些场景。

（1）函数被频繁调用的场景

- window.onresize 事件。给 window 对象绑定了这个事件后，当浏览器窗口大小被拖动而改变的时候，这个事件的触发频率很高，如果我们在 window.onresize 事件函数里面有一些跟 DOM 节点相关的操作，而跟 DOM 节点相关的操作往往是非常消耗性能的，这时候浏览器就有可能会吃不消而造成卡顿的现象。
- mousemove 事件。同样我们给一个 div 节点绑定了拖曳事件（主要是 mouseover）,当 div 节点被拖动的时候，也会频繁触发该拖曳事件函数
- 上传进度，微云的上传功能使用了一个浏览器插件。该浏览器插件在真正开始上传文件之前，会对文件进行扫描并通知 JavaScript 函数，以便在页面中显示当前的扫描进度。但该插件通知的频率非常之高，大约1秒10次，很显然我们在页面中不需要如此频繁地去提示用户。

（2）函数节流的原理

函数节流的是实现有很多种，下面的 throttle 函数的原理是，将即将要被执行的函数用 setTimeout 延迟一段时间执行。如果该延迟执行还没有完成，则忽略接下来调用该函数的请求。throttle 接受两个参数，第一个为需要被延迟执行的函数，第二个参数是为延迟执行的时间。

```javascript
var throttle = function(fn,interval){
    var _self = fn, // 保存需要被延迟执行的函数引用
        timer, // 定时器
        firstTime = true; // 是否第一次调用
    return function(){
        var args = arguments,
            _me = this;
        if(firstTime){ // 如果是第一次调用，不需要延迟执行
        	_self.apply(_me,args);
            return firstTime = false;
        }
        timer = setTimeout(function(){ // 延迟一段时间执行
            clearTimeout(timer);
            timer = null;
            _self.apply(_me,args);
        },interval || 500);
    }
}

window.onresize = throttle(function(){
    console.log(1);
},500);
```

#### **4.分时函数**

上面提供了一种限制函数被频繁调用的解决方案，但是还有另外一个问题，某些函数是用户主动调用的，但是这些函数有时候会严重影响页面性能。

一个例子就是创建 WebQQ 的 QQ 好友列表，列表中通常有很多个好友，如果一个好友用一个节点来表示，当我们在页面中渲染这个列表的时候，可能一次性要创建成百上千个节点。

在短时间内页面大量添加 DOM 节点显然会让浏览器吃不消，那么可能浏览器会卡死或者是卡顿。

```javascript
var ary = [];
for(var i = 0; i <= 1000; i++){
    ary.push(i);
}

var renderFrinedList = function(data){
    for(var i = 0; i <= data.length; i++){
        var div = document.createElement('div');
        div.innerHTML = i;
        document.body.appendChild(div);
    }
}

renderFrinedList(ary);
```

这个问题的解决方案之一是下面的 timeChunk 函数，timeChunk 函数让创建节点的工作分批进行，比如每1秒创建1000个节点，改成了每隔200毫秒创建8个节点。

timeChunk 接受三个参数，第一个参数是创建节点时需要的用到的数据，第二个参数是封装了创建节点逻辑的函数，第三个参数表示每一批创建节点的数量。

```javascript
var timeChunk = function(ary,fn,count){
    var obj,
        t;
    var len = ary.length;
    
    var start = function(){
        for(var i = 0; i < Math.min(count || 1, ary.length); i++){
            var obj = ary.shift();
            fn(obj);
        }
    }
    
    return function(){
        t = setInterval(function(){
            if(ary.length === 0){ // 如果节点全部创建好了
               return clearInterval(t);
            }
            start();
        },200);
    }
}

// 测试，假设我们有 1000 个好友的数据，我们利用 timeChunk 函数，每一批只往页面中创建 8 个节点：

var args = [];
for(var i = i; i <= 1000; i++){
    args.push(i);
}
var renderFriendList = timeChunk(ary,function(n){
    var div = document.createElement('div');
    div.innerHTML = n;
    document.body.appendChild(div);
},8) 
renderFrinedList();
```

#### **5.惰性加载函数**

在 Web 开发中，因为浏览器之间的实现差异，一些嗅探工作总是不可避免的，比如我们需要一个在各个浏览器中能够通用的事件绑定函数 addEvent,常见的方法如下：

```javascript
var addEvent = function(elem,type,handler){
    if(window.addEventListener){
       return elem.addEventListener(type,handler,false);
    }
    if(window.attackEvent){
       return elem.attackEvent('on'+type,handler);
    }
}
```

这个函数的缺点是，当它每次被调用的时候都会执行里面的 if 条件分支，虽然执行这些 if 分支的开销不算大，但也许有一些方法可以让程序避免重复的执行过程。

第二种方案是这样的，我们把嗅探浏览器的操作提前到代码加载的时候。在代码加载的是就立刻进行一次判断，以便让 addEvent 返回一个包裹了正确逻辑的函数。代码如下：

```javascript
var addEvent = (function(){
    if(window.addEventListener){
        return function function(elem,type,handler){
            elem.addEventListener(type,handler,false);
        }
    }
    if(window.attackEvent){
       return function function(elem,type,handler){
            elem.attackEvent('on'+type,handler);
       }
    }    
})();
```

目前的 addEvent 函数依然有个缺点，也许我们从头到尾都没有使用过 addEvent 函数，这样看来，前一次的浏览器嗅探就是完全多余的操作，而且这也会稍稍延长页面 ready 的时间。

第三种方案就是惰性载入函数方案，此时 addEvent 依然被声明为一个普通函数，在函数里面依然有一些分支判断。但是在第一次进入条件分支之后，在函数内部会重写这个函数，重写之后的函数就是我们期望的 addEvent 函数，在下一次进去 addEvent 函数的时候，addEvent 函数里面就不存在分支语句了：

```javascript
var addEvent = function(elem,type,handler){
    if(window.addEventListener){
        addEvent = function function(elem,type,handler){
            elem.addEventListener(type,handler,false);
        }
    }
    else if(window.attackEvent){
       addEvent = function function(elem,type,handler){
            elem.attackEvent('on'+type,handler);
       }
    } 
    addEvent(elem,type,handler);
}
```

### 小结

在进入设计模式的学习之前，本章挑选了闭包和高阶函数来进行讲解。这是因为在 JavaScript开发中，闭包和高阶函数的应用极多。就设计模式而言，因为 JavaScript 这门语言的自身特点，许多设计模式在 JavaScript 之中的实现跟在一些传统面向对象语言中的实现相差很大。在JavaScript 中，很多设计模式都是通过闭包和高阶函数实现的。这并不奇怪，相对于模式的实现过程，我们更关注的是模式可以帮助我们完成什么