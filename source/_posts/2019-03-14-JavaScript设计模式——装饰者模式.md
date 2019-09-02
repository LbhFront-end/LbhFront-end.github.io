---
title: JavaScript设计模式——装饰者模式
date: 2019-03-14 11:30:00
tags: JavaScript设计模式
categories: 
	- JavaScript设计模式





---

学习曾探的 《JavaScript设计模式与开发实践》并做记录。

[书籍的购买链接](http://www.ituring.com.cn/book/1632/)

设计模式的定义是：在面向对象软件设计过程中针对特定问题的简洁而优雅的解决方案。

通俗一点，设计模式是在某种场合下对某个问题的一种解决方案。是给面向对象软件开发中的一些好的设计取个名字。

# JavaScript设计模式——装饰者模式

在程序开发中，许多时候都并不希望某个类天生就非常庞大，一次性包含许多职责。那么我们就可以使用装饰者模式。装饰者模式可以动态地给某个对象添加一些额外的职责，而不会影响从这个类中派生的其他对象。

在传统的面向对象语言中，给对象添加功能常常使用继承的方式，但是继承的方式并不灵活，还会带来学过的问题：一方面会导致超类和子类之间存在强耦合性，当超类改变的时候，子类也会随着改变，另一方面，继承这种功能复用方式通常被称为 “白箱复用”，“白箱”是相对可见性而言的，在继承方式中，超类的内部细节是对子类可见的，继承常常被认为破坏了封装性。

使用继承还会带来另外一个问题，在完成一些功能复用的同时，有可能创建出大量的子类，使子类的数量呈爆炸性增长。比如现在有 4 种型号的自行车，我们为每种自行车都定义了一个单独的类。现在要给每种自行车都装上前灯、尾灯和铃铛这 3 种配件。如果使用继承的方式来给每种自行车创建子类，则需要 4×3 = 12 个子类。但是如果把前灯、尾灯、铃铛这些对象动态组合到自行车上面，则只需要额外增加 3 个类。

这种给对象动态地增加职责的方式称为装饰者（decorator）模式。装饰者模式能够在不改变对象自身的基础上，在程序运行期间给对象动态地添加职责。跟继承相比，装饰者是一更轻便灵活的做法，这是一种“即用即付”的方式，比如天冷了就多穿一件外套，需要飞行时就在头上插一支竹蜻蜓，遇到一堆食尸鬼时就点开 AOE（范围攻击）技能。

## 模拟传统面向对象语言的装饰者模式

首先要提出来的是，作为一门解释执行的语言，给 JavaScript 中的对象动态添加或者改变职责是一件再简单不过的事情，虽然这种做法改动了对象自身，跟传统定义中的装饰者模式并不一样，但这无疑更符合 JavaScript 的语言特色。代码如下： 

```javascript
var obj = {
    name:'sevn',
    address:'深圳'
}
obj.address = obj.address + '福田区';
```

传统面向对象语言中的装饰者模式在 JavaScript 中适用的场景不多。通常我们并不大介意改变对象自身。

假设我们在编写一个飞机大战的游戏，随着经验的增加，我们操作的飞机对象可以升级成为更加厉害的飞机，一开始这些飞机只能发射普通的子弹，升到第二级的时候可以发射导弹，升到第三级的时候可以发射原子弹。

代码实现：

```javascript
const Plane = function(){};
Plane.prototype.fire = function(){
    console.log('发射普通子弹');
}
// 两个装饰类
const MissileDecorator = function(plane){
    this.plane = plane;
}

MissileDecorator.prototype.fire = function(){
    this.plane.fire();
    console.log('发射导弹');
}

const AtomDecorator = function(plane){
    this.plane = plane;			
}

AtomDecorator.prototype.fire = function(){
    this.plane.fire();
    console.log('发射原子弹');

}
// 测试
let plane = new Plane();
plane = new MissileDecorator(plane);
plane = new AtomDecorator(plane);
plane.fire();
// 发射普通子弹
// 发射导弹
// 发射原子弹
```

这种给对象增加职责的方式，并没有真正改变对象自身，而是将对象放入另一个对象之中，这些对象以一条链的方式进行引用，形成一个聚合对象。这些对象都拥有相同的接口（fire 方法），当请求达到链中的某个对象的时候，这个对象会执行自身的操作，随后把请求转发给链中的下一个对象。

因为装饰者对象和它所装饰的对象拥有一致的接口，所有它们对使用该对象的客户来说是透明的，被装饰的对象也并不需要了解它曾经被装饰过，这种透明性使得我们可以递归嵌套任意多个装饰者对象。



## 装饰者也是包装器

GoF 原想把装饰者（decorator）模式称为包装器（wrapper）模式.

从功能上而言，decorator 能很好地描述这个模式，但从结构上看，wrapper 的说法更加贴切。装饰者模式将一个对象嵌入另一个对象之中看，实际上相当于这个对象被另一个对象包装起来，形成一条包装链。请求随着这条链依次传递到所有的对象，每个对象都有处理这条请求的机会。

## 回到 JavaScript 的装饰者

JavaScript 语言动态改变对象相当容易，我们可以直接改写对象或者对象的某个方法，并不需要使用“类”来实现装饰者模式，代码如下：

```javascript
const plane = {
    fire:function(){
        console.log('发射普通子弹');
    }
}

const missileDecorator = function(){
    console.log('发射导弹');
}

const atomDecorator = function(){
    console.log('发射原子弹');
}

const fire1 = plane.fire;
plane.fire = function(){
    fire1();
    missileDecorator();
}

const fire2 = plane.fire;

plane.fire = function(){
    fire2();
    atomDecorator();
}
plane.fire();
```

## 装饰函数

在 JavaScript 中，几乎一切都是对象，其中函数又被称为一等对象。在平时的开发中，也许大部分时间都在跟函数打交道。在 JavaScript 中可以很方便地给某个对象扩展属性和方法，但却很难在不改懂某个函数源代码的情况下，给该函数添加一些额外的功能。在代码的运行期间，我们很难切入某个函数的执行环境。

要为函数添加一些功能，最简单粗暴的方式就是直接改写函数，但是这是最差的方法，直接违反了开发-封闭的原则。

可以通过保存原引用的方法就可以改写某个函数：

```javascript
let a = function(){
    console.log(1);
}

const _a = a;
a = function(){
    _a();
    console.log(1);
}
a();
```

这是实际开发中很常用的一种做法，比如我们想给 window 绑定 onload 时间，但是又不确定这个事件是不是已经被其他人绑定过了，为了避免覆盖掉之前的 window.onload 函数中的行为，我们一般都会保存好原先的 window.onload ，把它放入新的 window.onload 里面执行：

```javascript
window.onload = function(){
    console.log(1);
}

const _onload = window.onload || function(){};
window.onload = function(){
    _onload();
    console.log(2);
}
```

这样的代码当然是符合开放封闭原则的，我们在增加新功能的时候，确实没有修改原来的window.onload 代码，但是这种方式存在以下两个问题:


- 必须维护 _onload 这个中间变量，虽然看起来并不起眼，但如果函数的装饰链较长，或者需要装饰的函数很多，这些中间变量也会越来越多。
- 其中还遇到了 this 被劫持的问题，在 window.onload 的例子中没有这个烦恼，是因为调用普通函数 _onload时候，this 也指向 window。现在把 window.onload 改成 document.getElementById,代码如下：

```javascript
const _getElementById = document.getElementById;

document.getElementById = function(id){
    console.log('3');
    return _getElementById(id);
}
const button = document.getElementById('button');
// 输出： Uncaught TypeError: Illegal invocation 
```

此时_getElementById 是一个全局函数，当调用一个全局函数时，this 是指向 window 的，而 document.getElementById 方法的内部实现需要使用 this 引用，this 在这个方法内预期是指向 document，而不是 window, 这是错误发生的原因所以使用现在的方式给函数增加功能并不保险。

改进后的代码可以满足需求，我们要手动把 document 当作上下文 this 传入_getElementById：

```javascript
const _getElementById = document.getElementById;

document.getElementById = function(id){
    console.log('3');
    return _getElementById.apply(document,arguments);
}
const button = document.getElementById('button');
```



## 用 AOP 装饰函数

首先给出 Funtion.prototype.before 和 Function.prototype.after 方法：

```javascript
Function.prototype.before = function(beforefn){
    const _self = this;
    return function(){
        beforefn.apply(this,arguments);
        return _self.apply(this,arguments);
    }
}

Function.prototype.after = function(afterfn){
    const _self = this;
    return function(){
        const ret = _self.apply(this,arguments);
        afterfn.apply(this,arguments);
        return ret;
    }
}
```

Function.prototype.before 接受一个函数作为参数，这个函数即为新添加的函数，它转载了新添加的功能代码。

接下来把当前的 this 保存起来，这个 this 指向原函数，然后返回一个“代理”函数，这个“代理”函数只是结构上像代理而已，并不承担代理的职责（比如控制对象的访问等）。它的工作是把请求分别转发给新添加的函数和原函数，且负责保证它们的执行顺序，让新添加的函数在原函数之前执行（前置装饰），这样就实现了动态装饰的效果。

我们注意到，通过 Function.prototype.apply 来动态传入正确的 this，保证了函数在被装饰之后，this 不会被劫持。Function.prototype.after 的原理跟 Function.prototype.before 一模一样，唯一不同的地方在于让新添加的函数在原函数执行之后再执行。

下面来试试用 Function.prototype.before 的威力：

```javascript
document.getElementById = document.getElementById.before(function(){
    console.log('before');
}).after(function(){
    console.log('after');
});
const button = document.getElementById('button');	

window.onload = (window.onload || function(){}).after(function(){
    console.log(1)
}).after(function(){
    console.log(2);
}).after(function(){
    console.log(3)
});
```

值得提到的是，上面的 AOP 实现是在 Function.prototype 上添加 before 和 after 方法，但许多人不喜欢这种污染原型的方式，那么我们可以做一些变通，把原函数和新函数都作为参数传before 或者 after 方法：

```javascript
const before = function(fn,beforefn){
    return function(){
        beforefn.apply(this,arguments);
        return fn.apply(this,arguments);
    }
}

const after = function(fn,afterfn){
    return function(){
        const ret = fn.apply(this,arguments);
        afterfn.apply(this,arguments);
        return ret;
    }
}		

const a = before(function(){
    console.log('fn')
},function(){
    console.log('beforefn');
});
a();
```



## AOP 的应用实例

用 AOP 装饰函数的技巧在实际开发中非常有用，无论是业务代码的编写，还是框架层面，我们都可以把行为依照职责分成粒度更细的函数，随后通过装饰在它们合并在一起，这有助于我们编写一个送耦合和高复用性的函数。

### 数据统计上传

分离业务diam和数据统计代码，无论在什么语言，都是 AOP 的经典引用之一。在项目开发的结尾难免要加上很多统计数据的代码。，这些过程可能让我们被迫改动早已封装好的函数。

比如页面中有一个登录 button，点击 button 会弹出登录浮层，与此同时在进行数据上报。来统计有多少用户点击了这个登录 button：

```javascript
const showLogin = function(){
    console.log('打开登录浮层');
    log(this.getAttribute('tag'));
}

const log = function(tag){
    console.log('上报的标签：'+tag);
}
document.getElementById('button').onclick = showLogin;
```

我们看到在 showLogin 函数里，既要负责打开登录浮层，又要负责数据上报，这是两个层面的功能，在此处却被耦合在一个函数里。使用 AOP 分离之后，代码如下：

```java
let showLogin = function(){
    console.log('打开登录浮层');
}

const log = function(){
    console.log('上报的标签：'+this.getAttribute('tag'));
}

showLogin = showLogin.after(log);
document.getElementById('button').onclick = showLogin;
```

### 用 AOP 动态改变函数的参数

beforefn 和原函数 self  共用一组参数列表arguments，当我们在 beforefn 的函数体内改变 arguments 的时候，原函数 self 接收的参数列表自然也会变化

下面的例子展示了如何通过 Function.prototype.before 方法给函数 func 的参数 param 动态地添加属性 b：

```javascript
Function.prototype.before = function( beforefn ){ 
    var __self = this; 
    return function(){ 
        beforefn.apply( this, arguments );
        return __self.apply( this, arguments );
    } 
} 
let func = function(param){
    console.log(param)；
}
func = func.before(function(param){
    param.b = 'b';
});

func({a:'a'});
```

现在有一个用于发起 ajax 请求的函数，这个函数负责项目中所有的 ajax 异步请求：

```javascript
const ajax = function(type,url,param){
    console.dir(param);
    // 发起ajax 的代码
}
ajax( 'get', 'http:// xxx.com/userinfo', { name: 'sven' } ); 
```

上面的伪代码表示向后台 cgi 发起一个请求来获取用户信息，传递给 cgi 的参数是{ name: 'sven' }

ajax 函数在项目中一直运转良好，跟 cgi 的合作也很愉快。直到有一天，我们的网站遭受了CSRF 攻击。解决 CSRF 攻击最简单的一个办法就是在 HTTP 请求中带上一个 Token 参数。

假设我们已经有一个用于生成 Token 的函数：

```javascript
const getToken = function(){
    return 'Token';
}
// 给每个 ajax 请求都加上 Token 参数
const ajax = function( type, url, param ){ 
 param = param || {}; 
 Param.Token = getToken(); // 发送 ajax 请求的代码略... 
}; 

```

虽然已经解决了问题，但我们的 ajax 函数相对变得僵硬了，每个从 ajax 函数里发出的请求都自动带上了 Token 参数，虽然在现在的项目中没有什么问题，但如果将来把这个函数移植到其他项目上，或者把它放到一个开源库中供其他人使用，Token 参数都将是多余的。
也许另一个项目不需要验证 Token，或者是 Token 的生成方式不同，无论是哪种情况，都必须重新修改 ajax 函数。
为了解决这个问题，先把 ajax 函数还原成一个干净的函数：

```javascript
let ajax = function( type, url, param ){ 
 console.log(param);
}; 
// 把 Token 参数通过 Function.prototyte.before 装饰到 ajax 函数的参数 param 对象中：
const getToken = function(){
    return 'Token';
}
ajax = ajax.before(function(type,url,param){
    param.Token = getToken();
});
ajax( 'get', 'http:// xxx.com/userinfo', { name: 'sven' } ); 
// 从 ajax 函数打印的 log 可以看到，Token 参数已经被附加到了 ajax 请求的参数中:
// {name: "sven", Token: "Token"} 
```

明显可以看到，用 AOP 的方式给 ajax 函数动态装饰上 Token 参数，保证了 ajax 函数是一个相对纯净的函数，提高了 ajax 函数的可复用性，它在被迁往其他项目的时候，不需要做任何修改

### 插件式的表单验证

我们很多人写过许多表单验证的代码，在一个 Web 项目中，可能存在很多表单，如注册、登录、修改用户信息等、在表单数据提交给后台之前，常常要做一些校验，比如登录的时候需要验证用户名和密码是否为空，代码如下：

```javascript
const username = document.getElementById('username'),
password = document.getElementById('password'),
submitBtn = document.getElementById('submitBtn');

const formSubmit = function(){
    if(username.value === ''){
        return alert('用户名不能为空');
    }
    if(password.value === ''){
        return alert('密码不能为空');
    }
    const param = {
        username:username.value,
        password:password.value
    }
    // ajax( 'http:// xxx.com/login', param ); // ajax 具体实现略
}

submitBtn.onclick = function(){ 
    formSubmit(); 
} 
```

formSubmit 函数在此处承担了两个职责，除了提交 ajax 请求之外，还要验证用户输入的合法性。这种代码一来会造成函数臃肿，职责混乱，二来谈不上任何可复用性。
本节的目的是分离校验输入和提交 ajax 请求的代码，我们把校验输入的逻辑放到 validata函数中，并且约定当 validata 函数返回 false 的时候，表示校验未通过，代码如下:

```javascript
submitBtn.onclick = function(){ 
    formSubmit(); 
} 

const validata = function(){
    if(username.value === ''){
        alert('用户名不能为空');
        return false;
    }
    if(password.value === ''){
        alert('密码不能为空');
        return false;
    }
}
const formSubmit = function(){
    if(!validata()){
        return;
    }
    const param = {
        username:username.value,
        password:password.value
    }
    // ajax( 'http:// xxx.com/login', param ); // ajax 具体实现略
}
```

现在的代码已经有了一些改进，我们把校验的逻辑都放到了 validata 函数中，但 formSubmit函数的内部还要计算 validata 函数的返回值，因为返回值的结果表明了是否通过校验。
接下来进一步优化这段代码，使 validata 和 formSubmit 完全分离开来。首先要改写 Function. prototype.before，如果 beforefn 的执行结果返回 false，表示不再执行后面的原函数，代码如下：

```javascript
Function.prototype.before = function(beforefn){
    const _self = this;
    return function(){
        if(!beforefn.apply(this,arguments)){
            return;
        }
        return _self.apply(this,arguments);
    }
}
const validata = function(){
    if(username.value === ''){
        alert('用户名不能为空');
        return false;
    }
    if(password.value === ''){
        alert('密码不能为空');
        return false;
    }
}
let formSubmit = function(){
    const param = {
        username:username.value,
        password:password.value
    }
    // ajax( 'http:// xxx.com/login', param ); // ajax 具体实现略
}
formSubmit = formSubmit.before(validata);
submitBtn.onclick = function(){ 
    formSubmit(); 
} 
```

在这段代码中，校验输入和提交表单的代码完全分离开来，它们不再有任何耦合关系，formSubmit = formSubmit.before( validata )这句代码，如同把校验规则动态接在 formSubmit 函数之前，validata 成为一个即插即用的函数，它甚至可以被写成配置文件的形式，这有利于我们分开维护这两个函数。再利用策略模式稍加改造，我们就可以把这些校验规则都写成插件的形式，用在不同的项目当中。

值得注意的是，因为函数通过 Function.prototype.before 或者 Function.prototype.after 被装饰之后，返回的实际上是一个新的函数，如果在原函数上保存了一些属性，那么这些属性会丢失。代码如下：

```javascript
var func = function(){ 
 alert( 1 ); 
} 
func.a = 'a'; 
func = func.after( function(){ 
 alert( 2 ); 
}); 
alert ( func.a ); // 输出：undefined 
```

另外，这种装饰方式也叠加了函数的作用域，如果装饰的链条过长，性能上也会受到一些影响

## 装饰者模式和代理模式

这两种模式的结构看起来非常相像，这两种模式都描述了怎样为对象提供一定程度上的引用，它们的实现部分都保留了对另外一个对象的引用，并且向那个对象发送请求。

代理模式和装饰者模式最重要的区别在于它们的意图和设计目的。代理模式的目的是，当直接访问本体不方便或者不符合需求的时候，为这个本体提供一个替代者。本体定义了关键功能， 而代理提供或拒绝对它的访问，或者在访问本体之前做一些额外的事情。装饰者模式的作用就为对象动态加入行为。换句话说，代理模式强调的是一种关系 （Proxy） 与它实体的关系，这种关系可以静态的表达，也就说，这种关系是一开始就可以被确定的。而装饰者模式用于一开始不能确定对象的全部功能时。代理模式通常只有一层代理-本体的引用，而装饰者模式经常会形成一条长长的装饰链。

在虚拟代理实现图片预加载的例子中，本体负责设置 img 节点的 src，代理则提供了预加载的功能，这看起来也是“加入行为”的一种方式，但这种加入行为的方式和装饰者模式的偏重点是不一样的。装饰者模式是实实在在的为对象增加新的职责和行为，而代理做的事情还是跟本体一样，最终都是设置 src。但代理可以加入一些“聪明”的功能，比如在图片真正加载好之前，先使用一张占位的 loading 图片反馈给客户。

## 小结

本章通过数据上报、统计函数的执行时间、动态改变函数参数以及插件式的表单验证的这 4 个例子，我们了解到了装饰函数，它是 JavaScript 中独特的装饰者模式。这种模式的在实际开发中非常有用，除了上面提到的例子，在框架开发中也非常有用。我们希望框架里面的函数提供的是一些稳定而且可以移植的功能，那些个性化的功能在框架之外的动态装饰上去，这可以避免为了让框架有更多的功能，而去使用一些 if-else 语句预测用户的实际需要。