---
title: 你不知道的JavaScript(上)——行为委托
date: 2019-01-23 18:30:00
tags: 你不知道的JavaScript
categories: 
	- JavaScript

---

这个系列的作品是上一次当当网有活动买的，记得是上一年九月份开学季的时候了。后面一直有其他的事情，或者自身一些因素，迟迟没有开封这本书。今天立下一个 flag，希望可以在两个月内看完并记录这个系列的三本书，保持学习的激情，不断弥补自己的基础不够扎实的缺点。

[作者的github](https://github.com/getify/You-Dont-Know-JS)

书籍的购买链接，自己搜。

# 你不知道的JavaScript(上)——行为委托

回顾上一章的理论：[[Prototype]] 机制就是指对象中的一个内部链接引用另一个对象。

如果在第一个对象上没有找到需要的属性或者方法引用，引擎就会继续在 [[Prototype]] 关联的对象上进行查找。同理，如果在后者中也没有找到需要的引用就会继续查找它的 [[Prototype]] ,以此类推，这一系列对象的链接被称为“原型链”。

换句话说，JavaScript 中这个机制的本质就是对象之间的关联关系。

## 面向委托的设计

试着把思路从类和继承的设计模式转换到委托行为的设计模式。

### 类理论

假设我们需要在软件中建模一些类似的任务（“XYZ”，“ABC”等）。

如果使用类，那设计方法可能是这样的：定义一个通用父（基）类，可以将其命名为 Task,在 Task 类中定义所有任务都有的行为。接着定义子类 XYZ 和 ABC，它们都继承自 Task 并且添加一些特殊的行为来处理对应的任务。

非常重要的是，类设计模式鼓励你在继承时使用方法重写（和多态），比如在 XYZ 任务中重写 Task 中定义的一些通用的方法，甚至在添加新行为时通过 super 调用这个方法的原始版本。你会发现许多行为可以先 “抽象”到父类然后再用子类进行特殊化（重写）。

下面的是对应的伪代码：

```pseudocode
class Task{
    id:
    // 构造函数 Task()
    Task(ID){id = ID}
    outputTask(){output(id);}
}
class XYZ inherits Task{
    label;
    // 构造函数 XYZ()
    XYZ(ID,Label){super(ID);label = Label}
    outputTask(){super(); output(label)}
}
class ABC inherits Task{
    //..
}
```

现在可以实例化子类 XYZ 的一些副本然后使用这些实例来执行任务 “XYZ”。这些实例会复制 Task 定义的通用行为以及 XYZ 定义的特殊行为。同理，ABC 类的实例也会复制 Task 的行为和 ABC 的行为。在构造完成后，你通常只需要操作这些实例（而不是类），因为每个实例都有你需要完成任务的所有行为。

### 委托理论

现在我们试着用委托行为来思考同样的问题。

首先你会定义一个名为 Task 的对象（它既不是对象也不是函数），它会包含所有任务都可以使用（写作使用，读作委托）的具体行为。接着，对于每个任务（“XYZ”,"ABC"）你都会定义一个对象来存储对应的数据和行为。把特定的任务对象都关联到 Task 功能对象上，让它们在需要的时候可以进行委托。

基本上可以想象成，执行任务的 “XYZ” 需要两个对象（XYZ 和 Task）协作完成，但是我们并不需要这些行为放在一起，通过类的复制，我们可以把它们分别放在各自独立的对象中，需要时可以运行 XYZ 对象委托给 Task。

下面是推荐的代码形式，非常简单：

```javascript
Task = {
    setID:function(ID){this.id = ID},
    outputID:function){console.log(this.id)}
}
// 让 XYZ 委托 Task
XYZ = Object.create(Task);

XYZ.prepareTask = function(ID,Label){
    this.setID();
    this.label = Label;
}
XYZ.outputTaskDetails = function(){
    this.outputID();
    console.log(this.label);
}
// ABC = Object.create(Task);
// ABC .. = ...
```

这段代码中，Task 和 XYZ 并不是类（或者函数），它们是对象。XYZ 通过 Object.create(..) 创建，它的 [[Prototype]] 委托了对象。

相比于面向类（或者说面向对象），作者会把这种编码风格称为“对象关联”（OLOO，object linked to other objects）。我们真正关心的是 XYZ 对象（和 ABC 对象）委托了 Task 对象。

在 JavaScript 中，[[Prototype]] 机制会把对象关联到其他对象。

对象关联风格的代码还有一些不足之处：

1. 在上面的例子中，id 和 label 数据成员都是直接存在 XYZ 上（而不是 Task），通常来说，[[Prototype]] 委托中最好把状态保存在委托者（XYZ、ABC）而不是委托目标（Task）上。

2. 在类设计模式中，故意让父类（Task）和子类（XYZ）中都有 outputTask 方法，这样就可以利用重写（多态）的优势，在委托行为中则恰好相反，我们会尽量避免在 [[Prototype]] 链的不同级别使用相同的命名，否则就需要使用笨拙并且脆弱的语法来消除引用歧义。

   这个设计模式要求就进来少使用容易被重写的通用方法名，提倡使用更有描述性的方法名，尤其是写清相对对象行为的类型。这样做实际上可以创建出更容易理解和维护的代码。因为方法名（不仅在定义的位置，而是贯穿整个代码）更加清晰（自文档）。

3. this.setID(ID),XYZ 中的方法首先会寻找 XYZ 自身是否有 setID(..)，但是 XYZ 中并没有整个方法名，因为会通过 [[Prototype]] 委托关联到 Task 继续寻找，这时就可以找到 setID(..) 整个方法。此外，由于调用位置触发了 this 的隐式绑定规则，因此虽然 setID(..) 方法在 Task 中，运行时 this 仍然会绑定到 XYZ，这正是我们想要的。后面的outputID(..) 也是同样的原理。

   换句话说，我们和 XYZ 进行交互时，可以使用 Task 中通用的方法，因为 XYZ 委托了 Task.

委托行为意味着某些对象（XYZ）在找不到属性或者方法引用时会把这个请求委托给另一个对象（Task）.

这个是一种极其强大的设计模式，和父类、子类、继承、多态等概念完全不同。对象并不是按照父类到子类的关系垂直组织的，而是通过任意方法的委托并排组织的。

> 在 API 接口的设计中，委托最好在内部实现，不用直接暴露出去。在之前的例子中我们没有让开发者通过 API 直接调用 XYZ.setID()（当然，可以这么做）。相反，我们把委托隐藏了在 API 内部，XYZ.propareTas() 会委托 Task.setID(..)。

#### **1.相互委托( 禁止)**

你无法在两个或两个以上互相（双向）委托的对象之间创建循环委托。如果你把 B 关联到 A 然后试着把 A 关联到 B，就会出现。

这种方法是禁止的。如果你引用了一个两边都不存在的属性或者是方法，那就会在 [[Prototype]] 链上产生一个无限递归的循环。但是如果所有的引用都被严格限制的话，B是可以委托 A的，反之亦然。因为，互相委托理论上是可以正常工作的，在某些情况下这是非常有用的。

之所以要禁止互相委托，是因为引擎的开发者发现在设置时检查（并禁止）一次无限循环引用要更加高效，否则每次从对象中查找属性时都需要进行检查。

#### **2.调试**

通常来说，JavaScript 规范并不会控制浏览器中开发者对于特定值或者结构的表达方式，浏览器和引擎可以自己选择合适的方式来解析，因此浏览器和工具的解析结构并不一定相同。比如下面的这段代码的结果只能在谷歌的开发者工具中看到。

这段传统的“类构造函数” JavaScript 代码在谷歌开发者工具的控制台中结果是这样的：

```javascript
function Foo(){}
var a1 = new Foo();
a1; // Foo{}
// 在 FireFox 中运行却是 Object{}
```

谷歌浏览器想说的 "{ } 是一个空对象，由名为 Foo 的函数构造"，Firefox 想说的是 “{} 是一个空对象，由Object 构造”，之所有有这种细微的差别，是因为谷歌会动态跟踪并把实际构造过程的函数当做一个内置属性，但是其他浏览器并不会跟踪这些额外的信息。

可以用 JavaScript 中的机制来解释一些谷歌的跟踪原理：

```javascript
function Foo(){}
var a1 = new Foo();
a1.constructor(); // Foo(){}
a1.constructor.name; //"Foo"
```

谷歌是不是直接输出了对象的 .constructor.name ？令人迷惑的是，答案既是又不是。

思考下面的代码：

```javascript
function Foo(){}
var a1 = new Foo();
Foo.prototype.constructor = function Gotcha(){};
a1.constructor; // Gotcha(){}
a1.constructor.name; // "Gotcha"
a1; // Foo(){}
```

即使我们把 a1.constructor.name 修改为一个合理的值，谷歌浏览器的控制台仍然会输出 Foo。

所以上面的那个问题答案是“不是”。谷歌浏览器是通过另一种方式进行跟踪的。

```javascript
var Foo = {};
var a1 = Object.create(Foo);
a1; // Object{}
Object.defineProperty(Foo,"constructor",{
    enumerable:false,
    value:function Gotcha(){}
});
a1; //Gotcha{}
```

这个bug现在已经被修复了，谷歌浏览器内部跟踪（只用于调试输出）“构造函数名称”的方法是它自身的一种扩展行为，并不包含在 JavaScript 规范中。

如果使用的并不是 “构造函数”来生成对象，比如使用本章介绍的对象关联风格来编写代码，那么谷歌浏览器就无法跟踪对象内部的 “构造函数名称”，这样的对象输出的就是 “Object{}”，意思是“Object() 构造出来的对象”。

当然，这并不是对象关联的风格的缺点，当你使用对象关联风格来编写代码并使用行为委托设计模式时，并不需要关注谁“构造了”对象（就是使用 new 来编写的那个函数）。只有使用类风格来编写代码时，谷歌浏览器内部的 “构造函数名称”跟踪才有意义，使用对象关联时这个功能起不到任何的作用。

### 比较思维模型

现在已经明白了“类”和“委托”这两种设计模式的理论区别，接下来看看他们在思维模型方面的区别。

通过一些实例代码来比较一下两种设计模式（面向对象和对象关联）具体的实现方法，下面是典型（“原型”）面向对象风格：

```javascript
function Foo(who){
    this.me = who;
}
Foo.prototype.identify = function(){
    return "I am" + this.me;
}
function Bar(who){
    Foo.call(this,who);
}
Bar.prototype = Object.create(Foo.prototype);
Bar.prototype.speak = function(){
    alert('Hello,'+this.identify()+".");
}

var b1 = new Bar("b1");
var b2 = new Bar("b2");
b1.speak();
b2.speak();
```

子类 Bar 委托了 父类 Foo,然后生成了 b1 和 b2 两个实例。b1 委托了 Bar.prototype,Bar.prototype 委托了Foo.prototype。

下面看看如何使用面向对象关联风格来编写完全相同的代码：

```javascript
Foo = {
    init:function(who){
        this.me = who;
    }
    identify:function(){
        return "I am" + this.me;
    }
}
Bar = Object.create(Foo);
Bar.speak = function(){
    alert('Hello,'+this.identify()+".");
}
var b1 = Object.create(Bar);
var b2 = Object.create(Bar);   
b1.init();
b2.init();
b1.speak();
b2.speak();
```

这段代码中我们同样利用了 [[Prototype]] 把b1 委托给了 Bar 并把 Bar 委托给了 Foo,和上一段方面一模一样，我们仍然实现了三个对象之间的关联。

但是非常重要的一点是，这段代码简洁了很多。我们只是把对象关联起来，并不需要那些既复杂又令人困惑的模仿类的行为（构造函数、原型已经 new）.

首先，类风格的代码的思维模型强调实体以及实体间的关系

![类风格代码的思维模型](/images/2019-01-23-你不知道的JavaScript(上)之行为委托-类风格代码的思维模型.png)

看起来很复杂，从图中可以看出这是一张非常复杂的关系图，此外，你跟着图片中的箭头走就会发现，JavaScript 中有很强的内部连贯性。

举个例子，JavaScript 指哪个的函数之所以可以访问 call、apply、bind 是因为函数本身也是对象。而函数对象同样有 [[Prototype]] 属性并关联到 Function.prototype 对象，因此所有函数对象都可以通过委托来调用这些默认方法。

下面看简化版的，更加清晰——只展示了必要的对象和关系：

![类风格代码的思维模型(简化版)](/images/2019-01-23-你不知道的JavaScript(上)之行为委托-类风格代码的思维模型(简化版).png)

虚线表示的是 Bar.prototype 继承 Foo.prototype 之后丢失的 .constructor 属性引用，它们还没有被修复。即使移除这些虚线，这个思维模型在你处理关联时仍然非常复杂。

看看对象关联风格代码的思维模型：

![对象关联风格代码的思维模型](/images/2019-01-23-你不知道的JavaScript(上)之行为委托-对象关联风格代码的思维模型)

通过这些可以看出，对象关联风格的代码显然更加简洁，因为这种代码只关注一件事情：对象之间的关联关系。

其他 “类”技巧都是非常复杂并且令人困惑的。去掉它们之后，事情会变得简单许多（同时保留所有功能）。

## 类和对象

我们已经看到了 “类” 和 “行为委托”在理论和思维模型方面的区别，现在看看真实场景如何应用这些方法。

看看 Web 开发中非常经典的一些前端场景：创建 UI 控件（按钮、下拉列表等等）。

### 控件“类”

可能你已经习惯了面向对象的设计模式，所以很快会想到一个包含所有通用控件行为的父类和继承父类的特殊控件子类。

> 这里将使用JQ 来操作 DOM 和 CSS，因为这些操作和我们讨论的内容没有太多的关系。

下面这段代码展示的是如何不使用任何 “类”辅助库或者语法的情况下，使用 纯 JavaScript 实现 类风格的代码：

```javascript
// 父类
function Widget(width,height){
    this.width = width || 50;
    this.height = height || 50;
    this.$elem = null;
}
Widget.prototype.render = function($where){
    if(this.$elem){
        this.$elem.css({
            width:this.width + 'px',
            height:this.height + 'px',
        }).appendTo($where);   
    }
}
// 子类
function Button(width,height,label){
    // 调用 “super” 构造函数
    Widget.call(this,width,height);
    this.label = label || "Default";
    this.$elem = $("<button>").text(this.label);
}
// 让 Button 继承 Widget
Button.prototype = Object.create(Widget.prototype);

// 重写 render(...)
Button.prototype.render = function($where){
    // “super” 调用
    Widget.prototype.render.call(this,$where);
    this.$elem.click(this.onClick.bind(this));
}
Button.prototype.onClick = function(evt){
    console.log("Button "+ this.label +" clicked!");
}

$(document).ready(function(){
    var $body = $(document.body);
    var btn1 = new Button(125,30,"Hello");
    var btn2 = new Button(150,40,"World");
    btn1.render($body);
    btn2.render($body);
});
```

在面向对象设计模式中我们需要现在父类中定义基础的 render(..)，然后在子类中重写它。子类并不会替换基础的 render(..)，只是添加了一些按钮特有的欣慰。可以看到代码中丑陋的显式伪多态，即通过 Widget.call 和  Widget.prototype.render.call  从“子类”方法中引用“父类”中的基础方法。

#### **ES6 的 class 语法糖**

简单介绍如何使用 class 来实现相同的功能：

```javascript
// 父类
class Widget{
    constructor(width,height){
        this.width = width || 50;
        this.height = height || 50;
        this.$elem = null;  
    }
    render($where){
        if(this.$elem){
            this.$elem.css({
                width:this.width + 'px',
                height:this.height + 'px',
            }).appendTo($where);   
        }        
    }
}
class Button extends Widget{
    constructor(width,height,label){
        super(width,height);
        this.label = label || "Default";
        this.$elem = $("<button>").text(this.label);
    }    
    render($where){
        super.render($where)
		this.$elem.click(this.onClick.bind(this));       
    }
    onClick(evt){
        console.log("Button "+ this.label +" clicked!");
    }
}


$(document).ready(function(){
    var $body = $(document.body);
    var btn1 = new Button(125,30,"Hello");
    var btn2 = new Button(150,40,"World");
    btn1.render($body);
    btn2.render($body);
});
```

毫无疑问，使用了 ES6 的 class之后，上一段代码中很多丑陋的语法都不见了，super(..) 函数很棒。

尽管语法上得到了改进，但实际上这里并没有真正的类，class 仍然是通过 [[Prototype]] 机制实现的，因为我们仍然面临思维模式不匹配的问题。

无论你使用传统的原型语法还是 ES6 新语法糖，仍然需要用 “类”的概念来对问题进行建模。

### 委托控件对象

下面的例子使用对象关联风格委托来简单实现 Widget/Button：

```javascript
var Widget = {
    init:function(width,height){
        this.width = width || 50;
        this.height = height || 50;
        this.$elem = null;          
    },
    insert:function($where){
        if(this.$elem){
            this.$elem.css({
                width:this.width + 'px',
                height:this.height + 'px',
            }).appendTo($where);   
        }        
    }
};
var Button = Object.create(Widget);
Button.setup = function(width,height,label){
    // 委托调用
    this.init(width,height);
    this.label = label || "Default";
    this.$elem = $("<button>").text(this.label);    
}
Button.build = function($where){
    // 委托调用
    this.insert($where)
    this.$elem.click(this.onClick.bind(this));    
}
Button.OnClick = function(evt){
    console.log("Button "+ this.label +" clicked!");
}

$(document).ready(function(){
    var $body = $(document.body);
    var btn1 = Object.create(Button);
    var btn2 = Object.create(Button);
    btn1.setup(125,30,"Hello");
    btn1.setup(150,40,"World");    
    btn1.build($body);
    btn2.build($body);
});
```

使用对象关联来编写代码时不需要把 Widget 和 Button 当做父类和子类，相反，Widget 只是一个对象，包含一个通用的函数，任何类型的控件都可以作为委托，Button 同样只是一个对象。

从设计模式的角度来说，我们并没有像类一样在两个对象中都定义相同的方法名 render(..) ，相反我们定义了两个更具有描述性的方法名（insert/build）。同理，初始化方法分别叫做 init 和 setup。

在委托设计的模式中，除了建议使用不相同的并且更具有描述性的方法名之外，还要通过对象关联避免使用丑陋的显式伪多态调用，而是用简单的相对委托调用 this.init() 和 this.insert。

从语法角度来说，我们没有使用任何的构造函数 、.prototype 或者 new.实际上也没有必要使用它们。

仔细观察的话，会发现之前的一次调用 （var btn1 = new Button(125,30,"Hello");）变成了两次（var btn1 = Object.create(Button);btn1.setup(125,30,"Hello");）咋一看这似乎是一个缺点（需要更多的代码）。

但是这一点其实也是对象关联风格相比较传统原型风格代码的优势的地方，为什么？

使用类构造的话，你需要（并不是硬性要求，但是强烈建议）在同一个步骤中实现构造和初始化。然后，许多情况下把这两步分开（就像对象关联代码一样）更灵活。

举例来说，假如在程序启动的时候创建一个实例池，然后一直等到实例被取出来并使用才执行特定的初始化过程。这个过程中两个函数调用是挨着的。但是完全可以根据需要让它们出现在不同的位置。

**对象关联可以更好地支持关注分离（separation of concerns）原则，创建和初始化并不需要合并为一个步骤**

## 更简洁的设计

对象关联除了能让代码看起来更加简洁（并且就有更好的扩展性）外还可以通过行为委托模式简化代码结构。

下面的场景中我们有两个控制器对象，一个用来操作网页中的登录表单，一个用来与服务器进行验证（通信）。

我们需要一个辅助函数来创建 Ajax 通信，我们使用的是 jq，它不仅可以处理 Ajax 并且返回一个类 Promise 的结果，因此可以使用 .then() 来监听响应。

在传统的类设计模式中，我们会把基础的函数定义在名为 Controller 的类中，然后派生出两个子类 LoginController 和 AuthController ，它们都继承自 Controller 并且重写了一些基础行为：

```javascript
// 父类
function Controller(){
    this.error = [];
}
Controller.prototype.showDialog = function(title,msg){
    // 给用户显示标题和消息
}
Controller.prototype.success = function(msg){
    this.showDialog("Success",msg);
}
Controller.prototype.failure = function(err){
    this.errors.push(err);
    this.showDialog("Error",msg);
}

// 子类
function LoginController(){
    Controller.call(this);
}
// 把子类关联到父类
LoginController.prototype = Object.create(Controller.prototype);
LoginController.prototype.getUser = function(){
    return document.getElementById("login_username").value;
}
LoginController.prototype.getPassword = function(){
    return document.getElementById("login_password").value;
}
LoginController.prototype.validateEntry = function(user,pw){
    user = user || this.getUser();
    pw = pw || this.getPassword();
    if(!(user && pw)){
       return this.failure("Please enter a username & password!")
    }else if(pw.length < 5){
       return this.failure("Password must be 5+ characters!")
    }
    // 如果这行到这里说明通过验证
    return true;
}
// 重写基础的 failure()
LoginController.prototype.failure = function(err){
    // "super" 调用
    Controller.prototype.failure.call(this,"Login invalid" + err);
}

// 子类
function AuthController(login){
    Controller.call(this);
    // 合成
    this.login = login;
}
// 把子类关联到父类
AuthController.prototype = Object.create(Controller.prototype);
AuthController.prototype.server = function(url,data){
    return $.ajax({
        url:url,
        data:data
    })
}
AuthController.prototype.checkAuth = function(){
    var user = this.login.getUser();
    var pw = this.login.getPassword();
    if(this.login.validateEntry(user,pw)){
        this.server("/check-auth",{
            user:user,
            pw:pw
        }).then(this.succdess.bind(this))
        .fail(this.failure.bind(this))
    }
}
// 重写 success
AuthController.prototype.success = function(){
    // super 调用
    Controller.prototype.success.call(this,"Authenticated!");
}
// 重写 failure
AuthController.prototype.failure = function(){
    // super 调用
    Controller.prototype.success.call(this,"Auth Failed: "+ err);
}
// 除了继承，我们还要合成
var auth = new AuthController(new LoginController);
auth.checkAuth();
```

所有控制器共享的基础行为是 success 和 failure 和 showDialog。子类 LoginController 和 AuthController 通过重写 failure 和 success 来扩展默认基础类行为。此外，注意 AuthController 需要一个 LoginController 的实例来登录表单进行交互，因此这个实例变成了一个数据属性。

另外还要注意的是，我们在继承的基础上进行了一些合成。AuthController 需要使用 LoginController ，因为我们实例化后者并用一个类成员属性 this.login 来引用它，这样 AuthController 就可以调用 LoginController 的行为了。

> 你可能想让 AuthController 继承 LoginController 或者相反，这样我们就通过继承实现了真正的合成，但是这就是类继承在问题领域建模时产生的问题，因为 AutController 和 LoginController 都不具备对方的基础行为，所以这种继承是不恰当的。我们的解决方法就是进行一些就简单的合成从而让它们既不必互相继承又可以互相合作。

### **反类**

但是，我们真的需要一个 Controller 父类，两个子类加上合成来对这个问题进行建模吗？能不能使用对象关联风格的行为委托来实现更简单的设计呢？当然可以。

```javascript
var LoginController = {
    error:[],
    getUser:function(){
        return document.getElementById("login_username").value;
    },
    getPassword:function(){
        return document.getElementById("login_password").value;
    },
    vaildateEntry:function(user,pw){
        user = user || this.getUser();
        pw = pw || this.getPassword();
        if(!(user && pw)){
            return this.failure("Please enter a username & password!")
        }else if(pw.length < 5){
            return this.failure("Password must be 5+ characters!")
        }
        // 如果这行到这里说明通过验证
        return true;			
    },
    showDialog:function(){
        // 给用户显示标题和消息
    },
    failure:function(err){
        this.error.push(err);
        this.showDialog("Error","Login invalid" + err);
    }
}
// 让 AuthController 委托 LoginController
var AuthController = Object.create(LoginController);
AuthController.errors = [];
AuthController.checkAuth = function(){
    var user = this.getUser();
    var pw = this.getPassword();
    if(this.validateEntry(user,pw)){
        this.server("/check-auth",{
            user:user,
            pw:pw
        })
        .then(this.accepted.bind(this))
        .fail(this.rejected.bind(this))
    }		
}
AuthController.server = function(url,data){
    return $.ajax({
        url:url,
        data:data
    })
}
AuthController.accepted = function(){
    this.showDialog("Success","Authenticated!");
}
Authenticated.rejected = function(err){
    this.failure("Auth Failed!",err);		
}
```

由于 AuthController 只是一个对象，因为我们不需要实例化只要一行代码：

```javascript
AuthController.checkAuth();
```

借助对象关联，可以简单地向上委托链上添加一个或者多个对象，而且同样不需要实例化：

```javascript
var controller1 = Object.create(AuthController);
var controller2 = Object.create(AuthController);
```

在行为委托模式中，AuthController 和 LoginController 只是对象，它们之间是兄弟关系，并不是父类和子类的关系，代码中 AuthController 委托了 LoginController ，反向委托也可以。

这种模式的重点在于只需要两个实体（LoginController 和 AuthController ）,而之前的模式需要三个。

我们不需要 Controller 基本类来共享两个实体之间的行为，因为委托足以满足我们需要的功能，同样的，前面提到的，我们也不需要实例化类，因为它们根本就不是类，它们只是对象。此外，我们也不需要合成，因为两个对象可以通过委托进行合作。

最后，我们避免了面向类设计模式的多态。我们在不同的对象中没有使用的函数名 success 和 failure 这样就不需要使用丑陋的显式伪多态。相反，在 AuthController 中它们的名字分别是 accepted 和 rejected 可以更好地描述它们的行为。

总结：我们使用一种简单的设计来实现了相同的功能，这就是对象关联风格代码和行为委托设计模式的力量。

## 更好的语法

ES6 的 class 语法可以简洁地定义为 类方法,这个特性让 class 乍看起来更有吸引力。

```javascript
class Foo(){
    methodName(){/*..*/}
}
```

在 ES6 中，我们可以在任意对象的字面形式使用简洁方法声明（concise method declaration），所欲对象关联风格的对象还可以这样声明。

```javascript
var loginController = {
    error:[],
    getUser(){
        //,,,
    },
    getPassword(){
        // ...
    }
}
```

唯一的区别就是对象的字面形式仍然使用 “,”来隔开元素，而 class 不需要。

此外，在 ES6 中，可以使用对象的字面形式来改写之前繁杂的属性赋值语法，然后用 Object.setPrototypeOf(..) 来修改它的 [[Prototype]]

```javascript
// 使用更好的对象字面形式语法和简洁方法
var AuthController = {
    error:[],
    checkAuth(){
        //..
    },
    server(url,data){
        //..
    },
    //..
};
// 现在把 AuthController 关联到 LoginController
Object.setPrototypeOf(AuthController,LoginController);
```

使用 ES6 的简洁方法可以让对象关联风格更加人性化（并且仍然比典型的原型风格更加简洁和优秀）。

### **反词法**

简洁方法有一个非常小但是非常重要的缺点，思考下面的代码：

```javascript
var Foo = {
    bar(){/*..*/},
    baz:function baz(){/*..*/}
}
```

去掉语法糖之后的代码如下所示

```javascript
var Foo = {
    bar:function(){/*..*/},
    baz:function baz(){/*..*/}
}
```

由于函数对象本身没有名称标识符，所以 bar 的缩写形式实际上会变成一个匿名函数表达式并且赋值给 bar 属性，相比之下，具名函数表达式会额外地给 .baz 属性附加一个词法名称标识符 baz。

在前面我们分析了匿名函数表达式的三大主要缺点，，匿名函数没有 name 标识符，会导致：

1. 调用栈难以追踪
2. 自我引用（递归、事件、（接触）绑定等等）更难
3. 代码更难理解

简洁方法没有第一和第三个缺点。

去掉语法糖的版本使用的是匿名表达式，通常来说不会在追踪栈中添加 name ，但是简洁方法很特殊，会给对应的函数对象设置一个内部的 name 属性，这样的理论上可以用在追踪栈中。（但是追踪的具体实现是不同的，因为无法保证可以使用）。

简洁方法无法避免第二个缺点，它们不具备自我引用的词法标识符，思考下面的代码：

```javascript
var Foo = {
    bar:function(x){
        if(x<10){
        	return Foo.bar(x*2);   
        }
        return x;
    },
    baz：function(){
        if(x<10){
			return Foo.baz(x*2);              
        }
        return x;
    }
}
```

在上面的例子中使用 Foo.bar(x*2)就足够了，但是在许多情况下，无法使用这种方法，比如多个对象通过代理共享函数，使用 this 绑定，等等。这种情况下，最好使用的方法就是使用函数对象的 name 标识符来进行真正的自我引用。

使用简洁方法一定要小心这一点，如果需要自我引用的时候，那最好的方法就是使用传统的具名函数表达式来定义对应的函数，不要使用简洁方法。

## 内省

内省就是检查实例的类型，类实例的内省主要目的是通过创建方式来判断对象的结构和功能。

下面的代码就是使用 instanceof 来推测对象的 a1 功能：

```javascript
function Foo(){
    //..
}
Foo.prototype.something = function(){
    // ..
}
var a1 = new Foo();
//之后
if(a1 instanceof Foo){
   a1.something();
}
```

因为 Foo.prototype(不是 Foo) 在 a1 的 [[Prototype]] 链上，所以 instanceof 可以告诉我们 a1 是 Foo 类的一个实例，知道了这一点之后，我们就可以认为 a1 有 Foo “类”描述的功能。

当然，Foo 类并不存在，只有一个普通的函数 Foo,它引用了 a1 委托的对象（Foo.prototype）。从语法角度来说，instanceof 似乎是检查 a1 和 Foo 的关系，但是实际上它想说的是 a1 和 Foo.prototype （引用的对象）是互相关联的。

instanceof 语法会产生语义困惑而且非常不直观，如果想检查对象a1 和某个对象的关系，那必须使用另一个引用该对象的函数才可以——你不可以判断两个对象是否关联。

之前介绍的抽象的 Foo/Bar/b1 例子，简单来说是这样的：

```javascript
function Foo(){}
Foo.prototype...
function Bar(){}
Bar.prototype = Object.create(Foo.prototype);
var b1 = new Bar("b1");
```

如果要使用 instanceof 和 .prototype 语义来检查上面例子中的实体的关系，那必须这样做：

```javascript
// 让 Foo 和 Bar 互相关联
Bar.prototype instanceof Foo; // true
Object.getPrototypeOf(Bar.prototype) === Foo.prototype; // true
Foo.prototype.isPrototypeOf(Bar.prototype); // true

// 让 b1 关联到 Foo 和 Bar
b1 instanceof Foo; // true
b1 instanceof Bar; // true
Object.getPrototypeOf(b1) === Bar.prototype; // true
Bar.prototype.isPrototypeOf(b1); // true
Foo.prototype.isPrototypeOf(b1); // true
```

显然这是一种非常糟糕的方法，举例来说（使用类时）你最直观的想法可能是使用 Bar instanceof Foo(因为很容易把实例理解成继承)，但是在 JavaScript 中是行不通的，你必须使用 Bar.prototype instanceof Foo.

还有一种常见的但是很脆弱的内省模式，这种模式被称为鸭子类型。

```javascript
if(a1.something){
   a1.something();
}
```

我们并没有检查 a1 和 委托 something()函数的对象之间的关系，而是假设如果如果 a1 通过了 测试 a1.something 的话，那么 a1 就一定可以调用这个方法（无论这个方法是否存在于它本身或是委托到其他对象）。

但是 “鸭子类型”通常会在测试之外做出很多关于对象功能的假设，这会带来很多风险（或者说脆弱的设计）。

ES6 的 Promise 就是典型的 “鸭子模式”。

由于各种各样的原因，我们需要判断一个对象引用是否是 Promise ，但是判断的方法就是检查对象中是否有 then 方法，换句话说，如果对象中有 then 方法，ES6 的Promise 就会认为这个对象是“可持续的”（thenable），因此会期望它具有 Promise 的所有标准行为。

如果有一个不是 Promise 但是具有 then 方法的对象，那就千万不要把它用在 ES6 中的 Promise 机制中，否则会出错。

使用对象关联时，所有的对象都是通过 [[Prototype]] 委托相互关联的，下面是内省的方法

```javascript
// 让 Foo 和 Bar 互相关联
Foo.isPrototypeOf(Bar); // true
Object.getPrototypeOf(Bar) === Foo; // true
// 让 b1 关联到 Foo 和 Bar
Foo.isPrototypeOf(b1); // true
Bar.isPrototypeOf(b1); // true
Object.getPrototypeOf(b1) === Bar; // true
```

我们没有使用 instanceof 因为它会产生一些和类有关的误解。我们认为 JavaScript 中对象关联比类风格的代码更加简洁（而且功能相同）

## 小结

在软件架构中你可以选择是否使用类和继承设计模式，大多数开发者理所当然地认为类是唯一的代码组织方式，但是本章中我们看到另一种少见但是更强大的设计行为：行为委托。

行为委托认为对象之间是兄弟关系，互相委托，不是父类和子类的关系。JavaScript 的 [[Prototype]] 机制本质上就是行为委托机制。也就是说，我们可以选择在 JavaScript 中努力实现类机制，也可以拥抱更自然的 [[Prototype]] 委托机制。

当你只用对象设计代码时，不仅可以让语法更加简洁，而且可以让代码结构更加清晰。

对象关联（对象之前互相关联）是一种编码风格，它倡导的是直接创建和关联对象，不把它们抽象成类。对象关联可以用基于 [[Prototype]] 的行为委托非常自然地实现。

## 附录

### ES6 中的 Class

类是一种可选的（而不是必须）的设计模式，而且在 JavaScript 这样的 [[Prototype]] 语言中实现类是很别扭的。

缺点：繁琐杂乱的 .prototype 引用、试图调用原型链上层同名函数时的显式伪多态以及不可靠、不美观容易被误解成“构造函数“的 .construtor。

除此之外，类设计其实还有更深刻的问题，传统面向类的语言中父子类，子和实例之间其实是复制操作，但是在 [[Prototype]] 中并没有复制，相反，它们之间只有委托关联。

对象关联代码和行为委托使用了 [[Prototype]] 而不是将它们藏起来，对比起简洁性可以看出，类并不适用 JavaScirpt.

#### **class**

ES6 的 class 机制，会介绍它的工作原理以及是否改进了之前提到的缺点。

```javascript
// 父类
class Widget{
    constructor(width,height){
        this.width = width || 50;
        this.height = height || 50;
        this.$elem = null;  
    }
    render($where){
        if(this.$elem){
            this.$elem.css({
                width:this.width + 'px',
                height:this.height + 'px',
            }).appendTo($where);   
        }        
    }
}
class Button extends Widget{
    constructor(width,height,label){
        super(width,height);
        this.label = label || "Default";
        this.$elem = $("<button>").text(this.label);
    }    
    render($where){
        super.render($where)
		this.$elem.click(this.onClick.bind(this));       
    }
    onClick(evt){
        console.log("Button "+ this.label +" clicked!");
    }
}


$(document).ready(function(){
    var $body = $(document.body);
    var btn1 = new Button(125,30,"Hello");
    var btn2 = new Button(150,40,"World");
    btn1.render($body);
    btn2.render($body);
});
```

除了语法更加好看之外，ES6 还解决了什么问题呢？

1. 不再引用杂乱的 .prototype 了
2. Button 声明时直接“继承” 了 Widget，不再需要通过 Object.create(..) 来替换 .prototype 对象，也不需要设置 `.__proto__` 或者 Object.setPrototypeOf(..)
3. 可以通过 super(..) 来实现相对多态，这样任何防范都可以引用原型链上层的同名方法，可以解决之前提到的问题：构造函数不属于类，所以无法互相引用——super() 可以完美解决构造函数的问题。
4. class 字面语法不能声明属性（只能声明方法）。看起来这是一种限制，但是它会排除掉很多不好的情况，如果没有这种限制的话，原型链末端的实例可能会意外地获取其他地方的属性（这些属性隐式被所有“实例”所共享）。所以 ，class语法实际上可以帮你避免犯错。
5. 可以通过 extends 很自然扩展对象（子）类型，甚至是内置的对象（子）类型，比如 Array，RegExp。没有 class  ..extends 语法时，想实现这一点是非常困难的，基本上只有框架的作者才能搞懂这一点。但是现在可以轻而易举地做到。

平心而论,class 语法确实解决了典型原型风格代码中很多显而易见的（语法）问题和缺点。

#### **class 陷阱**

然后，class 语法并没有解决所有的问题，在 JavaScript 中使用 “类”设计模式仍然存在许多深层问题。

首先，你可能会认为 ES6 的 class 语法是向 JavaScript 中引入了一种新的 “类”机制，其实不是这样的，class 基本上只是现有的 [[Prototype]] （委托！）机制的一种语法糖。

也就是说，class 并不会像传统面向类的语言一样在声明时静态复制所有的行为。如果修改或者替换了父“类”中的一个方法，那么子“类”和所有实例都会受到影响，因为它们在定义时并没有进行复制，只是适用基于 [[Prototype]] 的实时委托：

```javascript
class C{
    constructor(){
        this.sum = Math.random();
    }
    rand(){
        console.log("Random: "+this.num);
    }
}
var c1 = new C();
c1.rand(); // "Random: 0.4546976..."
C.prototype.rand = function(){
    console.log("Random: "+Math.round(this.num * 1000));
}
var c2 = new C();
c2.rand(); // "Random: 867"
c1.rand(); // "Random: 425"
```

为什么要使用本质上不是类的 class 语法？

ES6 的 class 语法不是让传统类和委托对象之间的区别更加难以发现和理解吗？

class 语法无法定义类成员属性(只能定义方法)，如果为了追踪实例之间的共享状态必须要这么做，那你就只能使用丑陋的 .prototype 语法，像这样：

```javascript
class C{
    constructor(){
        // 确保修改的是共享状态而不是一个在实例上创建的一个屏蔽属性！
        C.prototype.count++;
        // this.count 可以通过委托实现我们想要的功能
        console.log("Hello: " + this.count);
    }
}
// 直接向 prototype 对象上添加一个共享状态
C.prototype.count = 0；

var c1 = new C();
// Hello:1

var c2 = new C();
// Hello:2

c1.count === 2; // true
c1.count === c2.count; // true
```

这种方法最大的问题是，它违背了 class语法的本意，在实现中暴露（泄露）了 .prototype

如果使用 this.count++ 的话，我们很惊讶地发现在对象 c1 和 c2 上都创建了 .count 属性，而不是更新共享状态。class没有办法解决这个问题，并且干脆就不提供相应的语法支持，所有根本就不应该这样做。

此外，class 语法仍然面临意外屏蔽的问题：

```javascript
class C{
    constructor(id){
       // 我们的id属性屏蔽了 id 方法
        this.id = id;
    }    
    id(){
        console.log(id)
    }
}
var c1 = new C();
c1.id(); // TypeError 
```

除此之外，super 也存在一些细微的问题，你可能认为 super 的绑定方法和 this 类似，也就是说，无论目前的方法在原型链中处于什么位置，super 总会绑定到链的上一层。

然而，处于性能的考虑，super 并不是动态绑定到，它会在声明时“静态”绑定。如果你会用许多不同的方法把函数应用在不同的对象上，那你可能不知道，每次执行这些操作时都必须重新绑定 super。

此外，根据应用方式的不同，super 可能不会绑定到合适的对象，所以你可能需要 用 toMethod(..) 来手动绑定 super。你习惯了把方法应用到不同的对象上，从而可以自动利用 this 的隐式绑定规则，但是 super 是行不通的。

```javascript
class P{
    foo(){
        console.log("P.foo");
    }
}
class C extends P{
    foo(){
        super();
    }
}
var c1 = new C();
C1.foo(); // "P.foo"
var D = {
    foo:function(){
        console.log("D.foo");
    }
}
var E = {
    foo:C.prototype.foo
}
// 把 E 委托到 D
Object.setPrototypeOf(E,D);

E.foo(); // "P.foo"
```

你可能会认为 super 会动态绑定，那么可能期望 super() 会自动识别出 E 委托了 D，所以E.foo() 中的 super 应该调用 D.foo()。

但事实并不是这样的，处于性能考虑，super 并不像 this 一样是晚绑定（lated bound，或者说动态绑定）的，它在 [[HomeObject]].[[Prototype]] 上，[[HomeObject]] 会在创建时静态绑定。

在上面的例子中，super() 会调用 P.foo() ，因为方法的 [[HomeObject]] 仍然是 C,C.[[Prototype]] 是 P。确实可以手动修改 super 绑定，使用 toMethod(..) 绑定或者重新绑定方法的 [[HomeObject]]（就像设置 [[Prototype]] 一样）就可以解决上面的问题：

```javascript
var D = {
    foo:function(){
        console.log("D.foo");
    }
}
// 把 E 委托到 D
var E = Object.create(D);

// 手动把 foo 的 [[HomeObject]] 绑定到 E，E.[[Prototype]] 是 D，所以 super() 是 D.foo() 
E.foo = C.prototype.foo.toMethod(E,"foo");
E.foo(); // "D.foo"
```

> toMethod（..） 会复制方法并把 homeObject 当做第一个参数（也就是我们传入的 E）,第二个参数（可选）是新方法的名称（默认是原方法名）

除此之外，开发者有可能会遇到其他问题，这有待观察，无论如何，对于引擎自动绑定的 super 来说，你必须时刻警惕是否需要进行手动绑定。

#### **静态大于动态吗？**

通过上面的这些特性可以看出，ES6 的 class 最大问题在于，（像传统的类一样）它的语法有时会让你认为，定义了一个 class 后，它就变成了一个（未来会被实例化）东西的静态定义。你会彻底忽略C是一个对象，是一个具体的可以直接交互的东西。

在传统的面向类的语言中，类定义之后就不会进行修改，所以类的设计模式就不支持修改。但是 JavaScript  是最强大的特性之一就是它的动态性，任何对象的定义都可以修改（除非你想设置为不可变的）。

class 似乎不赞成这样子做，所以强制让你使用丑陋的 .prototype 语法以及 super 问题等等，而且这种动态产生的问题，class 基本上没有提供解决方案。

总体来说，ES6 的 class 想伪装成一种很好的语法问题的解决方案，但是实际上却让问题更难解决而且让 JavaScrip 更加难以理解了。

> 如果你使用 .bind() 函数来硬绑定函数，那么这个函数不会像普通函数那样被 ES6 的 extend 扩展于 子类中

#### **小结**

class 很好地伪装成 JavaScript 中类和继承设计模式的解决方案。但是它实际上起到了反作用：它隐藏了许多问题并且带来了更多更细小的但是很危险的问题。

class加上了过去20年对于 JavaScript 中  “类”的误解，在某些方面，它产生的问题比解决的还要多，而且让本来优雅简洁的 [[Prototype]] 机制变得非常别扭。



