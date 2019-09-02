---
title: JavaScript设计模式——单例模式
date: 2019-02-15 18:30:00
tags: JavaScript设计模式
categories: 
	- JavaScript设计模式



---

学习曾探的 《JavaScript设计模式与开发实践》并做记录。

[书籍的购买链接](http://www.ituring.com.cn/book/1632/)

设计模式的定义是：在面向对象软件设计过程中针对特定问题的简洁而优雅的解决方案。

通俗一点，设计模式是在某种场合下对某个问题的一种解决方案。是给面向对象软件开发中的一些好的设计取个名字。

# JavaScript设计模式——单例模式

单例模式的定义是：保证一个类仅有一个实例，并提供一个访问它的全局访问点

单例模式是一种常用的模式，有一些对象我们往往只需要一个，比如线程池、全程缓存、浏览器中的 window 对象等等。在 JavaScript 开发中，单例模式用途同样广泛。试想一下，当我们单机登录按钮的时候，页面中只会出现一个登录浮窗，而这个登录浮窗是唯一的，无论单击多少次登录按钮，这个浮窗都只会被创建一次，那么这个登录浮窗就适合用单例模式来创建。

## 实现单例模式

要实现一个标准的单例模式并不复杂，无非使用一个变量来标识当前是否已经为某个类创建过对象，如果是，则在下一次获取该类的实例的时候，直接返回之前创建的对象。代码如下:

```javascript
var Singleton = function (name) {
    this.name = name;
    this.instance = null;
}

Singleton.prototype.getName = function () {
    console.log(this.name);
}

Singleton.getInstance = function (name) {
    if (!this.instance) {
        this.instance = new Singleton(name);
    }
    return this.instance;
}

var a = Singleton.getInstance('sven1');
var b = Singleton.getInstance('sven2');

console.log(a === b); // true
```

或者

```javascript
var Singleton = function (name) {
  this.name = name;
}
Singleton.prototype.getName = function () {
  console.log(this.name);
}
Singleton.getInstance = (function () {
  var instance = null;
  return function (name) {
    if (!instance) {
      instance = new Singleton(name);
    }
    return instance;
  }
})()
```

我们通过 Singleton.getInstance 来获取 Singleton 类的唯一对象，这种方式相对简单，但有一个问题，就是增加了这个类的 “不透明性”。Singleton 类的使用者必须知道这是一个单例类，跟以往通过 new XX 的方式来获取对象不同，这里用到是 Singleton.getInstance 来获取对象

## 透明的单例模式

实现一个 “透明” 的单例类，用户从这个类中创建对象的时候，可以像使用其他任何普通类一样。在下面的例子中，我们将使用 CreateDiv 单例类，它的作用是负责在页面中创建唯一的 div 节点，代码如下：

```javascript
var CreateDiv = (function () {
    var instance;
    //CreateDiv 的构造函数实际上负责了两件事情，第一是创建对象和执行初始化 init 方法，第二是保证只有一个对象。
    var CreateDiv = function (html) {
        if (instance) {
            return instance;
        }
        this.html = html;
        this.init();
        return instance = this;
    }
    CreateDiv.prototype.init = function () {
        var div = document.createElement('DIV');
        div.innerHTML = this.html;
        document.body.appendChild(div);
    }
    return CreateDiv;
})();

var c = new CreateDiv('sven1');
var d = new CreateDiv('sven2');
console.log(c === d);
```

虽然现在是完成了一个透明的单例类的编写，但是它有一些缺点。

为了把 instance 封装起来，使用自执行匿名函数和闭包，并且让这个匿名函数返回真正的 Singleton 构造方法，增加了一些程序的复杂度，阅读起来也不是很舒服。

## 用代理实现单例模式

在上面的代码中进行改造，首先在 CreateDiv 构造函数中，把负责管理单例的代码移出去，让它成为一个普通的创建 div 的类：

```javascript
var CreateDiv = function (html) {
  this.html = html;
  this.init();
}

CreateDiv.prototype.init = function () {
  var div = document.createElement('div');
  div.innerHTML = this.html;
  document.body.appendChild(div);
}
```

接下来映日 代理类 ProxySingletonCreateDiv:

```javascript
var ProxySingletonCreateDiv = (function () {
    var instance;
    return function (html) {
        if (!instance) {
            instance = new CreateDiv(html);
        }
        return instance;
    }
})();
```

通过引用代理类的方法，我们同样完成了一个单例模式的编写，跟之前不同的是，现在我们把负责管理单例的逻辑移到了代理类中，这样一来，CreateDiv 就变成了一个普通的类，它跟代理类组合起来可以达到单例模式的效果。

这个例子是缓存代理的应用之一。

## JavaScript 中的单例模式

**单例的核心是确保只有一个实例，并提供全局访问。**

单例对象是从 “类” 中创建而来的，在以类为中心的语言中，这是很自然的做法。比如在 Java 中，需要某个对象，就必须先定义一个，对象总是从类中创建的。

但 JavaScript 其实一门 无类的语言，正是因为这样，生搬单例模式的概念并无意义。

全局变量不是单例模式，但在 JavaScript 开发中，我们经常会把全局变量当做单例来使用。

例如：

```javascript
var a = {}; 
```

当用这种方式创建对象 a 时，对象 a 确实是独一无二的。如果 a 变量被声明在全局作用域下，则我们可以在代码中的任何位置使用这个变量，全局变量提供给全局访问是理所当然的。这样就满足了单例模式的两个条件。

但是全局变量存在很多问题，它很容易造成命名空间污染。

我们有必要尽量减少全局变量的使用，即使需要，也要把它的污染降到最低。以下几种方式可以相对降低全局变量带来的命名污染。

### 1.使用命名空间

适当地使用命名空间，并不会杜绝全局变量，但是可以减少全局变量的数量

最简单的方法依然是使用对象字面量的方式：

```javascript
var namespace1 = {
    a:function(){
        console.log(1)
    },
    b:function(){
        console.log(2)
    }
}
// 把 a 和 b 都定义为 namespace1 的属性，可以减少变量和全局作用域打交道的机会。另外，还可以动态地创建命名空间。
var MyApp = {};
MyApp.namespace = function(name){
    var parts = name.split('.');
    var current = MyApp;
    for(var i in parts){
        if(!current[parts[i]]){
           current[parts[i]] = [];
        }
        current = current[parts[i]]
    }
}
MyApp.namespace('event');
MyApp.namespace('dom.style');
console.log(MyApp);

// 上面代码等价于 
var MyApp = {
    event:{},
    dom:{
        style:{}
    }
}
```

### 2.使用闭包封装私有变量

这种方法把一些变量封闭在闭包的内部，只暴露一些接口跟外界通信：

```javascript
var user = (function(){
    var __name = 'sven',
        __age = 29;
    return {
        getUserInfo:function(){
            return __name + '_' + __age;
        }
    }
})();
```

这里用下划线来约定私有变量，它们被封装在闭包产生的作用域中，外部是访问不到这两个变量的，这就避免了对全局的命令污染。（现在没有这样来定义私有变量，一般是使用 Symbol）

## 惰性单例

惰性单例指的是需要的时候才创建对象实例。惰性单例是单例模式的重点，这种技术是实际开发中非常有用的，有用的程度可能超出了我们的想象。instance 实例对象总是在我们调用 Singleton.getInstance 的时候才被创建，而不是在页面加载好的时候就被创建。代码如下：

```javascript
Singleton.getInstance = (function(){
    var instance = null;
    return function(name){
        if(!instance){
           	instance = new Singelton(name);
        }
        return instance;
    }
})();
```

不过这是基于“类” 的单例模式，基于 “类” 的单例模式在 JavaScript 中并不适用。下面用 WebQQ 的登录浮窗为例子，介绍与全局变量结合实现惰性的实例。

 假设我们是 WebQQ 的开发人员（网址是web.qq.com），当点击左边导航里 QQ 头像时，会弹出一个登录浮窗很明显这个浮窗在页面里总是唯一的，不可能出现同时存在两个登录窗口的情况。

第一种解决方案就是在页面加载完成的时候创建好这个 div 浮窗，这个浮窗一开始肯定是隐藏状态，当用户点击登录按钮的时候就显示：

```html
<html>
    <body>
        <button id="loginBtn">登录</button>
    </body>
    <script>
        var loginLayer = (function(){
            var div = document.createElement('div');
            div.innerHTML = '我是登录浮窗'；
            div.style.display = 'none';
            document.body.appendChild(div);
            return div;
        })();
        document.getElementById('loginBtn').onClick=function(){
            loginLayer.style.display = 'block';
        };
    </script>
</html>
```

这种方式有一个问题就是，也许我们进入 WebQQ 只是玩玩游戏或者看看天气，根本不需要进行登录操作，因为登录浮窗总是一开始就被创建好，那么很有可能将白白浪费一些 DOM 节点。

改变一下代码，使用户点击登录按钮的时候才开始创建浮窗

```html
<html>
    <body>
        <button id="loginBtn">登录</button>
    </body>
    <script>
        var createLoginLayer = function(){
            var div = document.createElement('div');
            div.innerHTML = '我是登录浮窗'；
            div.style.display = 'none';
            document.body.appendChild(div);
            return div;
        };
        document.getElementById('loginBtn').onClick=function(){
            var loginLayer = createLoginLayer();
            loginLayer.style.display = 'block';
        };
    </script>
</html>
```

虽然现在达到了惰性的目的，但失去了单例的效果，当我们点击登录按钮的时候，都会创建一个新的登录浮窗 div ，虽然我们可以在点击浮窗上的关闭时把这个浮窗从页面中删除，但这样频繁地创建和删除节点明显是不合理的，也是不必要的。

可以用一个变量来判断是否已经创建过登录浮窗

```javascript
var createLoginLayer = (function(){
    var div;
    return function(){
        if(!div){
            div = document.createElement('div');
            div.innerHTML = '我是登录浮窗'；
            div.style.display = 'none';
            document.body.appendChild(div);           
        }
        return div; 
    }
})();
document.getElementById('loginBtn').onClick=function(){
    var loginLayer = createLoginLayer();
    loginLayer.style.display = 'block';
};
```

## 通用的惰性单例

上面虽然完成了一个可用的惰性单例，但是它还有如下的一些问题。

- 代码仍然是违反单一职责原则的，创建对象和管理单例的逻辑都放在 createLoginLayer 对象内部。
- 如果我们下次需要创建一个页面中唯一的 iframe 的时候，或者 script 标签，用来跨域请求数据，就必须如法炮制，把 createLoginLayer 函数几乎抄多一遍

```javascript
var createIframe = (function(){
    var iframe;
    return function(){
        if(!iframe){
            iframe = document.createElement('iframe');
            iframe.style.display = 'none';
            document.body.appendChild(iframe);           
        }
        return iframe; 
    }
})();
```

可以看出，实际上管理单例的逻辑其实是完全可以抽象出来的，这个逻辑始终是一样的：用一个变量来标志是否创建了对象，如果是，则在下次直接返回这个已经创建好的对象。

```javascript
var obj;
if(!obj){
   obj = xxx;
}
```

我们把如何管理单例的逻辑从原来的代码中抽离出来，这些逻辑被封装在 getSingle 函数内部，创建对象的方法 fn 被当做参数动态传入 getSingle 函数中：

```javascript
var getSingle = function (fn) {
  var result;
  return function () {
    return result || (result = fn.apply(this, arguments))
  }
}
```

接下来用于创建登录浮窗的方法用 fn 的形式传入到这个 getSingle。我们可以传入任何我们想创建的东西。之后让 getSingle 返回一个新的函数，并用一个变量 result 来保存 fn 的计算结果。result 变量因为身在闭包中，它永远不会被销毁。在将来的请求中，如果 result 已经被赋值了，那么它将返回这个值。

```javascript
var createLoginLayer = function(){
    var div = document.createElement('div');
    div.innerHTML = '我是登录浮窗'；
    div.style.display = 'none';
    document.body.appendChild(div);  
    return div;
}

var createSingleLoginLayer = getSingle(createLoginLayer);
document.getElementById('loginBtn').onClick=function(){
    var loginLayer = createSingleLoginLayer();
    loginLayer.style.display = 'block';
};
// 下面再试试创建唯一的 iframe 用于动态加载第三方页面
var createSingleIframe = getSingle(function(){
    var iframe = document.createElement('iframe');
    document.body.appendChild(iframe);
    return iframe;
});
document.getElementById('loginBtn').onClick=function(){
    var loginLayer = createSingleIframe();
    loginLayer.src = 'http://laibh.top';
};
```

上面的例子中，把创建实例对象的职责和管理单例的职责分别放置在了两个方法里，这两个方法可以独立变化而互不影响，当它们连接在一起的时候，就完成了创建唯一实例对象的功能。

这种单例模式的用途远不止创建对象，比如我们通常渲染页面中的一个列表之后，接下来要给这个列表绑定一个 click 事件，如果是通过 ajax 动态往列表里追加数据，在使用事件代理的前提下，click 事件实际上只需要在第一次渲染列表的时候被绑定一次，但是我们不想去判断当前是否是第一次渲染列表，如果借助JQ，我们通常选择给节点绑定 one 事件：

```javascript
var bindEvent = function () {
  $('div').one('click', function () {
    console.log('click');
  });
}
var render = function () {
  console.log('开始渲染列表');
  bindEvent();
}
render();
render();
render();
```

如果使用 getSingle 函数也可以达到一样的效果，代码如下：

```javascript
var bindEvent = getSingle(function(){
    document.getElementById('div1').onclick = function(){
        console.log('click');
    };
    return true;
});

var render = function () {
  console.log('开始渲染列表');
  bindEvent();
}

render();
render();
render();
```

可以看到 render 函数 和 bindEvent 函数分别都执行了 3 次，但是 div 实际上只被绑定了一个事件。



## 小结

单例模式是一种简单但非常实用的模式。特别是惰性单例技术，在合适的时候才创建对象，并只创建唯一的一个。更奇妙的是，创建对象和管理单例的职责被分布在两个不同的方法中，这两个方法组合起来才具有单例模式的威力。