---
title: JavaScript设计模式——代理模式
date: 2019-02-20 11:30:00
tags: JavaScript设计模式
categories: 
	- JavaScript设计模式
---

学习曾探的 《JavaScript设计模式与开发实践》并做记录。

[书籍的购买链接](http://www.ituring.com.cn/book/1632/)

设计模式的定义是：在面向对象软件设计过程中针对特定问题的简洁而优雅的解决方案。

通俗一点，设计模式是在某种场合下对某个问题的一种解决方案。是给面向对象软件开发中的一些好的设计取个名字。

# JavaScript设计模式——代理模式

代理模式是为了一个对象提供一个代用品或占位符，以便控制对它的访问。

代理模式在生活中可以找到很多场景。比如：明星都有经纪人作为代理。如果想请明星来办一场商业演出，只能联系他的经纪人。经纪人会把商业演出的细节和报酬谈好后，再把合同交给明星签。

代理模式的关键是，当客户不方便直接访问一个对象或者不满足需求的时候，提供一个替身对象来控制这个对象的访问，客户实际上访问的是替身对象。替身对象对请求做出一些处理之后，再把请求转交给本体对象。

## 第一个例子

在四月一个晴朗的早晨，小明遇见了他的百分百女孩，我们暂且称呼小明的女神为A。两天之后，小明决定给 A 送一束花来表白。刚好小明打听到 A 和他有一个共同的朋友 B，于是内向的小明决定让 B 来代替自己完成送花这件事情。

用代理来描述小明追女神的过程，先看看不用代理模式的情况：

```javascript
  var Flower = function () { };
  var xiaoming = {
    sendFlower: function (target) {
      var flower = new Flower();
      target.receiveFlower(flower);
    }
  }

  var A = {
    receiveFlower: function (flower) {
      console.log('收到花：' + flower);
    }
  }
  xiaoming.sendFlower(A);
```

接着引入代理B

```javascript
  var Flower = function () { };
  var xiaoming = {
    sendFlower: function (target) {
      var flower = new Flower();
      target.receiveFlower(flower);
    }
  }
  
  var B = {
    receiveFlower: function (flower) {
      A.receiveFlower(flower);
    }
  }
  
  var A = {
    receiveFlower: function (flower) {
      console.log('收到花：' + flower);
    }
  }
  xiaoming.sendFlower(B);
```

在这里，引入代理模式看起来毫无作用，反而会复杂代码，它所作的只是把请求简单地转交给了本体。

现在我们改变故事的背景设定，假设当A 在心情好的时候收到花，小明表白成功的几率有 60%，而当 A在心情差的时候收到花，小明表白的成功率无限趋近于0.

小明跟A才刚刚认识了两天，无法判别A什么时候心情好，但是B是A的好朋友，她会监听A的心情变化，然后选择 A 心情好的时候把花转交给 A。

代码如下：

```javascript
var Flower = function () { };
var xiaoming = {
sendFlower: function (target) {
  var flower = new Flower();
  target.receiveFlower(flower);
}
}
var B = {
receiveFlower: function (flower) {
  A.listenGoodMood(function () { // 监听 A 的好心情
    A.receiveFlower(flower);
  })
}
}
var A = {
receiveFlower: function (flower) {
  console.log('收到花：' + flower);
},
listenGoodMood: function (fn) {
  setTimeout(function () { // 假设10秒后 A 的心情变好了
    fn();
  }, 10000);
}
}
xiaoming.sendFlower(B);
```

## 保护代理和虚拟代理

虽然这只是一个虚拟的例子，但是我们可以从中找到两种代理模式的身影。代理 B 可以帮助 A 过滤掉一些请求，比如送花的人中年龄太大的或者是经济能力不够的。这种请求就可以在代理 B 处被过滤掉。这种代理叫做保护代理。A 和 B 一个充当白脸，一个充当 黑脸。白脸 A 继续保持良好的女神形象，不希望直接拒绝任何人，于是找了 黑脸 B 来控制对 A 的访问。

另外，假设在现实中花的价格不菲，导致在程序世界中，new Flower 也是一个代价昂贵的操作，那么我们可以把 new Flower 的操作交给代理 B 去执行，代理 B 会选择在 A 心情好的时候再执行 new Flower ，这是代理模式的另一种形式，叫做虚拟代理。虚拟代理把一些操作开销很大的对象，延迟到真正需要它的时候去创建。

代码如下：

```javascript
  var B = {
    receiveFlower: function (flower) {
      A.listenGoodMood(function () { // 监听 A 的好心情
        var flower = new Flower();
        A.receiveFlower(flower);
      })
    }
  }
```

保护代理用于控制不同权限的对象对目标对象的访问，但在 JavaScript 中并不容易实现保护代理，因为我们无法判断谁访问了某个对象。而虚拟代理是最常见的一种代理模式。

## 虚拟代理实现图片预加载

在 Web 开发中，图片预加载是一种常用的技术，如果直接给某个 img 标签节点设置 src 属性，由于图片过大或者是网络不佳，图片的位置常常有段实际是一片空白。常见的做法是先用一张 loading 图片占位，然后用异步的方式加载图片，等图片加载好了再把它填充到 img 节点中，这种场景就很适合使用虚拟代理。

下面实现这个虚拟代理，首先创建一个普通的本体对象，这个对象负责往页面中创建一个 img 标签，并且提供一个对外的 setSrc 接口，便可以给该 img 标签设置src 属性：

```javascript
var myImage = (function () {
var imgNode = document.createElement('img');
document.body.appendChild(imgNode);
return {
  setSrc: function (src) {
    imgNode.src = src;
  }
}
})();
myImage.setSrc('https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1550806593869&di=f2c243dcd52c67fb8cb9c730bfe97cff&imgtype=0&src=http%3A%2F%2Fimg1.xcarimg.com%2Fexp%2F2872%2F2875%2F2937%2F20101220130509576539.jpg');
```

F12 调开浏览器调式，切换到 Network ，禁止缓存，把速度调为 5KB/s ，可以看到页面中在图片加载完成之前，有一段长长的空白时间。

现在开始引入代理对象 proxyImage,通过这个代理对象，在图片被真正加载好之前，页面中将会出现一张占位图，来提示用户图片正在加载：

```javascript
var myImage = (function () {
    var imgNode = document.createElement('img');
    document.body.appendChild(imgNode);
    return {
        setSrc: function (src) {
            imgNode.src = src;
        }
    }
})();
var proxyImage = (function () {
    var img = new Image;
    img.onload = function () {
        myImage.setSrc(this.src);
    }
    return {
        setSrc: function (src) {
            myImage.setSrc('file:///C:/Users/Administrator/Desktop/timg.gif');
            img.src = src;
        }
    }
})();
proxyImage.setSrc('https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1550806593869&di=f2c243dcd52c67fb8cb9c730bfe97cff&imgtype=0&src=http%3A%2F%2Fimg1.xcarimg.com%2Fexp%2F2872%2F2875%2F2937%2F20101220130509576539.jpg');		var myImage = (function () {
    var imgNode = document.createElement('img');
    document.body.appendChild(imgNode);
    return {
        setSrc: function (src) {
            imgNode.src = src;
        }
    }
})();
var proxyImage = (function () {
    var img = new Image;
    img.onload = function () {
        myImage.setSrc(this.src);
    }
    return {
        setSrc: function (src) {
            myImage.setSrc('file:///C:/Users/Administrator/Desktop/timg.gif');
            img.src = src;
        }
    }
})();
proxyImage.setSrc('https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1550806593869&di=f2c243dcd52c67fb8cb9c730bfe97cff&imgtype=0&src=http%3A%2F%2Fimg1.xcarimg.com%2Fexp%2F2872%2F2875%2F2937%2F20101220130509576539.jpg');
```

现在我们通过 proxyImage 间接地访问 MyImage 。proxyImage 控制了客户对 MyImage 的访问，并在此过程中加入了一些额外的操作，比如在真正的图片加载之前，先把 img 节点的 src 设置为本地的一张 loading 图片。

## 代理的意义

不用代理的预加载图片函数实现如下:

```javascript
var MyImage = (function () {
    var imgNode = document.createElement('img');
    document.body.appendChild(imgNode);
    var img = new Image;
    img.onload = function () {
        imgNode.src = img.src;
    };
    return {
        setSrc: function (src) {
            imgNode.src = 'file:///C:/Users/Administrator/Desktop/timg.gif';
            img.src = src;
        }
    }
})();	
MyImage.setSrc('https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1550806593869&di=f2c243dcd52c67fb8cb9c730bfe97cff&imgtype=0&src=http%3A%2F%2Fimg1.xcarimg.com%2Fexp%2F2872%2F2875%2F2937%2F20101220130509576539.jpg');
```

为了说明代理的意义，引入一个面向对象设计的原则——单一职责原则。

单一职责原则指的是，就一个类（通常也包括对象和函数等）而言，应该仅有一个引起它变化的原因。如果一个对象承担了多项职责，就意味着这个对象将变得巨大，引起它的变化原因可能有很多个。面向对象的设计鼓励将行为分布到细粒度的对象之中，如果一个对象承担的职责过多，等于把这些职责耦合到了一起，这种耦合导致脆弱和低内聚的设计。当变化发生的时候，设计可能会遭到破坏。

职责被定义为引起变化的原因。上段代码中 MyImage 对象除了负责给 img 节点设置 src 外，还要负责预加载图片。我们正在处理一个职责的时候，有可能因为其强耦合性影响另外一个职责的实现。

另外，在面向对象的程序设计中，在大多数情况下，若违反了其他任何规则，同时将违反开放——封闭原则。如果我们只是在网络中获取一些体积很小的图片，或者 5 年后的网速快到根本不需要再预加载了。我们可能希望把预加载的图片这段代码从 MyImage 中删除掉。这时候就不得不改动 MyImage 对象了。

实际上，我们需要的只是给 img 节点设置 src ,预加载图片只是一个锦上添花的功能。如果能把这个操作放在另一个对象中，自然是一个最好的方法。于是代理的作用就在这里体现出来了，代理负责预加载图片，加载完之后，把请求交给本体 MyImage。

通过代理对象，实际上给系统添加了新的行为。这是符合开发——封闭的原则。给 img 节点设置 src 和 图片预加载这里两个功能，被隔离在了两个对象里面，它们可以各自变化而不会影响对对方。



## 代理和本体接口的一致性

上面的例子中，关键的是代理对象和本体都提供了 setSrc 方法，在客户看来，代理对象和本体对象是一致的，代理接手请求的过程对于用户来说是透明的，用户并不清楚和本体的区别，这样做有两个好处：

1. 用户可以放心请求代理，他只关心是否可以拿到想要的结果。
2. 在任何使用本体的地方都可以替换成使用代理。

Java 等语言中，代理和本体都需要显式地实现同一个接口，一方面接口要保证它们会拥有同样的方法，另一方法，面向接口编程迎合依赖倒置的原则，通过接口进行向上转型，从而避开了编译器的类型检查，代理和本体可以被替换使用。

在 Java 这种动态类型语言中，我们有时候通过鸭子类型来检测代理和本体是否都实现了 setSrc 方法，另外大多数时候甚至干脆不做检测，全部依赖程序员的自觉性，这对于程序的健壮性是有影响的。

如果是代理对象和本体对象都是一个函数（函数也是对象），函数必然都能被执行，则可以认为它们拥有一致的"接口“。

```javascript
var myImage = (function () {
var imgNode = document.createElement('img');
document.body.appendChild(imgNode);
return function (src) {
  imgNode.src = src;
}
})();

var proxyImage = (function () {
var img = new Image;
img.onload = function () {
  myImage(this.src);
}
return function (src) {
  myImage('本地加载图片');
  img.src = src;
}
})();
proxyImage('https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1550806593869&di=f2c243dcd52c67fb8cb9c730bfe97cff&imgtype=0&src=http%3A%2F%2Fimg1.xcarimg.com%2Fexp%2F2872%2F2875%2F2937%2F20101220130509576539.jpg')
```



## 虚拟代理合并 HTTP 请求

有这么一个场景：每周我们都要写一份工作周报，周报要交给总监批阅。总监手下管理着 150 个员工，如果我们每个人直接把周报交给总监，那总监可能要把一整周的时间都花在查看邮件上面。

现在我们把周报都发给各自的组长，组长作为代理，把组内成员的周报提炼合并成一份后一次性交给总监，这样一来，总监的邮件便清净了很多。

这样的例子在 Web 开发中很常见，网络请求也许是最大的开销。假设我们在做一个文件同步的功能，当我们选中一个 checkbox 的时候，它对应的文件就会被同步到另一台备用服务器上面。

页面中放置好这些 checkbox 节点:

```html
<input type="checkbox" id="1" />1
<input type="checkbox" id="2" />2
<input type="checkbox" id="3" />3
<input type="checkbox" id="4" />4
<input type="checkbox" id="5" />5
<input type="checkbox" id="6" />6
<input type="checkbox" id="7" />7
<input type="checkbox" id="8" />8
<input type="checkbox" id="9" />9
```

接下来，给这些 checkbox 绑定点击事件，并在点击的同时往另一台服务器同步文件：

```javascript
  var synchronousFile = function (id) {
    console.log('开始同步文件，id为：' + id);
  }

  var checkbox = document.getElementsByTagName('input');

  for (var i = 0, c; c = checkbox[i++];) {
    c.onclick = function () {
      if (this.checked) {
        synchronousFile(this.id);
      }
    }
  }
```

当我们选中 3 个 checkbox 的时候，依次往服务器发送了 3 次同步文件的请求。而点击一个 checkbox 并不是很复杂的操作，可以预见，如此频繁的网络请求将会带来相当大的开销。

解决方案是，我们可以同一个代理函数 proxySynchronousFile 来收集一段时间之内的请求，最后一次性发送给服务器。比如我们等待了2秒之后才把这2秒之内的需要同步的文件ID 打包发送给 服务器，如果不是对实时性要求很高的系统，2秒的延迟不会带来太大的副作用，却能大大减轻服务器的压力。代码如下：

```javascript
var synchronousFile = function (id) {
    console.log('开始同步文件，id为：' + id);
}
  var proxySynchronousFile = (function () {
    var cache = [], // 保存一段时间内需要同步的 ID 
      timer; // 定时器
    return function (id) {
      cache.push(id);
      if (timer) {
        return;
      }
      timer = setTimeout(function () {
        synchronousFile(cache.join(','));
        // console.log(cache);
        clearTimeout(timer);
        timer = null;
        cache.length = 0;
      }, 2000);
    }
  })();
var checkbox = document.getElementsByTagName('input');

for (var i = 0, c; c = checkbox[i++];) {
    c.onclick = function () {
        if (this.checked) {
            proxySynchronousFile(this.id);
        }
    }
}
```

## 虚拟代理在惰性加载中的应用

作者我曾经写过一个 mini 控制台的开源项目 miniConsole.js，这个控制台可以帮助开发者在 IE 浏
览器以及移动端浏览器上进行一些简单的调试工作。调用方式很简单：

```javascript
miniConsole.log(1); 
```

这句话会在页面中创建一个 div，并且把 log 显示在 div 里面。miniConsole.js的代码量大概有1000行左右，也许我们并不想一开始就加载这么大的JS文件，因为也许并不是每个用户都需要打印 log。我们希望在有必要的时候才开始加载它，比如当用户按下 F2 来主动唤出控制台的时候。

在 miniConsole.js 加载之前，为了能够让用户正常地使用里面的 API，通常我们的解决方案是用一个占位的 miniConsole 代理对象来给用户提前使用，这个代理对象提供给用户的接口，跟实际的 miniConsole 是一样的。

用户使用这个代理对象来打印 log 的时候，并不会真正在控制台内打印日志，更不会在页面中创建任何 DOM 节点。即使我们想这样做也无能为力，因为真正的 miniConsole.js 还没有被加载。

于是，我们可以把打印 log 的请求都包裹在一个函数里面，这个包装了请求的函数就相当于其他语言中命名模式的 Command 对象。随后这些函数将全部放到缓存队列中，这些逻辑都是在 miniConsole 代理对象中完成实现的。等用户按下 F12 的召唤出控制台的时候，才开始真正加载 miniConsole 的代码，加载完成之后将遍历的 miniConsole 代理对象中缓存函数队列，同时依次执行它们。

未加载真正的 miniConsole.js 之前的代码如下：

```javascript
var cache = [];
var miniConsole = {
    log:function(){
        var args = arguments;
        cache.push(function(){
            return miniConsole.log.apply(miniConsole,args);
        });
    }
}
miniConsole.log(1);
```

当用户按下 F12 的时候，开始真正加载 miniConsole.js ,代码如下：

```javascript
var handler = function(ev){
    if(ev.keyCode === 113){
    	var script = document.createElement('script');
        script.onload = function(){
            for(var i=0,fn;fn=cache[i++]){
                fn();
            }
        }
        script.src = 'miniConsole.js';
        document.getElementsByTagName('head')[0].appendChild(script);
    }
}
document.body.addEventListener('keydown',handler,false);

// miniConsole.js 代码
miniConsole = {
    log:function(){
        // 真正的代码
        console.log(Array.prototype.join.call(argument));
    }
}
```

另外这里要注意，我们要保证 F12 被重复按下的时候，miniConsole.js 只被加载一次。另外，我们整理一下 miniConsole 代理对象的代码，使它成为一个标准的虚拟对象。

```javascript
var miniConsole = (function(){
    var cache = [];
    var handler = function(ev){
        if(ev.keyCode === 113){
            var script = document.createElement('script');
            script.onload = function(){
                for(var i=0,fn;fn=cache[i++]){
                    fn();
                }
            }
            script.src = 'miniConsole.js';
            document.getElementsByTagName('head')[0].appendChild(script);
            document.body.removeEventListener('keydown',handler); // 只加载一次 miniConsole.js
        }        
    }
    document.body.addEventListener('keydown',handler,false);
    return{
        log:function(){
            var args = arguments;
            cache.push(function(){
                return miniConsole.log.apply(miniConsole,args);
            });
        }
    }
})();
miniConsole.log(11);
// miniConsole.js 代码
miniConsole = {
    log:function(){
        // 真正的代码
        console.log(Array.prototype.join.call(argument));
    }
}
```



## 缓存代理

缓存代理可以为一些开销大的计算结果提供暂时的存储，在下次运算的时候，如果传递进来参数跟之前的一样，则可以直接返回前面存储的运算结果。

### 计算乘积

编写一个简单的求乘积程序作为演示：

```javascript
var mult = function(){
    console.log('开始计算乘积');
    var a = 1;
    for(var i=0,l = arguments.length;i<l;i++){
        a = a * arguments[i];
    }
    return a;
}
mult(2,4); // 输出：6
mult(2,3,4); // 输出：24
```

加入缓存代理函数：

```javascript
var proxyMult = (function(){
    var cache = {};
    return function(){
        var args = Array.prototype.join.call(arguments,',');
        if(args in cache){
           return cache[args];
        }
        return cache[args] = mult.apply(this,arguments);
    }
})();

proxyMult(1,2,3,4); // 24
proxyMult(1,2,3,4); // 24
```

当我们第二次调用 proxyMult(1,2,3,4) 的时候，本体 mult 函数并没有被计算，proxyMult 直接返回了之前缓存好的计算结果。

通过增加缓存代理的方式，mult 函数可以继续专注于自身的职责——计算乘积，缓存的功能由代理对象来实现。

### ajax 异步请数据

我们常常在项目中遇到分页的需求，同一页的数据理论上只需要去后台拉取一次，这些已经拉取到的数据在某个地方被缓存之后，下次再请求同一页的时候，便可以直接使用之前的数据。

显然这里也是可以引入缓存代理，实现方式跟计算乘积差不多，唯一不同的是，请求数据是一个异步的操作，我们无法直接把计算结果放到代理对象的缓存中，而是要通过回调的方式。

## 用高阶函数动态创建代理

通过传入高阶函数这种更加灵活的方式，可以为各种计算方式创建缓存代理。现在这些计算方式被当做参数传入一个专门用于创建换粗代理的工厂中，这样一来，我们就可以称为乘法、加法、减法等创建缓存代理，代码如下：

```javascript
// 计算乘积
var mult = function(){
    console.log('开始计算乘积');
    var a = 1;
    for(var i=0,l = arguments.length;i<l;i++){
        a = a * arguments[i];
    }
    return a;
}
// 计算加和
var plus = function(){
    var a = 0;
    for(var i=0,l = arguments.length;i< l;i++){
        a = a + arguments[i];
    }
    return a;
}
// 创建缓存代理的工厂
var createProxyFactory = function(fn){
    var cache = {};
    return function(){
        var args = Array.prototype.join.call(arguments,',');
        if(args in cache){
           return cache[args];
        }
        return cache[args] = fn.apply(this.arguments);
    }
}

var proxyMult = createProxyFactory(mult);
var plusMult = createProxyFactory(plus);

proxyMult(1,2,3,4); // 24
proxyMult(1,2,3,4); // 24
plusMult(1,2,3,4); // 10
plusMult(1,2,3,4); // 10
```



## 其他代理模式

代理模式的变体种类非常多

- 防火墙代理：控制网络资源的访问，保护主题不被“坏人”接近
- 远程代理：为一个对象在不同的地址空间提供局部代表，在java 中，远程代理可以是另外一个虚拟机中的代理
- 保护代理：用于对象应该有不同访问权限的情况
- 智能应用代理：取代了简单的指针，它在访问对象时执行了一些附加操作，比如计算了一个对象被引用的次数。
- 写时复制代理：通常用于复制一个庞大对象的情况。写时复制代理延迟了复制的过程，当对象被真正修改的时候，才对它进行复制操作。写时复制代理是虚拟代理的一种变体，DLL （操作系统中的动态链接库）是其典型运用场景。

## 小结

代理模式包括很多小分类，JavaScript 开发中最常用的是虚拟代理和缓存代理。虽然代理模式非常有用，但我们编写某个业务逻辑代码的时候，往往不需要预先猜测是否需要使用代理模式。当真正发现不方便直接访问某个对象的时候，再写代理也不迟。

