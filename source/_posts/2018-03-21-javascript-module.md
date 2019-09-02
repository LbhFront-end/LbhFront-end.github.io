---
title: javascript模块化
date: 2018-03-21 14:12:23
tags: js
categories: 
	- javaScript相关
---

<div class="jquery-head">
       <center><h1>JavaScript模块化思想</h1></center>
</div> 

模块化
=============
### 模块化的基本思想  

（1）原始写法：  
模块就是实现特地功能的一组方法（将不同函数简单地放在一起，就是一个模块）  

```javascript
function m1(){
//...
}
function m2(){
//...
}
```

缺点：污染了全局变量，无法保证不与其他模块发送变量名的冲突，而且模块成员间看不出来直接关系  
（2）对象写法：  
将模块写成一个对象，将所有模块成员都放在对象里面  

```javascript
var module = new Object({
_count : 0,
    m1 : function(){
        //...
    }
    m2 : function(){
        //...
    }
});
```

优点：函数`m1()`与函数`m2()`都封装在module对象中，使用的时候就是调用对象中的属性
**module.m1();** **module._count = 5;**  
（3）立即执行函数写法  
IIFE 可以达到不暴露私有成员的目的  

```javascript
var module = (function(){
    var _count = 0;
    var m1 = function(){
        //...
    };
    var m2 = function(){
        //...
    };
    return {
    m1 : m1,
    m2 : m2,
    }
})();
```

优点：外部代码无法读取内部_count变量 console.log(module._count); //undefined  
（4）放大模式
如果模块很大，必须分成几个部分，或者是一个模块需要继承另外一个模块，就有必要使用放大模式（augmentation）  

```javascript
var module = (function (mod){
    mod.m3 = function(){
        //...
    };
    return mod;
})(module);  
```

上面的代码为module模块添加了一个新的方法m3，然后返回新的module模块。  
（5）宽放大模式（Loose augmentation）
在浏览器环境中，模块的各个部分通常都是从网上获取的，有时候无法知道哪个部分会先加载。采取4的写法，第一个执行部分有可能加载一个不存在空对象  

```javascript
var module = (function (mod){
    //...
    return mod;
})(window.module || {});
```

（6）输入全局变量  
独立性是模块的重要特点，模块内部最好不要与程序的其他部分交互，为了在模块内部调用全局变量，必须显式地将其他变量输入模块  

```javascript
var module = (function($,YAHOO){
    //...
})(jQuery, YAHOO);
```

上面的模块需要使用jQuery和YUI库，就要把这两个库（模块），当作是参数输入module中，这样做除了保证了模块的独立性，还使得模块之间的依赖关系变得明显。  

### AMD规范  

通行的javascript模块规范共有两种：**CommonJs**和**AMD**  
（1）CommonJS  
当node.js项目诞生后，将JavaScript语言用于服务器端编程，node.js的模块系统，就是参照CommonJS规范实现的。在CommonJS中有一个全局性方法`require()`,用于加载模块。假设有个数学模块math.js。就可以像下面那样加载  

```javascript
var math = require("math");

//调用模块提供的方法
math.add(2,3);
```

限制：CommonJS规范不适用浏览器环境，相同的代码，在浏览器中运行，必须等到math.js加载完成，如果加载的时候很长，那么整个应用就会停在那里等。这对服务端不是问题，因为所有的模块都是存放在本地硬盘或在那个，可以同步加载完成，等待的时间就是硬盘的读取时间。而在浏览器，因为模块都存放在服务端，等待时间取决于网速的快慢，可能要等很久的时间，浏览器处于假死的状态。因此浏览器端的模块不可以采用同步加载（synchronous）,只能采用异步加载（asynchronous）这就是AMD诞生的背景。  
（2）AMD  
全程“Asynchronous Module Definition”意思是异步模块定义。它采用异步方式加载模块，模块的加载不影响后面语句的运行。所有一栏这个模块的语句，都定义在一个回调函数中。等到加载完成后，这个回调函数才会执行。AMD也采用require()语句加载模块，不同于CommonJS的是它要求两个参数。  

```javascript
require([module],callback);
```

第一个参数[module]是一个数组，里面的成员就是要加载的模块；第二个参数callback则是加载完成之后的回调函数。还是使用CommonJS的代码，如下  

```javascript
require([‘math’]，function(math){
    math.add(2,3);
});
```

math.add()与math模块加载不是同步的，所以浏览器不会出现假死的状态。AMD比CommonJS更适合浏览器环境  

### require.js的使用  

（1）原始加载js方法  
加载多个js文件，加载的时候浏览器会停止网页渲染，加载的文件越多，页面失去响应的时间就会越长。其次由于js文件之间存在依赖关系。因此必须严格保证加载顺序，依赖性最大的模块一定要放在最后加载，当依赖关系很复杂的时候，代码的编写和维护都会变得困难。  
（2）require.js的加载  
优点：实现了js文件的异步加载，避免了网页失去响应，管理模块之间的依赖性，便于代码的编写和维护  
。使用步骤：  
1.下载require.js   
2.加载这个require.js，也可能造成网页失去响应。解决办法有两个，一个是把它放在网页底部加载，另一个是 `<script src="js/require.js" defer aysnc = 'true'></script> ` 
async属性表明这个文件需要异步加载，避免网页失去响应。IE不支持这个属性，只支持defer  
3.加载完require.js 后就要加载我们自己的代码，假定我们的代码文件是main.js,也放在js目录下。  
`<script src ="js/require.js" data-main="js/main"></script>  `
data-main属性的作用是，指定网页程序的主要模块。  
（3）主模块的写法  
主模块要依赖其他模块，使用AMD规范定义的`require()`函数。  

```javascript
//main.js
require(['moduleA','moduleB','moduleC'],function(moduleA, moduleB, moduleC){
    //...
});
```

`require()`函数接受两个参数。第一个参数是数组，表示所依赖的模块。上例就是['moduleA', 'moduleB', 'moduleC'] ，即主模块依赖这三个模块。第二个参数就是一个回调函数，当前面指定的模块都加载完成之后，它将被调用，加载的模块会以参数形式传入该函数，从而在回调函数的内部就可以使用
这些模块了。require()异步加载moduleA,moduleB,moduleC,浏览器就不会失去响应，它指定的回调函数，只有前面的
模块都加载成功后，才会运行，解决了依赖性的问题。  
实际例子：  

```javascript
require(['jquery','underscore','backbone'],function($,_,Backbone){
    //...
});
```

`require.js`会加载`jQuery`，`underscore`,`backbone` 然后再运行回调函数，主要模块的代码就写在回调函数中  
（4）模块的加载  
`require.config()`，我们可以通过这个方法，对模块的加载行为记性自定义。require.config()就写在主模块**main.js**的头部。参数是一个对象，这个对象的paths属性指定各个模块的加载路径。默认是在同一个目录（js子目录）  

```javascript
require.config({
    paths: {
        "jquery": "jquery.min",
        "underscore": "underscore.min",
        "backbone": "backbone.min",
    }
});
```

不在通过目录，例如在js/lib，有两种写法：  
1.逐一指定路径  

```javascript
require.config({
    paths: {
        "jquery": "lib/jquery.min",
        "underscore": "lib/underscore.min",
        "backbone": "lib/backbone.min",
    }
});
```

2.改变基目录  

```javascript
require.config({
    baseUrl: "js/lib",
    paths: {
        "jquery": "jquery.min",
        "underscore": "underscore.min",
        "backbone": "backbone.min",
    }
});
```

如果是在另一个主机上，也可以指定它的地址  

```javascript
require.config({
    paths: {
        "jquery": "https://ajax.googleapis.com/ajax/libs/jquery/1.7.2/jquery.min"
        
    }
});
```

require.js要求每个模块都是单一个js文件，这样的话，加载多个模块，就会发送多次http请求，也会影响到网页的加载速度。因而它提供了一个优化工具，当模块部署完毕后，可以使用这个工具将多个模块合并在一个文件中，减少http请求数。  
（5）AMD写法  
require.js加载的模块，采用AMD规范，那就是说模板也应该按照AMD的规范来写。  
具体的说，就是模块必须采用特定的`define()`函数来定义。如果一个模块不依赖其他模块，那么可以直接定义在difine（）函数之中。  
假定现在有个math.js文件，它定了一个math模块，那么math.js就要这样写：  

```javascript
//math.js
define(function(){
    var add = function(x,y){
        return x+y;
    };
    return {
        add : add
    };
});
```

加载方法如下：  

```javascript
//main.js
require(['math'],function(math){
    alert(math.add(1,1));
});
//如果这个模块还依赖其他模块，那么define（）函数的第一个参数，就必须是一个数组，指明该模块的依赖性
define(['myLib'],function(myLib){
    function foo(){
        myLib.doSomething();
    }
    return {
        foo : foo
    }
});
```

当require()函数加载上面的这个模块的时候，就会先加载myLib.js文件  
（6）加载非规范的模块  
在加载非规范的模块之前先用require.config()定义它们的一些特征，underscore与backbone这两个库为
例子。  

```javascript
require.config({
    shim: {
        'underscore':{
            export:'_'
        },
        'backbone':{
            deps:['underscore','jquery'],
            exports: 'Backbone'
        }
    },
});
```

`require.config()`接受一个配置对象，这个对象除了**paths**属性之外，还有一个**shim**属性，专门来配置不兼容的模块，具体来说，每个模块都要定义**exports**值（输出的变量名），来表明这个模块外部调用时的名称，**deps**数组来表明该模块的依赖性。  
jQuery的插件可以这样定义  

```javascript
shim{
    jquery.srcoll':{
        deps: ['jquery'],
        exports: 'jQuery.fn.scroll'
    }
}
```

（7）require.js插件  
domready插件，可以让回调函数在页面DOM结构加载完成后再运行  

```javasjavascriptcript
require(['domready!'],function(doc){
    //called once the Dom is ready
});
```

text和image插件，则是运行require.js加载文本和图片文件  

```javascript
define(['text!review.txt','image!cat.jpg'],function(review,cat){
    console.log(review);
    document.body.appendChild(cat);
});
```


[参考自阮一峰老师文章](http://www.ruanyifeng.com/blog/2012/11/require_js.html "点我")