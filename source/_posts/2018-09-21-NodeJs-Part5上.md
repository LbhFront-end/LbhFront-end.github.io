---
title: 好玩的Nodejs —— 使用 Node.js进行 Web 开发（上）
date: 2018-09-21 15:31:54
tags: Nodejs
categories: 
	- Nodejs
---



本章从零开始用 Node.js 实现一个微博系统，功能包括路由控制、页面模板、数据库访问、用户注册、登录、用户会话等内容。
介绍 Express 框架、MVC 设计模式、ejs 模板引擎以及 MongoDB 数据库的操作。
由于篇幅看起来过长，就打算分成几节来记录，具体几节看后面情况，好了，让我们一起来实践用Node.js进行Web开发吧

## 使用 Node.js进行 Web 开发

### 准备工作

MVC（Model-View-Controller，模型-视图-控制器）是一种软件的设计模式，即把一个复杂的软件工程分解成三个层面：模型、视图和控制器。

- 模型是对象及其数据结构的实现，通常包含数据库操作
- 视图表示用户界面，在网站中通常就是 HTML 的组织结构
- 控制器用于处理用户请求和数据流、复杂模型，将输出传递给视图

称 PHP、ASP、JSP 为 “模板为中心的架构”，下表是两种 Web 开发架构的对比

| 特性         | 模板为中心架构                   | MVC架构                          |
| ------------ | -------------------------------- | -------------------------------- |
| 页面产生方式 | 执行并替换标签中的语句           | 由模板引擎生成 HTML 页面         |
| 路径解析     | 对应到文件系统                   | 由控制器定义                     |
| 数据访问     | 通过 SQL 语句查询或访问文件系统  | 对象关系模型                     |
| 架构中心     | 脚本语言是静态 HTTP 服务器的扩展 | 静态 HTTP 服务器是脚本语言的补充 |
| 适用范围     | 小规模网站                       | 大规模网站                       |
| 学习难度     | 容易                             | 较难                             |

这两种架构都出自原始的 CGI，但不同之处是前者走了一条粗放扩张的发展路线，由于易学易用，在几年前应用较广，而随着互联网规模的扩大，后者优势逐渐体现，目前已经成为主流。

Node.js 本质上和 Perl 或 C++ 一样，都可以作为 CGI 扩展被调用，但它还可以跳过 HTTP 服务器，因为它本身就是。传统的架构中 HTTP 服务器的角色会由 Apache、Nginx、IIS 之类的软件来担任，而 Node.js 不需要。Node.js 提供了 http  模块，它是由 C++ 实现的，性能可靠，可以直接应用到生产环境。

Node.js 和其他的语言相比的另一个显著区别，在于它的原始封装程度较低。例如 PHP 中可以访问  $_REQUEST  获取客户端的 POST 或 GET 请求，通常不需要直接处理 HTTP 协议。这些语言要求由 HTTP 服务器来调用，因此需要设置一个 HTTP 服务器来处理客户端的请求，HTTP 服务器通过 CGI 或其他方式调用脚本语言解释器，将运行的结果传递回HTTP 服务器，最终再把内容返回给客户端。而在 Node.js 中，很多工作需要自己来做（并不是都要自己动手，因为有第三方框架的帮助）。

#### 使用 http  模块

Node.js 由于不需要另外的 HTTP 服务器，因此减少了一层抽象，给性能带来不少提升，但同时也因此而提高了开发难度。举例来说，要实现一个 POST 数据的表单，例如：

```html
<form method = "post" action="http://localhost:3000">
    <input type="text" name="title">
    <textarea name="text"></textarea>
    <input type="submit">
</form>
```

这个表单包含两个字段： title 和  text ，提交时以 POST 的方式将请求发送给 http://localhost:3000/。 假设我们要实现的功能是将这两个字段的东西原封不动地返回给用户，PHP 只需写两行代码，储存为 index.php 放在网站根目录下即可：

```php
echo $_POST['title']
echo $_POST['text']
```

在第三章使用了类似下面的方法（用 `http`模块）

```javascript
var http = require('http');
var querystring = require('querystring');

var server = http.createServer(function(req, res) {
	var post = '';
	req.on('data', function(chunk) {
		post += chunk;
	});
	req.on('end', function() {
		post = querystring.parse(post);
        res.write(post.title);
        res.write(post.text);
		res.end();
	});
}).listen(3000);
```

PHP 的实现要比Node.js容易得多。Node.js完成一个简单的任务需要这么复杂：先建立一个 `http`实例，在其请求处理函数手动编写 `req` 对象的事件监听器，当客户端数据到达时，将POST 数据暂存在闭包的变量中，直到 `end`事件触发，解析 POST 请求，处理后返回客户端。

PHP之所以这么简单也是因为它很多事情都封装好了，而Node.js 的 `http` 模块则是底层的接口。尽管使用起来复制，但是可以帮助我们对于 HTTP 协议的理解更加清晰。

实际上，Node.js 虽然提供了  `http` 模块，却不是让你直接用这个模块进行 Web 开发的。`http` 模块仅仅是一个 HTTP 服务器内核的封装，你可以用它做任何 HTTP 服务器能做的事情，不仅仅是做一个网站，甚至实现一个 HTTP 代理服务器都行。你如果想用它直接开发网站，那么就必须手动实现所有的东西了，小到一个 POST 请求，大到 Cookie、会话的管理。当你用这种方式建成一个网站的时候，你就几乎已经做好了一个完整的框架了。

#### Express 框架

`npm` 提供了大量的第三方模块，其中 `Express` 是目前最稳定、使用最广泛，而且 Node.js 官方推荐的唯一一个 Web 开发框架。[Express](http://expressjs.com/ ) 除了为  http 模块提供了更高层的接口外，还实现了许多功能，其中包括：

- 路由控制
- 模板解析支持
- 动态视图
- 用户会话
- CSRF 保护
- 静态文件服务
- 操作控制器
- 访问日志
- 缓存
- 插件支持

需要指出的是，Express 不是一个无所不包的全能框架，像 Rails 或 Django 那样实现了模板引擎甚至 ORM （Object Relation Model，对象关系模型）。架，多数功能只是对 HTTP 协议中常用操作的封装，更多的功能需要插件或者整合其他模块来完成。

下面是 用 Express 重新实现的前面的例子

```javascript
var express = require('express');

var app =  express.createServer();
app.use(express.bodyParser());
app.all('/', function(){
    res.send(req.body.title + req.body.text);
});
app.listen(3000);
```



### 快速开始

#### 安装 Express

```shell
$ npm install -g express
```

安装完成后， 使用 `express --help`  查看帮助信息

```shell
C:\Users\Administrator>express --help

  Usage: express [options] [dir]


  Options:

        --version        output the version number
    -e, --ejs            add ejs engine support
        --pug            add pug engine support
        --hbs            add handlebars engine support
    -H, --hogan          add hogan.js engine support
    -v, --view <engine>  add view <engine> support (dust|ejs|hbs|hjs|jade|pug|tw
ig|vash) (defaults to jade)
        --no-view        use static html instead of view engine
    -c, --css <engine>   add stylesheet <engine> support (less|stylus|compass|sa
ss) (defaults to plain css)
        --git            add .gitignore
    -f, --force          force on non-empty directory
    -h, --help           output usage information
```

Express 在初始化一个项目的时候需要指定模板引擎，默认支持Jade和ejs，为了降低学习难度推荐使用 ejs ，同时暂时不添加 CSS 引擎和会话支持。（ejs （Embedded JavaScript） 是一个标签替换引擎，其语法与 ASP、PHP 相似，易于学习，目前被广泛应用。Express 默认提供的引擎是 jade，它颠覆了传统的模板引擎，制定了一套完整的语法用来生成 HTML 的每个标签结构，功能强大但不易学习。）

#### 建立工程

通过以下命令建立网站基本结构：

```shell
express -e microblog
```

当前目录下出现了子目录 microblog，并且产生了一些文件：

```shell
F:\赖彬鸿\git-project\usual\LearnNodeJsCode\express>express -e microblog

  warning: option `--ejs' has been renamed to `--view=ejs'

   create : microblog\
   create : microblog\public\
   create : microblog\public\javascripts\
   create : microblog\public\images\
   create : microblog\public\stylesheets\
   create : microblog\public\stylesheets\style.css
   create : microblog\routes\
   create : microblog\routes\index.js
   create : microblog\routes\users.js
   create : microblog\views\
   create : microblog\views\error.ejs
   create : microblog\views\index.ejs
   create : microblog\app.js
   create : microblog\package.json
   create : microblog\bin\
   create : microblog\bin\www

   change directory:
     > cd microblog

   install dependencies:
     > npm install

   run the app:
     > SET DEBUG=microblog:* & npm start
```

按照提示进入 microblog 文件夹，安装依赖 ，打开 目录中的 `package.json` 文件，可以看到具体的内容。

```json
{
  "name": "microblog",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "start": "node ./bin/www"
  },
  "dependencies": {
    "cookie-parser": "~1.4.3",
    "debug": "~2.6.9",
    "express": "~4.16.0",
    "http-errors": "~1.6.2",
    "jade": "~1.11.0",
    "morgan": "~1.9.0"
  }
}

```



#### 启动服务器

`npm start`启动服务器，可以看到以下内容，打开 http://localhost:3000/

![express启动页面](/images/2018-09-21-NodeJs-Part5-express.png)

#### 工程的结构

除了 package.json，它只产生了三个个 JavaScript 文件 app.js ， routes/index.js和routes/users.js。可以看出来这里将路由进行了分类，其中users.js 的是路由的路径前缀有user的。模板引擎 ejs 也有两个文件 index.ejs 和layout.ejs，此外还有样式表 style.css。

##### app.js

```javascript
var createError = require('http-errors');
var express = require('express');
var path = require('path');
var cookieParser = require('cookie-parser');
var logger = require('morgan');

var indexRouter = require('./routes/index');
var usersRouter = require('./routes/users');

var app = express();

// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');

app.use(logger('dev'));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));

app.use('/', indexRouter);
app.use('/users', usersRouter);

// catch 404 and forward to error handler
app.use(function(req, res, next) {
  next(createError(404));
});

// error handler
app.use(function(err, req, res, next) {
  // set locals, only providing error in development
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // render the error page
  res.status(err.status || 500);
  res.render('error');
});

module.exports = app;
```

> var indexRouter = require('./routes/index'); var usersRouter = require('./routes/users');

可以看出来 express 将路由分成了两个，并且进行了封装，在相对应的页面是用 `router.get`去替换教程中 `app.get`，然后再将 `router` exports  出来。

`app.set` 是 Express 的参数设置工具，接受一个键（key）和一个值（value），可用的参数如下所示。

- `basepath` ：基础地址，通常用于 res.redirect() 跳转。
- `views` ：视图文件的目录，存放模板文件。
- `view engine` ：视图模板引擎。
- `view options` ：全局视图参数对象。
- `view cache` ：启用视图缓存。
- `case sensitive routes` ：路径区分大小写。
- `strict routing` ：严格路径，启用后不会忽略路径末尾的“  / ”。
- `jsonp callback` ：开启透明的 JSONP 支持。

##### routes/index.js和routes/users.js

routes/index.js以及users.js 是路由文件，相当于控制器，用于组织展示的内容：

```javascript
//index.js 

var express = require('express');
var router = express.Router();

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;
```

```javascript
// users.js
var express = require('express');
var router = express.Router();

/* GET users listing. */
router.get('/', function(req, res, next) {
  res.send('respond with a resource');
});

module.exports = router;

```

两个文件的区别在于 users.js 的路由前面还有个 users的前缀，即是访问 http://localhost:3000/users ,才可以看到 `respond with a resource`。这个在 `app.js`中可以看出来。

` res.render('index', { title: 'Express' })`,功能是调用模板解析引擎，翻译名为 `index` 的模板，并传入一个对象作为参数，这个对象只有一个属性，即  `title: 'Express'` 。

##### index.ejs

ndex.ejs 是模板文件，即 routes/index.js 中调用的模板，内容是：

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

它的基础是 HTML 语言，其中包含了形如  `<%= title %>`  的标签，功能是显示引用的变量，即  `res.render` 函数第二个参数传入的对象的属性。

### 路由控制

#### 工作原理

当通过浏览器访问 app.js 建立的服务器时，会看到一个简单的页面，实际上它已经完成了许多透明的工作，现在就来解释一下它的工作机制，以帮助理解网站的整体架构。访问 http://localhost:3000，浏览器会向服务器发送以下请求：

```http
GET / HTTP/1.1
Host: localhost:3000
Connection: keep-alive
Pragma: no-cache
Cache-Control: no-cache
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.81 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
Cookie: bdshare_firstime=1534228662871; scroll-cookie=0|/
```

其中第一行是请求的方法、路径和 HTTP 协议版本，后面若干行是 HTTP 请求头。 app  会解析请求的路径，调用相应的逻辑。最终视图模板生成 HTML 页面，返回给浏览器，返回的内容是：

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Express</title>
    <link rel='stylesheet' href='/stylesheets/style.css' />
  </head>
  <body>
    <h1>Express</h1>
    <p>Welcome to Express</p>
  </body>
</html>

```

浏览器在接收到内容以后，经过分析发现要获取 /stylesheets/style.css，因此会再次向服务器发起请求。app.js 中并没有一个路由规则指派到 /stylesheets/style.css，但  app 通过`app.use(express.static(__dirname + '/public'))`  配置了静态文件服务器，因此 /stylesheets/style.css 会定向到 `app.js` 所在目录的子目录中的文件 public/stylesheets/style.css，向客户端返回以下信息：

```http
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Fri, 21 Sep 2018 02:09:44 GMT
ETag: W/"6f-165f9e3dc52"
Content-Type: text/css; charset=UTF-8
Content-Length: 111
Date: Fri, 21 Sep 2018 02:25:45 GMT
Connection: keep-alive

body {
  padding: 50px;
  font: 14px "Lucida Grande", Helvetica, Arial, sans-serif;
}

a {
  color: #00B7FF;
}
```

由 Express 创建的网络架构如下图



![express网站的架构](/images/2018-09-21-NodeJs-Part5-express网站的架构.png)

这是一个典型的 MVC 架构，浏览器发起请求，由路由控制器接受，根据不同的路径定向到不同的控制器。控制器处理用户的具体请求，可能会访问数据库中的对象，即模型部分。控制器还要访问模板引擎，生成视图的 HTML，最后再由控制器返回给浏览器，完成一次请求。

#### 创建路由规则

当我们在浏览器中访问譬如 http://localhost:3000/abc 这样不存在的页面时，服务器会在响应头中返回 404 Not Found 错误,这是因为 /abc 是一个不存在的路由规则，而且它也不是一个 public 目录下的文件，所以Express返回了404 Not Found的错误。

假设要创建一个地址为  /hello 的页面，内容是当前的服务器时间，在 routes/index.js 中加一行

```javascript
router.get('/hello', function(req, res, next) {
  res.send('The time is ' + new Date().toString());
});
```

重启 app.js，在浏览器中访问 http://localhost:3000/hello ，就可以看到输出的时间。刷新网页看到时间的变化，同样可以在 routes/users.js 中添加，那就是要访问 http://localhost:3000/users/hello .

服务器在开始监听之前，设置好了所有的路由规则，当请求到达时直接分配到响应函数。routes.get 是路由规则创建函数，它接受两个参数，第一个参数是请求的路径，第二个参数是一个回调函数，该路由规则被触发时调用回调函数，其参数表传递三个参数，分别是  req，  res 和 next，表示请求信息，响应信息和`next()`在函数体内调用以将控制权交给下一个回调。

#### 路径匹配

上面的例子是为固定的路径设置路由规则，Express 还支持更高级的路径匹配模式。例如想要展示一个用户的个人页面，路径为 `/users/[username]`，可以用下面的方法定义路由规则：

```javascript
// router/users.js

router.get('/:username', function(req, res, next) {
  res.send('user: ' + req.params.username);
});
```

修改以后重启 app.js，访问 http://localhost:3000/users/lbh， 可以看到页面显示了以下内容：

> user: lbh

路径规则 `/:username` 会被自动编译为正则表达式，类似于 `\/user\/([^\/]+)\/?`这样的形式。路径参数可以在响应函数中通过 `req.params`  的属性访问。

路径规则同样支持 JavaScript 正则表达式，例如  `router.get(\/user\/([^\/]+)\/?,callback)` 。这样的好处在于可以定义更加复杂的路径规则，而不同之处是匹配的参数是匿名的，因此需要通过 `req.params[0]` 、 `req.params[1]`  这样的形式访问。

#### REST 风格的路由规则

Express 支持 REST 风格的请求方式，REST 的意思是 表征状态转移（Representational State Transfer），它是一种基于 HTTP 协议的网络应用的接口风格，充分利用 HTTP 的方法实现统一风格接口的服务。HTTP 协议定义了以下8种标准的方法。

-   GET：请求获取指定资源。
-   HEAD：请求指定资源的响应头。

-   POST：向指定资源提交数据。
-   PUT：请求服务器存储一个资源。
-   DELETE：请求服务器删除指定资源。
-   TRACE：回显服务器收到的请求，主要用于测试或诊断。
-   CONNECT：HTTP/1.1 协议中预留给能够将连接改为管道方式的代理服务器。
-   OPTIONS：返回服务器支持的HTTP请求方法。

其中我们经常用到的是 GET、POST、PUT 和 DELETE 方法。根据 REST 设计模式，这4种方法通常分别用于实现以下功能。

-   GET：获取
-   POST：新增
-   PUT：更新
-   DELETE：删除

它们的特点

| 请求方式 | 安 全 | 幂 等 |
| -------- | ----- | ----- |
| GET      | 是    | 是    |
| POST     | 否    | 否    |
| PUT      | 否    | 是    |
| DELETE   | 否    | 是    |

所谓的安全是指没有副作用，即请求不会对资源产生变动，连续访问多次所获得的结果不受访问者的影响。而幂等指的是重复请求多次与请求一次的效果是一样的，比如获取和更新操作是幂等的，这与新增不同。删除也是幂等的，即重复删除一个资源，和删除一次是一样的。

Express 对每种 HTTP 请求方法都设计了不同的路由绑定函数，例如前面例子全部是 `router.get` ，表示为该路径绑定了 GET 请求，向这个路径发起其他方式的请求不会被响应。

下表是 Express 支持的所有 HTTP 请求的绑定函数。（没有封装前）

| 请求方式 | 绑定函数                    |
| -------- | --------------------------- |
| GET      | app.get(path, callback)     |
| POST     | app.post(path, callback)    |
| PUT      | app.put(path, callback)     |
| DELETE   | app.delete(path, callback)  |
| PATCH    | app.patch(path, callback)   |
| TRACE    | app.trace(path, callback)   |
| CONNECT  | app.connect(path, callback) |
| OPTIONS  | app.options(path, callback) |
| 所有方法 | app.all(path, callback)     |

绑定某个路径的 POST 请求，则可以用 `app.post(path, callback)`  的方法。需要注意的是 `app.all`  函数，它支持把所有的请求方式绑定到同一个响应函数，是一个非常灵活的函数。

#### 控制权转移

Express 支持同一路径绑定多个路由响应函数，例如：

```javascript
app.all('/user/:username', function(req, res) {
	res.send('all methods captured');
});
app.get('/user/:username', function(req, res) {
	res.send('user: ' + req.params.username);
});
```

但当你访问任何被这两条同样的规则匹配到的路径时，会发现请求总是被前一条路由规则捕获，后面的规则会被忽略。原因是 Express 在处理路由规则时，会优先匹配先定义的路由规则，因此后面相同的规则被屏蔽。

Express 提供了路由控制权转移的方法，即回调函数的第三个参数 `next` ，通过调用next() ，会将路由控制权转移给后面的规则，例如：

```javascript
app.all('/user/:username', function(req, res) {
	res.send('all methods captured');
    next();
});
app.get('/user/:username', function(req, res) {
	res.send('user: ' + req.params.username);
});
```

当访问被匹配到的路径时，如 http://localhost:3000/user/lbh ，会发现终端中打印了  all methods captured ，而且浏览器中显示了  user: lbh 。这说明请求先被第一条路由规则捕获，完成 console.log  使用 `next()` 转移控制权，又被第二条规则捕获，向浏览器返回了信息。

这是一个非常有用的工具，可以让我们轻易地实现中间件，而且还能提高代码的复用程度。例如我们针对一个用户查询信息和修改信息的操作，分别对应了 GET 和 PUT 操作，而两者共有的一个步骤是检查用户名是否合法，因此可以通过 `next()`  方法实现：

```javascript
// router/users.js
var users = {
  'lbh': {
    name: 'lbh',
    website: 'http://laibh.top'
  }
};
router.all('/:username', function (req, res, next) {
  // 检查用户是否存在
  if (users[req.params.username]) {
    next();
  } else {
    next(new Error(req.params.username + ' does not exist.'));
  }
});
router.get('/:username', function (req, res) {
  // 用户一定存在，直接展示
  res.send(JSON.stringify(users[req.params.username]));
});
router.put('/:username', function (req, res) {
  // 修改用户信息
  res.send('Done');
});
```

访问 http://localhost:3000/users/lbh 可以看到：

> {"name":"lbh","website":"http://laibh.top"}

访问其他网址，例如： http://localhost:3000/users/xxx，则会出现

> xxx does not exist.

上面例子中， `router.all`  定义的这个路由规则实际上起到了中间件的作用，把相似请求的相同部分提取出来，有利于代码维护其他 `next` 方法如果接受了参数，即代表发生了错误。使用这种方法可以把错误检查分段化，降低代码耦合度。

未完待续，后面即将讲模板引擎、建立微博网站、用户注册登录、发表微博等等