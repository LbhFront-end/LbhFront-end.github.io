---
title: 好玩的Nodejs —— Node.js进阶话题
date: 2018-10-13  09:54:54
tags: Nodejs
categories: 
	- Nodejs
---



## Node.js进阶话题

这本书的最后一章，主要讲解的内容为以下几点

- 模块加载机制
- 异步编程模式下的控制流
- Node.js 应用部署
- Node.js 的一些劣势



### 模块加载机制

#### 模块的类型

Node.js 的模块可以分为两大类，一类是核心模块，另一类是文件模块。核心模块就是 Node.js 标准 API 中提供的模块，如 fs、http、net、vm等，这些都是 Node.js 官方提供的模块，编译成了二进制代码。我们可以直接通过 require 获取核心模块。核心模块拥有最高的加载优先级，如果有模块与其命名冲突，Node.js 总是会先加载核心模块。

文件模块则是存储为单独的文件（或文件夹）的模块，可能是 JavaScript 代码、JSON 或编译好的 C/C++ 代码。文件模块的加载方法相对复杂，但十分灵活，尤其是和 npm 结合使用时，在不显式指定文件模块扩展名的时候，Node.js 会分别试图加上 .js、.json 和 .node 扩展名。.js 是 JavaScript 代码，.json 是 JSON 格式的文本，.node 是编译好的 C/C++ 代码。

下表总结了 Node.js 模块的类型，从上到下加载优先级由高到低。

| 核心模块 |            | 内建  |
| -------- | ---------- | ----- |
| 文件     | JavaScript | .js   |
|          | JSON       | .json |
|          | C/C++ 扩展 | .node |

#### 按路径加载模块

文件模块的加载有两种方式，一种是按路径加载，一种是查找 node_modules 文件夹。如果 require 参数以 “/” 开头，那么就绝对路径的方式查找路径模块名称。如果 require ('/home/lbh/module') 将会按照优先级依次尝试加载  /home/lbh/module.js、/home/lbh/module.json 和 /home/lbh/module.node。

如果 require  参数以“ ./ ”或“ ../ ”开头，那么则以相对路径的方式来查找模块，这种方式在应用中是最常见的。例如前面的例子中我们用了 require('./hello') 来加载同一文件夹下的hello.js。

#### 通过查找 node_modules 目录加载模块

如果 require 参数不以“ / ”、“ ./ ”或“ ../ ”开头，而该模块又不是核心模块，那么就要通过查找 node_modules 加载模块了。我们使用npm获取的包通常就是以这种方式加载的。

在某个目录下执行命令 npm install express， 你会发现出现了一个叫做node_modules的目录

在 node_modules 目录的外面一层，我们可以直接使用  require('express')  来代替require('./node_modules/express') 。这是Node.js模块加载的一个重要特性：通过查找 node_modules 目录来加载模块。

当  require 遇到一个既不是核心模块，又不是以路径形式表示的模块名称时，会试图在当前目录下的 node_modules 目录中来查找是不是有这样一个模块。如果没有找到，则会在当前目录的上一层中的 node_modules 目录中继续查找，反复执行这一过程，直到遇到根目录为止.

举个例子，我们要在 /home/lbh/develop/foo.js 中使用 require('bar.js')  命令，Node.js会依次查找：

- /home/lbh/develop/node_modules/bar.js
- /home/lbh/node_modules/bar.js
- /home/node_modules/bar.js
- /node_modules/bar.js

为什么要这样做呢？因为通常一个工程内会有一些子目录，当子目录内的文件需要访问到工程共同依赖的模块时，就需要向父目录上溯了

比如说工程的目录结构如下：

```
|- project
    |- app.js
    |- models
    	|- ...
	|- views
        |- ...
	|- controllers
        |- index_controller.js
        |- error_controller.js
        |- ...
    |- node_modules
        |- express
```

我们不仅要在 project 目录下的 app.js 中使用  require('express') ，而且可能要在controllers 子目录下的 index_controller.js 中也使用 require

#### 加载缓存

Node.js 模块不会被重复加载，这是因为 Node.js 通过文件名缓存所有加载过的文件模块，所以以后再访问到时就不会重新加载了。注意，Node.js 是根据实际文件名缓存的，而不是 require() 提供的参数缓存的，也就是说即使你分别通过require('express') 和 require('./node_modules/express')  加载两次，也不会重复加载，因为尽管两次参数不同，解析到的文件却是同一个。

#### 加载顺序

总结一下使用  require(some_module) 时的加载顺序。

(1) 如果 some_module 是一个核心模块，直接加载，结束。
(2) 如果 some_module 以“ / ”、“ ./ ”或“ ../ ”开头，按路径加载  some_module ，结束。
(3) 假设当前目录为 current_dir，按路径加载 current_dir/node_modules/some_module。 

- 如果加载成功，结束。
- 如果加载失败，令current_dir为其父目录。
-  重复这一过程，直到遇到根目录，抛出异常，结束。



### 控制流

基于异步 I/O 的事件式编程容易将程序的逻辑拆得七零八落，给控制流的疏理制造障碍。让我们通过下面的例子来说明这个问题。

#### 循环的陷阱

Node.js 的异步机制由事件和回调函数实现，一开始接触可能会感觉违反常规，但习惯以后就会发现还是很简单的。然而这之中其实暗藏了不少陷阱，一个很容易遇到的问题就是循环中的回调函数，初学者经常容易陷入这个圈套。让我们从一个例子开始说明这个问题。

```javascript
//forloop.js

var fs = require('fs');
var files = ['a.txt','b.txt','c.txt'];
for(var i = 0; i < files.length; i++){
	fs.readFile(files[i],'utf-8',function(err,contents){
		console.log(files[i]+ ':' + contents);
	});
}
```

这段代码的功能很直观，就是依次读取文件 a.txt、b.txt、c.txt，并输出文件名和内容。假设这三个文件的内容分别是 AAA、BBB 和 CCC，那么我们期望的输出结果就是：

```
a.txt: AAA
b.txt: BBB
c.txt: CCC
```

而实际上运行代码后，得到的是

```
undefined: AAA
undefined: BBB
undefined: CCC
```

在读取文件的回调函数中分别输出 files 、 i  和  files[i] 。

可以发现三次输出的i 都是3，超出了 files 数组的下标，因为得到的 files[i] 就都是 undefined 。说明了 fs.readFile 的回调函数中访问到的 i 值都是循环退出以后的，因此不能分辨，可以通过闭包来解决这个问题。代码如下：

```javascript
var fs = require('fs');
var files = ['a.txt','b.txt','c.txt'];
for(var i = 0; i < files.length; i++){
	(function(i){
		fs.readFile(files[i],'utf-8',function(err,contents){
			console.log(files[i]+ ':' + contents);
		});
	})(i)
}
```

上面代码在  for  循环体中建立了一个匿名函数，将循环迭代变量  i  作为函数的参数传递并调用。由于运行时闭包的存在，该匿名函数中定义的变量（包括参数表）在它内部的函数（  fs.readFile  的回调函数）执行完毕之前都不会释放，因此我们在其中访问到的 i  就分别是不同的闭包实例，这个实例是在循环体执行的过程中创建的，保留了不同的值。
事实上以上这种写法并不常见，因为它降低了程序的可读性，故不推荐使用。大多数情况下我们可以用数组的  `forEach` 方法解决这个问题：

```javascript
//callbackforeach.js

// callbackforeach.js

var fs = require('fs');
var files = ['a.txt','b.txt','c.txt'];

files.forEach(function(filename){
	fs.readFile(filename,'utf-8',function(err,contents){
		console.log(filename + ':' + contents );
	});
});
```

#### 解决控制流难题

除了循环的陷阱，Node.js 异步式编程还有一个显著的问题，即深层的回调函数嵌套。在这种情况下，我们很难像看基本控制流结构一样一眼看清回调函数之间的关系，因此当程序规模扩大时必须采取手段降低耦合度，以实现更加优美、可读的代码。这个问题本身没有立竿见影的解决方法，只能通过改变设计模式，时刻注意降低逻辑之间的耦合关系来解决。

除此之外，还有许多项目试图解决这一难题。`async` 是一个控制流解耦模块，它提供了`async.series` 、 `async.parallel` 、 `async.waterfall`  等函数，在实现复杂的逻辑时使用这些函数代替回调函数嵌套可以让程序变得更清晰可读且易于维护，但你必须遵循它的编程风格。

streamlinejs和jscex则采用了更高级的手段，它的思想是“变同步为异步”，实现了一个JavaScript 到JavaScript 的编译器，使用户可以用同步编程的模式写代码，编译后执行时却是异步的。
eventproxy 的思路与前面两者区别更大，它实现了对事件发射器的深度封装，采用一种完全基于事件松散耦合的方式来实现控制流的梳理。
无论是以上哪种解决手段，都不是“非侵入性的”，也就是说它对你编程模式的影响是非常大的，你几乎不可能无代价地在使用了一种模式很久以后从容地换成另一种模式，或者直接糅合使用两种模式。而且它们都是在解决了深层嵌套的回调函数可读性问题的同时，引入了其他复杂的语法，带来了另一种可读性的降低。所以，是否使用，使用哪种方案，在决定之前是需要仔细斟酌研究的。

### Node.js 不适合做什么

#### 计算密集型的程序

理想情况下，Node.js单线程在执行的过程中会将一个CPU核心完全占满，所有的请求必须等待当前请求处理完毕以后进入事件循环才能响应。如果一个应用是计算密集型的，那么除非你手动将它拆散，否则请求响应延迟将会相当大。

例如，某个事件的回调函数中要进行复杂的计算，占用CPU 200毫秒，那么事件循环中所有的请求都要等待200毫秒。为了提高响应速度，你唯一的办法就是把这个计算密集的部分拆成若干个逻辑，这给编程带来了额外的复杂性。即使这样，系统的总吞吐量和总响应延迟也不会降低，只是调度稍微公平了一些。

#### 单用户多任务型应用

前面我们讨论的通常都是服务器端编程，其中一个假设就是用户数量很多。但如果面对的是单用户，譬如本地的命令行工具或者图形界面，那么所谓的大量并发请求就不存在了。于是另一个恐怖的问题出现了，尽管是单用户，却不一定是单任务。例如给用户提供界面的同时后台在进行某个计算，为了让用户界面不出现阻塞状态，你不得不开启多线程或多进程。而Node.js 线程或进程之间的通信到目前为止还很不便，因为它根本没有锁，因而号称不会死锁。Node.js 的多进程往往是在执行同一任务，通过多进程利用多处理器的资源，但遇到多进程相互协作时，就显得捉襟见肘了。

#### 逻辑十分复杂的事务

Node.js 的控制流不是线性的，它被一个个事件拆散，但人的思维却是线性的，当你试图转换思维来迎合语言或编译器时，就不得不作出牺牲。举例来说，你要实现一个这样的逻辑：从银行取钱，拿钱去购买某个虚拟商品，买完以后加入库存数据库，这中间的任何一步都可能会涉及数十次的I/O操作，而且任何一次操作失败以后都要进行回滚操作。这个过程是线性的，已经很复杂了，如果要拆分为非线性的逻辑，那么其复杂程度很可能就达到无法维护的地步了。
Node.js更善于处理那些逻辑简单但访问频繁的任务，而不适合完成逻辑十分复杂的工作。



到这里 关于 nodejs 开发指南这本书就算阅读完了，通过这本书，大概地了解了NodeJS的特性，通过node+express+mongodb 搭建了一个微博的雏形实例，希望后面有机会可以完善它。这本书附录还有JavaScript的一些特性以及Node.js 编程的规范，如果有价值再稍后总结学习。