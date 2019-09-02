---
title: 好玩的Nodejs —— 简介
date: 2018-09-12 14:30:54
tags: Nodejs
categories: 
	- Nodejs
---



心心念念要学习的nodejs从今天开始，读 《node.js开发指南》，进行学习记录总结。

Node.js，或者 Node，是一个可以让 JavaScript 运行在服务器端的平台。它可以让JavaScript 脱离浏览器的束缚运行在一般的服务器环境下，就像运行 Python、Perl、PHP、Ruby程序一样。你可以用 Node.js 轻松地进行服务器端应用开发，Python、Perl、PHP、Ruby 能做的事情 Node.js 几乎都能做，而且可以做得更好。

Node.js 是一个为 实时 Web（Real-time Web）应用开发而诞生的平台，它从诞生之初就充分考虑了在实时响应、超大规模数据要求下架构的可扩展性。这使得它摒弃了传统平台依靠多线程来实现高并发的设计思路，而采用了单线程、异步式I/O、事件驱动式的程序设计模型。这些特性不仅带来了巨大的性能提升，还减少了多线程程序设计的复杂性，进而提高了开发效率。

尽管它诞生的时间（2009年）还不长，但它的周围已经形成了一个庞大的生态系统。Node.js 有着强大而灵活的 包管理器 （node package manager，npm），目前已经有上万个第三方模块，其中有网站开发框架，有 MySQL、PostgreSQL、MongoDB 数据库接口，有模板语言解析、CSS 生成工具、邮件、加密、图形、调试支持，甚至还有图形用户界面和操作系统 API工具。

## Node.js简介

### Node.js 是什么

Node.js 不是一门独立的语言，也不是一个 JavaScript 框架，也不是一个浏览器端的库。Node.js 是一个让 JavaScript 运行在服务器端的开发平台，它让 JavaScript 成为脚本语言的一等公民，在服务端堪与 PHP、Python、Perl、Ruby 平起平坐。

Node.js 是一个划时代的技术，它在原有的 Web 前端和后端技术的基础上总结并提炼出了许多新的概念和方法，堪称是十多年来 Web 开发经验的集大成者。Node.js 可以作为服务器向用户提供服务，与 PHP、Python、Ruby on Rails 相比，它跳过了 Apache、Nginx 等 HTTP服务器，直接面向前端开发。Node.js 的许多设计理念与经典架构（如 LAMP）有着很大的不同，可提供强大的伸缩能力，以适应21世纪10年代以后规模越来越庞大的互联网环境。

#### Node.js 与 JavaScript

传统意义上，JavaScript 是由 ECMAScript、文档对象模型（DOM）和浏览器对象模型（BOM）组成的，而 Mozilla 则指出 JavaScript 由Core JavaScript 和 Client JavaScript 组成。

Node.js 中所谓的 JavaScript 只是 Core JavaScript，或者说是 ECMAScript 的一个实现，不包含 DOM、BOM 或者 Client JavaScript。这是因为 Node.js 不运行在浏览器中，所以不需要使用浏览器中的许多特性。

Node.js 是一个让 JavaScript 运行在浏览器之外的平台。它实现了诸如文件系统、模块、包、操作系统 API、网络通信等 Core JavaScript 没有或者不完善的功能。

随着 Node.js 的成功，各种浏览器外的 JavaScript 实现逐步兴起，因此产生了 CommonJS 规范。CommonJS 试图拟定一套完整的 JavaScript 规范，以弥补普通应用程序所需的 API，譬如文件系统访问、命令行、模块管理、函数库集成等功能。CommonJS 制定者希望众多服务端 JavaScript 实现遵循CommonJS 规范，以便相互兼容和代码复用。Node.js 的部份实现遵循了CommonJS规范，但由于两者还都处于诞生之初的快速变化期，也会有不一致的地方。

Node.js 的 JavaScript 引擎是 号称是目前世界上最快——的 JavaScript 引擎V8。



### Node.js 能做什么

使用 Node.js 可以开发：

- 具有复杂逻辑的网站
- 基于社交网络的大规模 Web 应用
- Web Socket 服务器
- TCP/UDP 套接字应用程序
- 命令行工具
- 交互式终端程序
- 带有图形用户界面的本地应用程序
- 单元测试工具
- 客户端 JavaScript 编译器

Node.js 内建了 HTTP 服务器支持，也就是说你可以轻而易举地实现一个网站和服务器的组合。Node.js 还可以部署到非网络应用的环境下，比如一个命令行工具。Node.js 还可以调用C/C++ 的代码，这样可以充分利用已有的诸多函数库，也可以将对性能要求非常高的部分用C/C++ 来实现。



### 异步式 I/O 与事件驱动

Node.js 最大的特点就是采用异步式 I/O 与事件驱动的架构设计。对于高并发的解决方案，传统的架构是多线程模型，也就是为每个业务逻辑提供一个系统线程，通过系统线程切换来弥补同步式 I/O 调用时的时间开销。Node.js 使用的是单线程模型，对于所有 I/O 都采用异步请求方式，避免了频繁的上下文切换。Node.js 在执行的过程中会维护一个时间队列，程序 在执行时进入时间循环等待下一个事件到来，每个异步式请求完成后会被推送到事件队列，等待程序进程进行处理。

例如对于简单而常见的数据库查询操作，按照传统方式实现的代码如下：

```javascript
res = db.query('SELECT * from some_table')
res.output();
```

以上代码在执行到第一行的时候，线程会堵塞，等待数据库返回查询结果，然后再继续处理。然而，由于数据库查询可能涉及磁盘读写和网络通信，其延时可能相当大（长达几个到几百毫秒，相比CPU的时钟差了好几个数量级），线程会在这里阻塞等待结果返回，对于高并发的访问，一方面线程长期阻塞等待，另一方面为了应付新请求而不断新增加线程，因此会浪费大量系统资源，同时线程的增多也会占用大量的 CPU 时间来处理内存上下文切换，而且还容易遭受低速连接攻击。

看看 Nodejs 是如何解决这个问题的。

```javascript
db.query('SELECT * from some_table',fucntion(res){
	res.output();         
})
```

这段代码中，db.query 的第二个参数是一个回调函数。进程在执行到 db.query 的时候，不会有结果返回，而是直接继续执行后面的语句，知道进入时间循环。当数据库查询结果返回时，会将事件发送到事件队列，等到线程进入事件循环以后，才会调用之前的回调函数继续执行后面的逻辑。

Node.js 的异步机制是基于时间，所有的磁盘I/O、网络通信、数据库查询都以非阻塞的方式请求，返回的结果由事件循环来处理。

![事件循环](/images/2018-09-13-NodeJs-Part1-事件循环.png)

这样的好处是，CPU 和内存在同一时间集中处理一件事，同时尽可能让耗时的I/O 操作并行执行。对于低速连接攻击，Node.js 只是在事件队列中增加请求，等待操作系统的回应，因此不会有任何多线程开销，很大程度上可以提升 Web 应用的健壮性，防止恶意攻击。

这种异步事件模式的弊端也是显而易见的，因为它不符合开发者的常规线性思路，往往需要把一个完整的逻辑拆分成一个个事件，增加了开发和调试的难度。针对这个问题，Node.js第三方模块提出了很多解决方案，会在后面第六部分详细讨论。



### Node.js 的性能

Node.js 除了使用 V8 作为JavaScript引擎以外，还使用了高效的 libev 和 libeio 库支持事件驱动和异步式 I/O。



### CommonJS

正如当年为了统一 JavaScript 语言标准，人们制定了 ECMAScript 规范一样，如今为了统一 JavaScript 在浏览器之外的实现，CommonJS 诞生了。CommonJS 试图定义一套普通应用程序使用的 API，从而填补 JavaScript 标准库过于简单的不足。CommonJS 的最终目标是制定一个像 C++ 标准库一样的规范，使得基于 CommonJS API 的应用程序可以在不同的环境下运行，就像用 C++ 编写的应用程序可以使用不同的编译器和运行时函数库一样。为了保持中立，CommonJS 不参与标准库实现，其实现交给像 Node.js 之类的项目来完成。

CommonJS 规范包括了模块（modules）、包（packages）、系统（system）、二进制（binary）、控制台（console）、编码（encodings）、文件系统（filesystems）、套接字（sockets）、单元测试（unit testing）等部分