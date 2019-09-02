---
title: 好玩的Nodejs —— 使用 Node.js进行 Web 开发（下）
date: 2018-10-11  16:31:54
tags: Nodejs
categories: 
	- Nodejs
---



国庆回家因为拜访做客的原因，没有时间可以更新，回来又要开始赶项目，今天趁着后端去开会把主机都拔了。把用户注册和登录以及发表微博的功能给解决了。

## 使用 Node.js 进行 Web 开发（下）

### 用户注册和登录

在上一节我们使用 Bootstrap 创建了网站的基本框架。在这一节我们要实现用户会话的功能，包括用户注册和登录状态的维护。为了实现这些功能，我们需要引入会话机制来记录用户状态，还要访问数据库来保存和读取用户信息。现在就让我们从数据库开始。

#### 访问数据库

选用 MongoDB 作为网站的数据库系统，它是一个开源的 NoSQL 数据库，相比 MYSQL 那样的关系型数据库，它更为轻巧，灵活，非常适合在数据规模很大、事务性不强的场合使用。

##### NoSQL

在传统的数据库中，数据库的格式是由表（table）、行（row）、字段（field）组成的。表具有固定的结构，规定了每行有哪些字段，在创建时被定义，之后修改很困难。行的格式是相同的，由若干个固定的字段组成的。每个表可能有若干个字段作为索引（index）,这其中有点是主键（primary key），用于与约束表中的数据，还有一个唯一键（unique key），确保字段中不存放重复数据。表和表之间可能还有相互的约束，称为外键（foreign key ）。对数据库的每次查询都要以行为单位，复杂的查询包括嵌套查询、连接查询和交叉表查询。

拥有这些功能的数据库被称为关系型数据库，关系型数据库通常使用一种叫做 SQL （Structured Query Language） 的查询语言作为接口，因此又被称为 SQL 数据库。典型的 SQL 数
据库有 MySQL、Oracle、Microsoft SQL Server、PostgreSQL、SQLite，等等。

NoSQL 是 1998 年被提出的，它曾经是一个轻量、开源、不提供SQL功能的关系数据库。但现在 NoSQL 被认为是 Not Only SQL 的简称，主要指非关系型、分布式、不提供 ACID 的数据库系统。正如它的名称所暗示的，NoSQL 设计初衷并不是为了取代 SQL 数据库的，而是作为一个补充，它和 SQL 数据库有着各自不同的适应领域。NoSQL 不像 SQL 数据库一样都有着统一的架构和接口，不同的 NoSQL 数据库系统从里到外可能完全不同。

##### MongoDB

MongoDB 是一个对象数据库，它没有表、行等概念，也没有固定的模式和结构，所有的数据以文档的形式存储。所谓的文档就是一个关联数组式的对象，它的内部由属性组成，一个属性对应一个数、字符串、日期、数组、甚至是一个嵌套的文档。下面是一个 MongoDB 文档的实例

```javascript
{ 	"_id" : ObjectId( "4f7fe8432b4a1077a7c551e8" ),
    "uid" : 2018,
    "username" : "lbh",
    "net9" : { "nickname" : "lbh",
    "surname" : "lbh",
    "givenname" : "lbh",
    "fullname" : "lbh",
    "emails" : [ "544289495@qq.com"],
    "website" : "http://laibh.top",
    "address" : "GuangDong University" }
}
```

上面文档中 uid 是一个整数属性， username 是字符串属性， _id 是文档对象的标识符，格式为特定的  ObjectId 。 net9  是一个嵌套的文档，其内部结构与一般文档无异。从格式来看文档好像 JSON，没错，MongoDB 的数据格式就是 JSON（准确地说，MongoDB 的数据格式是 BSON （Binary JSON），它是 JSON 的一个扩展。） ，因此与 JavaScript 的亲和性很强。在Mongodb 中对数据的操作都是以文档为单位的，当然我们也可以修改文档的部分属性。对于查询操作，我们只需要指定文档的任何一个属性，就可在数据库中将满足条件的所有文档筛选出来。为了加快查询，MongoDB 也对文档实现了索引，这一点和 SQL 数据库一样。

##### 连接数据库

在本地安装 MongoDB ,点击  http://www.mongodb.org/  去官网下载。接着在项目里面，使用命令

```bash
// 全局安装驱动
npm install mongodb -g

// 在当前项目中引入mongodb
npm install mongodb --save
```

接下来在工程的目录中创建 settings.js 文件，这个文件用于保存数据库的连接信息。我们将用到的数据库命名为 microblog，数据库服务器在本地，因此Settings.js文件的内容如下：

```javascript
module.exports = {
    cookieSecret: 'microblogbyvoid',
    db: 'microblog',
    host: 'localhost',
};
```

其中， db 是数据库的名称， host 是数据库的地址。 cookieSecret 用于 Cookie 加密与数据库无关,留作后面有作用。

接下来在 models 子目录中创建 db.js，内容是：

```javascript
var settings = require('../settings');
var Db = require('mongodb').Db;
var Connection = require('mongodb').Connection;
var Server = require('mongodb').Server;

module.exports = new Db(settings.host, new Server(Connection.DEFAULT_PORT, {}));
```

以上代码通过  module.exports  输出了创建的数据库连接，在后面的小节中我们会用到这个模块。由于模块只会被加载一次，以后我们在其他文件中使用时均为这一个实例。

#### 会话支持

在完成用户注册和登录功能之前,需要先了解会话的概念。会话是一种持久的网络协议，用于完成服务器和客户端之间一些交互行为，会话是一个比较连接粒度更大的概念，一次会话可能包含多次连接，每次连接都被认为是会话的一些操作。在网络应用开发中，有必要实现会话来帮助用户交互。例如网上购物的场景，用户浏览了多个页面，购买了一些物品，这些请求在多次连接中完成。许多应用层网络协议都是由会话支持的，如 FTP、Telnet 等，而 HTTP 协议是无状态的，本身不支持会话，因此在没有额外手段的帮助下，前面场景中服务器不知道用户购买了什么。

为了在无状态的 HTTP 协议之上实现会话，Cookie 诞生了。Cookie 是一些存储在客户端的消息，每次连接的时候由浏览器向服务器递交，服务器也向浏览器发起存储 Cookie 的请求，依靠这样的手段服务器可以识别客户端。我们通常意义上的 HTTP 会话功能就是这样实现的。具体来说，浏览器首次向服务器发起请求时，服务器生成一个唯一标识符并发送给客户端浏览器，浏览器将这个唯一标识符存储在 Cookie 中，以后每次再发起请求，客户端浏览器都会向服务器传送这个唯一标识符，服务器通过这个唯一标识符来识别用户。

对于开发者来说，我们无须关心浏览器端的存储，需要关注的仅仅是如何通过这个唯一标识符来识别用户。很多服务端脚本语言都有会话功能，如 PHP，把每个唯一标识符存储到文件中。Express 也提供了会话中间件，默认情况下是把用户信息存储在内存中，但我们既然已经有了 MongoDB，不妨把会话信息存储在数据库中，便于持久维护。为了使用这一功能，我们首先要获得一个叫做 connect-mongo  的模块。

```bash
npm i connect-mongo -S
```

在 app.js 中添加以下内容

```javascript
var MongoStore = require('connect-mongo');
var settings = require('./settings');

app.use(express.session({
  secret: settings.cookieSecret,
  store: new MongoStore({
    db: settings.db
  })
}));
```

其中 express.cookieParser()  是 Cookie 解析的中间件。 express.session()  则提供会话支持，设置它的  store  参数为  MongoStore  实例，把会话信息存储到数据库中，以避免丢失。
在后面可以通过 req.session  获取当前用户的会话对象，以维护用户相关的信息。



#### 注册和登入

我们已经准备好了数据库访问和会话存储的相关信息，接下来开始实现网站的第一个功能，用户注册和登入。

##### 注册页面

首先来设计用户注册页面的表单，创建 views/reg.ejs 文件，内容是：

````html
<form class="form-horizontal" method="post">
    <fieldset>
        <legend>用户注册</legend>
        <div class="control-group">
            <label class="control-label" for="username">用户名</label>
            <div class="controls">
                <input type="text" class="input-xlarge" id="username" name="username">
                <p class="help-block">你的账户名称，用于登录和显示。</p>
            </div>
        </div>
        <div class="control-group">
            <label class="control-label" for="password">口令</label>
            <div class="controls">
                <input type="password" class="input-xlarge" id="password" name="password">
            </div>
        </div>
        <div class="control-group">
            <label class="control-label" for="password-repeat">重复输入口令</label>
            <div class="controls">
                <input type="password" class="input-xlarge" id="password-repeat" name="password-repeat">
            </div>
        </div>
        <div class="form-actions">
            <button type="submit" class="btn btn-primary">注册</button>
        </div>
    </fieldset>
</form>
````

这个表单中有3个输入单元，分别是  username 、 password  和  password-repeat 。表单的请求方法是 POST，将会发送到相同的路径下。

接着在 router/index.js 里面设置好路由

```javascript
// 用户注册
router.get('/reg', function (req, res, next) {
  res.render('reg', { title: '用户注册' });
});
```

现在运行 app.js，在浏览器中打开  http://localhost:3000/reg ，可以看到

![注册页面](/images/2018-09-21-NodeJs-Part5-注册页面.png)

##### 注册响应

实现注册响应，在 router/index.js 里面添加 /reg 的 POST 响应函数

```javascript
router.post('/reg', function (req, res, next) {
  console.log(res.body);
  if (req.body['password-repeat'] != req.body['password']) {
    req.flash('error', '两次输入的口令不一致');
    return res.redirect('/reg');
    }
  // 生成口令的散列值
  var md5 = crypto.createHash('md5');
  var password = md5.update(req.body.password).digest('base64');
  var newUser = new User({
    name: req.body.username,
    password: password,
  });

  // 检查用户名是否存在
  User.get(newUser.name, function (err, user) {
    if (user) {
      err = "Username is already exists."
    }
    if (err) {
      req.flash('error', err);
      return res.redirect('/reg');
    }
    // 如果不存在则新增用户
    newUser.save(function (err) {
      if (err) {
        req.flash('error', err);
        return res.redirect('/reg');
      }
      req.session.user = newUser;
      req.flash('success', '注册成功');
      res.redirect('/reg');
    });
  });
});
```

- `req.body` 就是 POST 请求信息解析过后的对象，例如我们要访问用户传递的 password 域的值，只需访问 req.body['password'] 即可
- `req.flash` 是 Express 提供的一个奇妙的工具，通过它可以保存的变量只会在用户当前和下一次的请求中被访问，之后就会被清除掉，通过它可以很方便地实现页面的通知和错误消息显示功能
- `res.redirect` 是重定向功能，通过它会向用户返回一个 303 See Other 状态，通知浏览器转向相应页面。
- `crypto` 是 Node.js 的一个核心模块，功能是加密并生成各种散列，使用它之前首先要声明 var crypto = require('crypto') 
- `User` 是设计的用户对象，后面会详细介绍。
- `User.get` 的功能是通过用户名获取已知用户，在这里我们判断用户名是否已经存在。 User.save 可以将用户对象的修改写入数据库。
- 通过  req.session.user = newUser  向会话对象写入了当前用户的信息。

##### 用户模型

在前面的代码中，我们直接使用了  User 对象。 User 是一个描述数据的对象，即 MVC架构中的模型。前面我们使用了许多视图和控制器，这是第一次接触到模型。与视图和控制器不同，模型是真正与数据打交道的工具，没有模型，网站就只是一个外壳，不能发挥真实的作用，因此它是框架中最根本的部分。现在就让我们来实现  User 模型吧。

在 models 目录中创建 user.js 的文件，内容如下：

```javascript
var mongodb = require('./db');

function User(user) {
    this.name = user.name;
    this.password = user.password;
};
module.exports = User;


/*
 * 保存一个用户到数据库
 * @param {Function} callback: 执行完数据库操作的应该执行的回调函数
 */
User.prototype.save = function save(callback) {
    // 存入 Mongodb 的文档
    var user = {
        name: this.name,
        password: this.password,
    };
    mongodb.open(function (err, db) {
        if (err) {
            return callback(err);
        }
        // 读取 users 集合
        db.collection('users', function (err, collection) {
            if (err) {
                mongodb.close();
                return callback(err);
            }
            //为name属性添加索引，新版本的ensureIndex方法需要一个回调函数
            collection.ensureIndex('name', { unique: true });
            //写入user文档
            collection.insert(user, { safe: true }, function (err, user) {
                mongodb.close();
                callback(err, user);
            });
        });
    });
}


/*
 * 查询在集合`users`是否存在一个制定用户名的用户
 * @param {String} username: 需要查询的用户的名字 
 * @param {Function} callback: 执行完数据库操作的应该执行的回调函数
 */

User.get = function get(username, callback) {
    mongodb.open(function (err, db) {
        if (err) {
            return callback(err);
        }
        // 读取 users 集合
        db.collection('users', function (err, collection) {
            if (err) {
                mongodb.close();
                return callback(err);
            }
            // 查找 name 属性为 username 的文档
            collection.findOne({ name: username }, function (err, doc) {
                mongodb.close();
                if (doc) {
                    // 封装文档为 User 对象
                    var user = new User(doc);
                    callback(err, user);
                } else {
                    callback(err, null);
                }
            });
        });
    });
};
```

以上代码实现了两个接口， User.prototype.save  和 User.get， 前者是对象实例的方法，用于将用户对象的数据保存到数据库中，后者是对象构造函数的方法，用于从数据库中查找指定的用户。

##### 视图交互

现在几乎已经万事俱备，只差视图的支持了。为了实现不同登录状态下页面呈现不同内容的功能，我们需要创建动态视图助手，通过它我们才能在视图中访问会话中的用户数据。同时为了显示错误和成功的信息，也要在动态视图助手中增加响应的函数。
打开 app.js，添加以下代码：

```javascript
var flash = require('connect-flash');
// 在路由后面配置
app.use(flash());
```

然后在 routes/index.js 里面添加

```javascript
router.use(function (req, res, next) {
  res.locals.user=req.session.user;
  var err = req.flash('error');
  var success = req.flash('success');

  res.locals.error = err.length ? err : null;
  res.locals.success = success.length ? success : null;
   
  next();
});
```

修改 header.ejs 中的导航部分

```ejs
 <ul class="nav">
    <li class="active"><a href="/">首页</a></li>
    <% if (!user) { %>
    <li><a href="/login">登入</a></li>
    <li><a href="/reg">注册</a></li>
    <% } else { %>
    <li><a href="/logout">登出</a></li>
    <% } %>
 </ul>
```

上面功能是为已登入用户和未登入用户显示不同的信息。在  container  中， <%- body %>之前加入：

```ejs
<% if (success) { %>
    <div class="alert alert-success">
      <%= success %>
    </div>
<% } %>
<% if (error) { %>
    <div class="alert alert-error">
      <%= error %>
    </div>
<% } %>
```

它的功能是页面通知。

下面分别是注册时遇到错误和注册成功以后的画面。

![两次口令不一致](/images/2018-09-21-NodeJs-Part5-注册两次密码不一样.png)

![注册成功](/images/2018-09-21-NodeJs-Part5-注册成功.png)

##### 登入和登出

完成用户注册的功能以后再实现用户登入和登出就相当容易了。把下面的代码加到 routes/index.js 中：

```javascript
// 用户登录
router.get('/login', function (req, res, next) {
  res.render('login', { title: '用户登录' });
});
router.post('/login', function (req, res, next) {
  // 生成口令的散列值
  var md5 = crypto.createHash('md5');
  var password = md5.update(req.body.password).digest('base64');
  User.get(req.body.username, function (err, user) {
    if (!user) {
      req.flash('error', '用户不存在');
      return res.redirect('/login');
    }
    if (user.password != password) {
      req.flash('error', '用户口令错误');
      return res.redirect('/login');
    }
    req.session.user = user;
    req.flash('success', '登入成功');
    res.redirect('/');
  });
});
```

在这里清晰地看出登入和登出仅仅是 req.session.user 变量的标记，非常简单。但这会不会有安全性问题呢？不会的，因为这个变量只有服务端才能访问到，只要不是黑客攻破了整个服务器，无法从外部改动。

创建 views/login.ejs

```html
<form class="form-horizontal" method="post">
    <fieldset>
        <legend>用户登入</legend>
        <div class="control-group">
            <label class="control-label" for="username">用户名</label>
            <div class="controls">
                <input type="text" class="input-xlarge" id="username" name="username">
            </div>
        </div>
        <div class="control-group">
            <label class="control-label" for="password">口令</label>
            <div class="controls">
                <input type="password" class="input-xlarge" id="password" name="password">
            </div>
        </div>
        <div class="form-actions">
            <button type="submit" class="btn btn-primary">登入</button>
        </div>
    </fieldset>
</form>
```

在浏览器中访问  http://localhost:3000/login   可以看到页面

![用户登录](/images/2018-09-21-NodeJs-Part5-用户登录.png)

至此用户注册和登录的功能就完全实现了。



#### 页面权限控制

还有一个工作要完成，就是为页面设置访问权限。例如，登出功能应该只对登入的用户开发，注册和登入页面则应该阻止已登入的用户访问。实现这一点，最简单的方法是在每个页面的路由响应函数内检查用户是否已经登录，但这会带来很多重复代码，因此利用路由中间件来实现这个功能。

同一路径绑定多个响应函数的方法，通过调用  next() 转移控制权，这种方法叫做路由中间件。把用户登入状态检查放在路由中间件中，每个路径前增加路由中间件，既可以实现页面权限控制。

routes/index.js

```javascript
// 用户注册
router.get('/reg', checkNotLogin);
router.get('/reg', function (req, res, next) {
  res.render('reg', { title: '用户注册' });
});
router.post('/reg', checkNotLogin);
router.post('/reg', function (req, res, next) {
  if (req.body['password-repeat'] != req.body['password']) {
    req.flash('error', '两次输入的口令不一致');
    return res.redirect('/reg');
  }
  // 生成口令的散列值
  var md5 = crypto.createHash('md5');
  var password = md5.update(req.body.password).digest('base64');
  var newUser = new User({
    name: req.body.username,
    password: password,
  });

  // 检查用户名是否存在
  User.get(newUser.name, function (err, user) {
    if (user) {
      err = "Username is already exists."
    }
    if (err) {
      req.flash('error', err);
      return res.redirect('/reg');
    }
    // 如果不存在则新增用户
    newUser.save(function (err) {
      if (err) {
        req.flash('error', err);
        return res.redirect('/reg');
      }
      req.session.user = newUser;
      req.flash('success', '注册成功');
      res.redirect('/');
    });
  });
});
// 用户登录
router.get('/login', checkNotLogin);
router.get('/login', function (req, res, next) {
  res.render('login', { title: '用户登录' });
});
router.post('/login', checkNotLogin);
router.post('/login', function (req, res, next) {
  // 生成口令的散列值
  var md5 = crypto.createHash('md5');
  var password = md5.update(req.body.password).digest('base64');
  User.get(req.body.username, function (err, user) {
    if (!user) {
      req.flash('error', '用户不存在');
      return res.redirect('/login');
    }
    if (user.password != password) {
      req.flash('error', '用户口令错误');
      return res.redirect('/login');
    }
    req.session.user = user;
    req.flash('success', '登入成功');
    res.redirect('/');
  });
});
// 用户登出 
router.get('/logout', checkLogin);
router.get('/logout', function (req, res, next) {
  req.session.user = null;
  req.flash('success', '登出成功');
  res.redirect('/');
});

function checkLogin(req, res, next) {
  if(!req.session.user){
    req.flash('error','未登入');
    return res.redirect('/login');
  }
  next();
}

function checkNotLogin(req, res, next){
  if(req.session.user){
    req.flash('error','已登入');
    return res.redirect('/');
  }
  next();
}
```



### 发表微博

现在网站已经具备了用户注册、登入、页面权限控制的功能，这些功能为网站最核心的部分——发表微博做好了准备,我们可以来实现发表微博的功能，完成整个网站的设计。

#### 微博模型

从模型设计开始，仿照用户模型，将微博模型命名为 Post 对象，它拥有与 User 相似的接口，分别是 Post.get 和 Post.prototype.save。Post.get 的功能是从数据库中获取微博，可以指定用户获取，也可以获取全部的内容。Post.prototype.save 是 Post 对象实例的方法，用于将对象的变动保存到数据库中。

创建 models/post.js，写入以下内容：

```javascript
var mongodb = require('./db');

function Post(username, post, time) {
    this.user = username;
    this.post = post;
    if (time) {
        this.time = time;
    } else {
        this.time = new Date();
    }
}

module.exports = Post;


/*
 * 保存一条发言到数据库
 * @param {Function} callback: 执行完数据库操作的应该执行的回调函数
 */

Post.prototype.save = function save(callback) {
    // 存入 Mongodb 的文档
    var post = {
        user: this.user,
        post: this.post,
        time: this.time,
    };
    mongodb.open(function (err, db) {
        if (err) {
            return callback(err);
        }
        // 读取 posts 集合
        db.collection('posts', function (err, collection) {
            if (err) {
                mongodb.close();
                return callback(err);
            }
            // 为 user 属性添加索引
            // collection.ensureIndex('user');
            // 写入 post 文档
            collection.insert(post, { safe: true }, function (err, post) {
                mongodb.close();
                callback(err, post);
            });
        });
    });
};


/*
 * 查询一个用户的所有发言
 * @param {String} username: 需要查询的用户的名字 
 * @param {Function} callback: 执行完数据库操作的应该执行的回调函数
 */

Post.get = function get(username, callback) {
    mongodb.open(function (err, db) {
        if (err) {
            return callback(err);
        }
        // 读取 posts 集合
        db.collection('posts', function (err, collection) {
            if (err) {
                mongodb.close();
                return callback(err);
            }
            // 查找 user 属性为 username 的文档，如果 username 是 null 则匹配全部
            var query = {};
            if (username) {
                query.user = username;
            }
            collection.find(query).sort({ time: -1 }).toArray(function (err, docs) {
                mongodb.close();
                if (err) {
                    callback(err, null);
                }
                // 封装 posts 为 Post 对象
                var posts = [];
                docs.forEach(function (doc, index) {
                    var post = new Post(doc.user, doc.post, doc.time);
                    posts.push(post);
                });
                callback(null, posts);
            });
        });
    });
}
```

在后面我们会通过控制器调用这个模块。

#### 发表微博

通过 POST 方法访问  /post 以发表微博，现在来实现这个控制器。在 routes/index.js 中添加下面的代码：

```javascript
// 用户发表微博
router.post('/post', checkLogin);
router.post('/post', function (req, res) {
  var currentUser = req.session.user;
  var post = new Post(currentUser.name, req.body.post);
  post.save(function (err) {
    if (err) {
      req.flash('error', err);
      return res.redirect('/');
    }
    req.flash('success', '发表成功');
    res.redirect('/u/' + currentUser.name);
  });
});
```

这段代码通过 req.session.user  获取当前用户信息，从 req.body.post  获取用户发表的内容，建立 Post  对象，调用 save()  方法存储信息，最后将用户重定向到用户页面。

在这里会报错，需要在 app.js 引入 bodyParser

```bash
npm i bodyParser
```

安装后引入，并且运用

```javascript
app.use(bodyParser.urlencoded());
```

#### 用户页面

用户页面的功能是展示用户发表的所有内容，在routes/index.js中加入以下代码：

```javascript
// 用户的主页 
router.get('/u/:user', function (req, res) {
  User.get(req.params.user, function (err, user) {
    if (!user) {
      req.flash('error', '用户不存在');
      return res.redirect('/');
    }
    Post.get(user.name, function (err, posts) {
      console.log(posts);
      if (err) {
        req.flash('error', err);
        return res.redirect('/');
      }
      res.render('user', {
        title: user.name,
        posts: posts,
      });
    });
  });
});
```

它的功能是首先检查用户是否存在，如果存在则从数据库中获取该用户的微博，最后通过  posts 属性传递给  user 视图。views/user.ejs 的内容如下：

```ejs
<% if (user) { %>
    <% include say.ejs %>
<% } %>
<% include posts.ejs %>
```

根据 DRY 原则，我们把重复用到的部分都提取出来，分别放入 say.ejs 和 posts.ejs。say.ejs的功能是显示一个发表微博的表单，它的内容如下：

```html
<form method="post" action="/post" class="well form-inline center" style="text-align:
center;">
    <input type="text" class="span8" name="post">
    <button type="submit" class="btn btn-success"><i class="icon-comment icon-white">
        </i> 发言</button>
</form>
```

posts.ejs 的目的是按照行列显示传入的  posts 的所有内容：

```ejs
<% posts.forEach(function(post, index) {
	if (index % 3 == 0) { %>
	<div class="row">
		<%} %>
		<div class="span4">
			<h2><a href="/u/<%= post.user %>"><%= post.user %></a> 说</h2>
			<p><small><%= post.time %></small></p>
			<p><%= post.post %></p>
		</div>
		<% if (index % 3 == 2) { %>
	</div><!-- end row -->
	<% } %>
	<%}) %>
	<% if (posts.length % 3 != 0) { %>
</div><!-- end row -->
<%} %>
```

完成上述工作后，重启服务器。在用户的页面上发表几个微博，可以看到以下效果

![用户页面](/images/2018-09-21-NodeJs-Part5-用户页面.png)

#### 首页

最后一步是实现首页的内容。我们计划在首页显示所有用户发表的微博，按时间从新到旧的顺序。

在 routes/index.js 中添加下面代码：

```javascript
// 正式微博路由
router.get('/', function (req, res, next) {
  Post.get(null, function (err, posts) {
    if (err) {
      posts = [];
    }
    res.render('index', {
      title: '首页',
      posts: posts,
      user: req.session.user,
    });
  });
});
```

它的功能是读取所有用户的微博，传递给页面  posts 属性。接下来修改首页的模板index.ejs：

```ejs
<% include header.ejs %>
<% if (!user) { %>
<div class="hero-unit">
  <h1>欢迎来到 Microblog</h1>
  <p>Microblog 是一个基于 Node.js 的微博系统。</p>
  <p>
    <a class="btn btn-primary btn-large" href="/login">登录</a>
    <a class="btn btn-large" href="/reg">立即注册</a>
  </p>
</div>
<% } else { %>
  <% include say.ejs %>
<% } %>
<% include posts.ejs %>

<% include footer.ejs %>


```

可以看到首页效果如下

![首页](/images/2018-09-21-NodeJs-Part5-首页.png)

到这里就初步完成了一个基本博客的模型，完整的源码可以到 https://github.com/LbhFront-end/LearnNodeJsCode 查看。有问题的可以互相交流

