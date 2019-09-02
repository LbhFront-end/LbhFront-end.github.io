---
title: 好玩的Nodejs —— 使用 Node.js进行 Web 开发（中）
date: 2018-09-27 15:31:54
tags: Nodejs
categories: 
	- Nodejs
---



不知不觉，已经过去快一个星期没有继续更新了，中秋假日总会使人想安于现状，这一章继续上面使用 Node.js 进行 Web 开发，介绍模板引擎，尝试建立一个微博网站，实现用户注册登录和发表微博的基础功能。好了，让我们一起开始吧

## 使用 Node.js 进行 Web 开发（中）

### 模板引擎

Express 的路由控制，是网络架构最核心的部分，即 MVC 架构中的控制器。而视图，主要是通过模板引擎的使用和集成来实现。视图决定了用户最终看到的东西，也是最重要的部分，这里以 ejs 为例介绍模板引擎的使用方法。

#### 什么是模板引擎

模板引擎（Template Engine） 是一个从页面模板根据一定的规则生成的 HTML 的工具。按照这种模式，整个网站就由一个个页面模块组成，所有的逻辑都嵌入在模块中，这种模式大大降低动态网页开发的门槛，但随着规模的扩大会遇到许多问题。

- 页面功能逻辑与页面布局样式耦合，网站模式变大以后逐渐难以维护
- 语法复杂，对于非技术的网页设计者来说门槛较高，难以学习。
- 功能过于全面，页面设计者可以在页面上编程，不利于功能划分，也使模板解析效率降低。

这些问题制约了早期模板引擎的发展，直到 MVC 开发模式普及，模板引擎才开始遍地开花。现代的模板引擎是 MVC 的一部分，在功能划分上它严格属于视图部分，因此功能以生存 HTML 页面为核心，不会引入过多的编程语言的功能。

模板引擎的功能主要是将页面模块和要显示的数据结合起来生成 HTML 页面。它既可以运行在服务器端又可以运行在客户端，大多数时候它都在服务器端直接被解析成 HTML ，解析完成后再传输给客户端，因为客户端甚至无法判断页面是否是模板引擎生成的。有时候模板引擎也可以而运行在客户端，即浏览器中，典型的代表就是 XSLT，它以 XML 为输入，在客户端生成 HTML 页面。但是由于浏览器兼容性问题，XSLT 并不是很流行。目前的主流还是由服务器运行模板引擎。

在MVC架构中，模板引擎包含在服务器端。控制器得到用户请求后，从模型获取数据，调用模板引擎。模板引擎以数据和页面模板输入，生成 HTML 页面，然后返回控制器，由控制器交给客户端。

![模板引擎在 MVC 架构中的位置](/images/2018-09-21-NodeJs-Part5-模板引擎在-MVC-架构中的位置.png)

#### 使用模板引擎

基于 JavaScript 的模板引擎有许多种实现。推荐使用 ejs （Embedded JavaScript），因为它十分简单而且与 Express 集成良好。由于它是标准 JavaScript 实现的，因此它不仅可以运行在服务器端，还可以运行在浏览器中。

app.js 中通过以下两个语句设置了模板引擎和页面模板的位置：

```javascript
// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');
```

表明要使用的模板引擎是 ejs ,页面模板在 views 子目录下。在 routes/index.js 用一下语句，绑定路由和调用模板引擎。

```javascript
var express = require('express');
var router = express.Router();

/* GET home page. */
router.get('/', function (req, res, next) {
  res.render('index', { title: 'Express' });
});
```

`res.render`  的功能是调用模板引擎，并将其产生的页面直接返回给客户端。它接受两个参数，第一个是模板的名称，即 `views` 目录下的模板文件名，不包含文件的扩展名；第二个参数是传递给模板的数据，用于模板翻译。index.ejs 内容如下：

```ejs
<h1><%= title %></h1>
<p>Welcome to <%= title %></p>
```

上面代码其中有两处 `<%= title %>` ，用于模板变量显示，它们在模板翻译时会被替换成 Express，因为  `res.render` 传递了  `{ title: 'Express' }` 。

ejs 的标签系统非常简单，它只有以下3种标签。

-  `<% code %>` ：JavaScript 代码。
-  `<%= code %>` ：显示替换过 HTML 特殊字符的内容。
-  `<%- code %>` ：显示原始 HTML 内容。

可以用它们实现页面模板系统能实现的任何内容。

#### 页面布局

views/index.ejs 里面的代码

```ejs
<!DOCTYPE html>
<html>
  <head>
    <title><%= title %></title>
    <link rel='stylesheet' href='/stylesheets/style.css' />
  </head>
  <body>
    <h1><%= title %></h1>
    <p>Welcome to <%= title %></p>
  </body>
</html>

```

Express 可以自动套用 layout.ejs ，所以可以将 index.ejs 里面的代码分开

```ejs
<!DOCTYPE html>
<html>
	<head>
		<title><%= title %></title>
		<link rel='stylesheet' href='/stylesheets/style.css' />
	</head>
	<body>
	<%- body %>
	</body>
</html>
```

`layout.ejs` 是一个页面布局模板，它描述了整个页面的框架结构，默认情况下每个单独的页面都继承自这个框架，替换掉  `<%- body %>`  部分。这个功能通常非常有用，因为一般为了保持整个网站的一致风格，HTML 页面的 `<head>` 部分以及页眉页脚中的大量内容是重复的，因此我们可以把它们放在 `layout.ejs` 中。当然，这个功能并不是强制的，如果想关闭它，可以在 app.js 的中  `app.configure`  中添加以下内容，这样页面布局功能就被关闭了。

```javascript
app.set('view options', {
	layout: false
});
```

另一种情况是，一个网站可能需要不止一种页面布局，例如网站分前台展示和后台管理系统，两者的页面结构有很大的区别，一套页面布局不能满足需求。这时我们可以在页面模板翻译时指定页面布局，即设置 layout  属性，例如：

```javascript
function(req, res) {
    res.render('userlist', {
        title: '用户列表后台管理系统',
        layout: 'admin'
    });
};
```

这段代码会在翻译 userlist  页面模板时套用 admin.ejs 作为页面布局。

#### 片段视图

Express 的视图系统还支持片段视图（partials），它就是一个页面的片段，通常是重复的内容，用于迭代显示。它就是一个页面的片段，通常是重复的内容，用于迭代显示。通过它你可以将相对独立的页面块分割出去，而且可以避免显式地使用  for  循环。而其实在 Express4.0以后，已经开始用 `include` 来取代 `partials ` 了。

如果仍要用的话，可以采用以下步骤：

1. 运行cmd 输入:npm install express-partials -g  
2. 下载成功后.在app.js 中引用此插件   var partials = require(‘express-partials’);
3. 开启此插件, 在app.js 中 app.set(‘view engine’, ‘ejs’);  代码后添加如下代码:  app.use(partials());

不过这里，我还是用新的方法，首先在 routers/index.js 中新增以下内容。

```javascript
router.get('/list', function (req, res, next) {
  res.render('list', {
    title: 'List',
    items: [1995, 'lbh', 'express', 'Node.js']
  });
});
```

接着在 views目录下新建 list.ejs 内容是

```ejs
<ul>
    <% items.forEach(function(listitem){%>
    <% include listitem%>
    <%}) %>
</ul>
```

同时建立 listitem.ejs 内容是

```ejs
<li><%= listitem %></li>
```

访问 http://localhost:3000/list，可以在源代码中看到以下内容：

```html
<html>
    <head></head>
    <body>
        <ul>    
            <li>1995</li>

            <li>lbh</li>

            <li>express</li>

            <li>Node.js</li>    
		</ul>
    </body>
</html>
```

#### 视图助手

Express 提供了一种叫做视图助手的工具，它的功能是允许在视图中访问一个全局的函数或对象，不用每次调用视图解析的时候单独传入。前面提到的 partial 就是一个视图助手。

视图助手有两类，分别是静态视图助手和动态视图助手。这两者的差别在于，静态视图助手可以是任何类型的对象，包括接受任意参数的函数，但访问到的对象必须是与用户请求无关的，一般指的是项目的名称，地址等配置参数或者是公共的方法，这些变量，方法只能用在模板视图里面。动态视图助手指的是该视图变量，方法与请求有关，一般用来解析请求信息，如用户登录信息，请求地址等。

静态视图助手可以通过  app.helpers()  函数注册，它接受一个对象，对象的每个属性名称为视图助手的名称，属性值对应视图助手的值。动态视图助手则通过  app.dynamicHelpers() 注册，方法与静态视图助手相同，但每个属性的值必须为一个函数，该函数提供  req 和  res 。当然这是express4.0之前的用法，现在视图助手有所不一样了。

##### 静态视图

在 app.js 中添加

```javascript
// app 静态视图助手
// 静态视图助手变量
app.locals.appName = 'NodeExpressBlog';
app.locals.sayHello = function(){
  return 'Welcome to my NodeExpressBlog';
}
```

新建一个 helper.ejs,内容如下：

```ejs
<h1><%= appName%></h1>
<p><%= sayHello()%></p>
```

访问 http://localhost:3000/helper 可以看到下面内容

![express静态视图](/images/2018-09-21-NodeJs-Part5-express静态视图.png)

##### 动态视图

动态视图助手的实现方式和路由的方式相似，所以动态视图助手要将语句放在路由的前面

在 routers/index.js 里面添加以下内容

```javascript
// 动态视图助手
router.use(function(req, res, next){
  res.locals.appUrl = req.url;
  res.locals.Welcome = function(){
    return 'Welcome to my NodeExpressBlog, the url is: ' + res.locals.appUrl;
  }
  next();
});
```

然后在 helper.ejs 添加以下内容

```ejs
<p><%= appUrl %></p>
<p><%= Welcome() %></p>
```

刷新网页，可以看到以下内容

![express动态视图](/images/2018-09-21-NodeJs-Part5-express动态视图.png)

视图助手的本质其实就是给所有视图注册了全局变量，因此无需每次在调用模板引擎时传递数据对象。

接下来 在 app.js 中加入 

```javascript
var util = require('util');
app.locals.inspect = function(obj){
  return util.inspect(obj, true);
}
```

然后在 routers/index.js 添加 

```javascript
// 动态视图助手
router.use(function(req, res, next){
  res.locals.appUrl = req.url;
  res.locals.Welcome = function(){
    return 'Welcome to my NodeExpressBlog, the url is: ' + res.locals.appUrl;
  }
  res.locals.headers = req.headers;
  next();
});
```

同样在 helper.ejs 添加

```ejs
<p><%=inspect(headers)%></p>
```

重开服务器，可以看到



![视图助手](/images/2018-09-21-NodeJs-Part5-视图助手.png)

在后面使用  session 时会发现它是非常有用的。



### 建立微博网站

#### 功能分析

首先，微博应该以用户为中心，因
此需要有用户的注册和登录功能。微博网站最核心的功能是信息的发表，这个功能涉及许多方面，包括数据库访问、前端显示等。一个完整的微博系统应该支持信息的评论、转发、圈点用户等功能，但出于演示目的，我们不能一一实现所有功能，只是实现一个微博社交网站的雏形。

#### 路由规划

在完成功能设计以后，下一个要做的事情就是路由规划了。路由规划，或者说控制器规划是整个网站的骨架部分，因为它处于整个架构的枢纽位置，相当于各个接口之间的粘合剂，所以应该优先考虑。

根据功能设计，我们把路由按照以下方案规划。

-   `/`：首页
-   `/u/[user]`：用户的主页 
-   `/post`：发表信息
-   `/reg`：用户注册
-   `/login`：用户登录
-   `/logout`：用户登出

以上页面还可以根据用户状态细分。发表信息以及用户登出页面必须是已登录用户才能操作的功能，而用户注册和用户登入所面向的对象必须是未登入的用户。首页和用户主页则针对已登入和未登入的用户显示不同的内容。

修改 routers/index.js 的内容

```javascript
// 正式微博路由
router.get('/', function (req, res, next) {
  res.render('index', { title: 'Express' });
});
// 用户的主页 
router.get('/u/:user', function (req, res, next) {
  
});
// 用户注册
router.route('/reg').
  all(function (req, res, next) {
    next()
  }).get(function (req, res, next) {
    next()
  }).post(function (req, res, next) {
    next()
  });
// 用户登录
router.route('/login').
  all(function (req, res, next) {
    next()
  }).get(function (req, res, next) {
    next()
  }).post(function (req, res, next) {
    next()
  });
// 用户登出 
router.get('/logout', function (req, res, next) {
  
});  
```

由于`/reg` 以及 `/login` 接受表单信息的同时还要显示用户注册要填写的表单，所以使用 `router.route`方法链式将 post/get 方法写在了一起。

#### 界面设计

Twitter Bootstrap 是由 Twitter 的设计师和工程师发起的开源项目，它提供了一套与 Twitter 风格一致的简洁、优雅的 Web UI，包含了完全由 HTML、CSS、JavaScript 实现的用户交互工具。可以轻松地使用Twitter Bootstrap 制作出优美的界面。

#### 使用 Bootstrap

从http://twitter.github.com/bootstrap/下载bootstrap.zip，解压后可以看到以下文件：

```shell
css/bootstrap-responsive.css
css/bootstrap-responsive.min.css
css/bootstrap.css
css/bootstrap.min.css
img/glyphicons-halflings-white.png
img/glyphicons-halflings.png
js/bootstrap.js
js/bootstrap.min.js
```

其中所有的 JavaScript 和 CSS 文件都提供了开发版和产品版，前者是原始的代码，后者经过压缩，文件名中带有 min。将 img 目录复制到工程 public 目录下，将 bootstrap.css、bootstrap-responsive.css 复制到 public/stylesheets 中，将 bootstrap.js 复制到 public/javascripts 目录中，然后从http://jquery.com/下载一份最新版的 jquery.js 也放入 public/javascripts 目录中。

由于 express4.x版本以及不再支持 layout.ejs，而是使用 `include`

首先我们本来要插入layout.ejs，内容如下：

```html
<!DOCTYPE html>
<html>

<head>
  <title>
    <%= title %> - Microblog</title>
  <link rel='stylesheet' href='/stylesheets/bootstrap.css' />
  <link rel='stylesheet' href='/stylesheets/style.css' />
  <style type="text/css">
    body {
      padding-top: 60px;
      padding-bottom: 40px;
    }
  </style>
  <link href="stylesheets/bootstrap-responsive.css" rel="stylesheet">
</head>

<body>
  <div class="navbar navbar-fixed-top">
    <div class="navbar-inner">
      <div class="container">
        <a class="btn btn-navbar" data-toggle="collapse" data-target=".nav-collapse">
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
        </a>
        <a class="brand" href="/">Microblog</a>
        <div class="nav-collapse">
          <ul class="nav">
            <li class="active"><a href="/">首页</a></li>
            <li><a href="/login">登入</a></li>
            <li><a href="/reg">注册</a></li>
          </ul>
        </div>
      </div>
    </div>
  </div>
  <div id="container" class="container"></div>
  <hr />
<footer>
    <p><a href="http://laibh.top" target="_blank">赖同学</a> 2018</p>
</footer>
</div>
</body>
<script src="/javascripts/jquery.js"></script>
<script src="/javascripts/bootstrap.js"></script>
</body>

</html>
```

上面代码是使用 Bootstrap部件实现的一个简单页面框架，整个页面分为顶部工具栏、正文和页脚三部分，其中正文和页脚包含在名为  container 的  div 标签中。

然后我们新建两个ejs 文件，分别是 footer.ejs 和 header.ejs 

```ejs
// header.ejs 
<!DOCTYPE html>
<html>

<head>
  <title>
    <%= title %> - Microblog</title>
  <link rel='stylesheet' href='/stylesheets/bootstrap.css' />
  <link rel='stylesheet' href='/stylesheets/style.css' />
  <style type="text/css">
    body {
      padding-top: 60px;
      padding-bottom: 40px;
    }
  </style>
  <link href="stylesheets/bootstrap-responsive.css" rel="stylesheet">
</head>

<body>
  <div class="navbar navbar-fixed-top">
    <div class="navbar-inner">
      <div class="container">
        <a class="btn btn-navbar" data-toggle="collapse" data-target=".nav-collapse">
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
        </a>
        <a class="brand" href="/">Microblog</a>
        <div class="nav-collapse">
          <ul class="nav">
            <li class="active"><a href="/">首页</a></li>
            <li><a href="/login">登入</a></li>
            <li><a href="/reg">注册</a></li>
          </ul>
        </div>
      </div>
    </div>
  </div>
  <div id="container" class="container">
```

```ejs
// footer.ejs
</div>
<hr />
<footer>
    <p><a href="http://laibh.top" target="_blank">赖同学</a> 2018</p>
</footer>
</div>
</body>
<script src="/javascripts/jquery.js"></script>
<script src="/javascripts/bootstrap.js"></script>
</body>

</html>
```

然后，在 index.ejs 添加以下内容

```ejs
<% include header.ejs %>
<div class="hero-unit">
  <h1>欢迎来到 Microblog</h1>
  <p>Microblog 是一个基于 Node.js 的微博系统。</p>
  <p>
    <a class="btn btn-primary btn-large" href="/login">登录</a>
    <a class="btn btn-large" href="/reg">立即注册</a>
  </p>
</div>
<div class="row">
  <div class="span4">
    <h2>Carbo 说</h2>
    <p>东风破早梅 向暖一枝开 冰雪无人见 春从天上来</p>
  </div>
  <div class="span4">
    <h2>BYVoid 说</h2>
    <p>
      Open Chinese Convert（OpenCC）是一个开源的中文简繁转换项目，
      致力于制作高质量的基于统计预料的简繁转换词库。
      还提供函数库(libopencc)、命令行简繁转换工具、人工校对工具、词典生成程序、
      在线转换服务及图形用户界面。</p>
  </div>
  <div class="span4">
    <h2>佛振 说</h2>
    <p>中州韵输入法引擎 / Rime Input Method Engine 取意历史上通行的中州韵，
      愿写就一部汇集音韵学智慧的输入法经典之作。
      项目网站设在 http://laibh.top
      创造应用价值是一方面，更要坚持对好技术的追求，希望能写出灵动而易于扩展的代码，
      使其成为一款个性十足的开源输入法。</p>
  </div>
</div>
<% include footer.ejs %>
```

访问 http://localhost:3000/ ，就可以看到以下内容

![博客首页](/images/2018-09-21-NodeJs-Part5-博客首页.png)

不知不觉，又篇幅有点长了，后面的是最后一章，将会介绍用户注册和登录已经发表微博的基本功能，希望可以在下一章一起学习。

