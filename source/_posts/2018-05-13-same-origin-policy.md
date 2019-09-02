---
title: 同源策略
date: 2018-05-13 18:45:12
tags: http同源策略
categories: 
	- http
---

同源策略
=============
## 含义  

是一种约定，是浏览器最核心也是最基本的安全功能，如果缺少了同源策略，则浏览器的正常功能都会受到影响，可以说web是构建在同源策略基础之上，浏览器只是针对同源策略的一种实现。所谓的同源指的是**域名、协议、端口相同**。  这也是狭义的跨域，跨域指的是一个域下的文档或者脚本试图去请求另一个域下的资源。另外广义的跨域：

1. 资源跳转：A链接、重定向、表单提交
2. 资源嵌入：`link`/`script`/`img`/`frame`等 dom 标签，样式中的 `background:url()`/`@font-face()`等文件外链
3. 脚本请求：js 发起的 ajax 请求，dom 和 js 对象的跨域操作等

## 目的  

保证了用户的信息安全，防止恶意的网站去窃取数据，A网站是一家银行，用户登录后就去浏览其他的网站，如果其他网站可以读A网站的cookie，而cookie往往用来保存用户的登录状态，如果用户没有退出登录，其他网站就可以冒充用户，为所欲为，浏览器还规定，提交表单不受不同源政策的影响。  

## 限制的范围  

1.cookie、LocalStorage 和 IndexDB无法读取 
2.DOM 无法获取 
3.AJAX请求无法发送  



## 常见跨域场景

```shell
URL									说明								是否运行通信
http://www.domain.com/a.js	   同一个域名，不同文件路径						运行
http://www.domain.com/b.js
http://www.domain.com/lab/c.js

http://www.domain.com:8000/a.js  同一域名，不同端口						  不允许
http://www.domain.com/b.js		

http://www.domain.com/a.js 		 同一域名，不同协议						  不允许
https://www.domain.com/a.js

http://www.domain.com/a.js 		 域名和域名对应的ip相同					  不允许
https://192.168.4.12/b.js

http://www.domain.com/a.js 		 主域相同，子域不同					       不允许
http://x.domain.com/b.js
http://domain.com/c.js

http://www.domain1.com/a.js	     不同域名						           运行
http://www.domain2.com/b.js
```

### 跨域解决方案

1. 通过 `jsonp`跨域
2. `document.domain`+`iframe`跨域
3. `location.hash`+`iframe`跨域
4. `postMessage`跨域
5. `window.name`+`iframe`跨域
6. 跨资源共享（CORS）
7. `nginx`代理跨域
8. `nodejs`中间件代理跨域
9. `webSocket`协议跨域

## 详解

### 通过 jsonp 跨域

通常为了减轻 web 服务器的负载，我们把 js/css/img 等静态资源分离到另一台独立域名的服务器上，在 html 页面中再通过相应的标签从不同域名下加载静态资源，而被浏览器允许，基于此原理，我们可以通过动态创建 `script`，再请求一个带参网址实现跨域通信。

**原生实现**

```javascript
let script = document.createElement('script');
script.type = 'text/javascript';

// 传参一个回调函数后给后端，方便后端返回时候执行这个在前端定义的回调函数
script.src = 'http://www.domain2.com:8080/login?user=admin&callback=handleCallback';
document.head.appendChild(script);

// 回调执行函数
function handleCallback(res){
    console.log(JSON.stringify(res));
}
```

服务端返回如下（返回时即执行全局函数）

```javascript
handleCallback({status:true,user:admin})
```

**jquery ajax 实现**

```javascript
$.ajax({
    url:'http://www.domain2.com:8080/login',
    type:'get',
    dataType:'jsonp',
    jsonpCallback:'handleCallback',
    data:{}
})
```

**vue.js 实现**

```javascript
this.$http.jsonp('http://www.domain2.com:8080/login',{
    params:{},
    jsonp:'handleCallback',
}).then(res =>{
    console.log(res)
})
```

**后端 node.js **

```javascript
let querystring = require('querystring');
let http = require('http');
let server = http.createServer();

server.on('request',function(req,res){
    let params = qs.parse(req.url.split('?').[1])
    let fn = params.callback;
    
    // jsonp 返回设置
    res.writeHead(200,{Content-Type;'text/javascript'})
    res.write(`(${JSON.stringify(params)})`)
    res.send()
})

server.listen('8080');
console.log('server is running at port 8080')
```

jsonp缺点：只能实现 get 一种请求。

### document.domain + iframe 跨域

此方案仅限主域相同，子域不同的跨域应用场景

实现原理：两个页面都通过 js 强制设置 `document.domain`为基础主域，就实现了主域。

父窗口：`http://www.domain.com/a.html`

```html
<iframe id='iframe' src="http://www.child.domain.com/b.html"></iframe>
<script>
    document.domain = 'domain.com'
    var user = 'admin';
</script>
```

子窗口：`http://child.domain.com/b.html`

```html
<script>
    document.domain = 'domain.com'
    console.log(window.parent.user)
</script>
```

### location.hash + iframe 跨域

实现原理：a 想跟 b 跨域相互通信，通过中间页 c 来实现，三个页面，不同域之间利用 iframe 的 location.hash 传值，相同域之间通过 js 访问来通信

具体实现：

A：a.html -> B：b.html -> A：c.html

a 与 b 不同域只能通过 hash 值单向通信，b 与 c  不同域名也只能单向通信，但 c 与 a 同域，所以 c 可以通过 `parent.parent`访问 a 页面的所有对象

a.html：`http://www.domain1.com/a.html`

```html
<iframe id="iframe" src="http://www.domain2.com/b.html" style="display:none"></iframe>
<script>
    let iframe = document.getElementById('iframe')
    // 向 b.html 传 hash
    setTimeout(function(){
        iframe.src = iframe.src + '#user=admin';
    },1000)
    // 开始给同域 c.html 的回调方法
    function onCallback(res){
        console.log(res)
    }
</script>
```

b.html：`http://www.domain1.com/c.html`

```html
<script>
    // 监听 b.html 传来的 hash 值，再传给 c.html
    window.onhashchange = function(){
        iframe.src = iframe.src + location.hash
    }
</script>
```

c.html：`http://www.domain1.com/c.html`

```html
<script>
    // 监听 b.html 传来的 hash 值
    window.onhashchange = function(){
        // 再通过操作同域 a.html 的js回调，将结果返回
        window.parent.parent.onCallback(location.hash.replace('#user=',''))
    }
</script>
```

### window.name + iframe 跨域

window.name 属性的独特之处：name 值在不同页面（甚至不同域名）加载后依旧存在，并且可以支持非常长更多 name 值（2MB）

a.html：`http://www.domain1.com/a.html`

```javascript
let proxy = function(url,callback){
    let state = 0;
    let iframe = document.createElement('iframe');
    // 加载跨域页面
    iframe.src = url;
    
    // onload 事件会触发2次，第一次加载跨域页，并留存数据于 window.name
    iframe.onload = function(){
        if(state === 1){
            // 第 2 次 onload 成功后，读取同域 window.name 中数据
            callback(iframe.contentWindow.name)
            destoryFrame();
        }else if(state === 0){
            iframe.contentWindow.location = 'http://www.domain1.com/proxy.html'
            state = 1;
        }
    }
    document.body.appendChild(iframe)
    // 获取数据后销毁和这个 iframe，释放内存，这也保证了安全（不被其他 iframe js 访问）
    function destoryFrame(){
        iframe.contentWindow.document.write('');
        iframe.contentWindow.close();
        document.body.removeChild(iframe);
    }
}

proxy('http://www.domain2.com/b.html',function(data){
    console.log(data);
})
```

proxy.html：`http://www.domain1.com/proxy.html`

中间代理页，与 a.html 同域，内容为空即可。

b.html：`http://www.domain2.com/b.html`

```html
<script>
    window.name = 'This is domain2 data!'
</script>
```

通过 iframe 的 src 属性由外域转向本地域，跨域数据即由 iframe 的 window.name 从外域传递到本地域。这个就是巧妙绕过了浏览器的跨域访问限制，但同时它又是安全操作。

### postMessage 跨域

postMessage 是 HTML5 XMLHttpRequest Level 2 中的 API,且是为数不多的可以跨域操作的 window 属性之一，它可以用于解决下面的问题：

- 页面和其打开的新窗口的数据传递
- 多窗口之间消息传递
- 页面与嵌套的 iframe 消息传递
- 上面三个场景的跨数据传递

用法：

postMessage(data,origin) 方法接受两个参数

data：html5 规范支持任意基本类型或者可复制的对象，但部分浏览器只支持字符串，所以传参时最好使用 `JSON.stringify`序列化

origin：协议+主机+端口号，可以配置为 “*”,表示可以传递任意的窗口，如果要指定和当前的窗口同源的话设置为 "/"

a.html：`http://www.domain1.com/a.html`

```html
<iframe id="iframe" src="http://www.domain2.com/b.html" style="display:none"></iframe>
<script>
    let iframe =document.getElementById('iframe');
    iframe.onload = function(){
        let data = {
            name:'haha'
        }
        // 向 domain2 传送跨域数据
        iframe.contentWindow.postMessage(JSON.stringify(data),'http://www.domain2.com')
    }
    // 接受 domain2 返回数据
    window.addEventListener('message',function(e){
        console.log(e.data)
    },false);
</script>
```



b.html：`http://www.domain2.com/b.html`

```html
<script>
    // 接受 domain1 的数据
    window.addEventListener('message',function(e){
        console.log(e.data)
        let data = JSON.parse(e.data)
        if(data){
            data.number = 16;
            // 处理完之后再发送给 domain1
            window.parent.postMessage(JSON.stringify(data),'http://www.domain1.com')
        }
    },false);
</script>
```

### 跨域资源共享（CORS）

普通跨域请求：服务端设置 `Access-Control-Allow-Origin`即可，前端无需设置，若要带 cookie 请求：前后端都需要设置。

需要注意的是：由于同源策略的限制，所要读取的 cookie 为跨域请求接口所在域名的 cookie，而非当前页。

目前，主流的浏览器都支持该功能（IE8+/9 需要使用 XDomainRequest对象来支持 CORS），CORS 也已经成为主流的跨域解决方案

#### 前端设置

**原生 ajax**:

```javascript
// 前端设置是否带 cookie
xhr.withCredentials = true;
```

```javascript
var xhr = new XMLHttpRequest(); // IE8/9 需要使用 window.XDomainRequest 兼容
// 前端设置是否带有 cookie
xhr.withCredentials = true;
xhr.open('post','http://www.domain2.com:8080/login',true);
xhr.setRequestHeader('Content-Type','application/x-www-form-urlencoded');
xhr.send('user=admin');
xhr.onreadystatechange = function(){
    if(xhr.readyState == 4 && xhr.status == 200){
        console.log(xhr.responseText);
    }
}
```

**jquery ajax**

```javascript
$.ajax({
    xhrFields:{
        withCredentials:true // 前端是否带 cookie
    },
    crossDomain:true // 会让请求头中噶博涵跨域的额外信息，但不会包含 cookie
})
```

**axios**

```javascript
axios.defaults.withCredentials = true
```

#### 服务端设置

java 后端：

```java
// 允许跨域访问的域名：若有端口需要写全（协议+域名+端口），若没有端口结尾不用加 '/'
response.setHeader('Access-Control-Allow-Origin','http://www.domain1.com');
// 允许前端带认证 cookie:启用此项后上面的域名不能为 "*"，必须指定具体的域名，否则浏览器会有提示
response.setHeader('Access-Control-Allow-Credentials','true');
// 提示 OPTIONS 预检时，后端需要设置的两个常用自定头
response.setHeader('Access-Control-Allow-Headers','Content-Type,X-Requested-With')
```

Node.js 后端：

```javascript
const http = require('http');
const server = http.createServer();
const qs = require('querystring');

server.on('request',(req,res)=>{
    let postData = '';
    // 数据块接收中
    req.addListener('data',(chunk)=>{
        postData +=chunk;
    })
    // 数据块接收完毕
    req.addListener('end',()=>{
        postData = qs.parse(postData);
        // 跨域后台设置
        res.writeHead(200,{
            'Access-Control-Allow-Credentials':'true', // 后端允许发送 cookie
            'Access-Control-Allow-Origin':'http://www.domain1.com', // 允许访问的域（协议+域名+端口）
            /* 
             * 此处设置的cookie还是domain2的而非domain1，因为后端也不能跨域写cookie(nginx反向代理可以实现)，
             * 但只要domain2中写入一次cookie认证，后面的跨域接口都能从domain2中获取cookie，从而实现所有的接口都能跨域访问
             */            
            'Set-Cookie':'l=a123456;Path=/;Domain=www.domain2.com;HttpOnly' // HttpOnly的作用是让js无法读取cookie
            
        })
        res.write(JSON.stringify(postData))
        res.end();
    })
}).listen('8080');
```

### nginx 代理跨域

**nginx 配置解决 iconfont 跨域**

浏览器跨域访问 js/css/img 等常规静态资源被同源策略许可，但 iconfont 字体文件（eot|otf|ttf|woff|svg）除外，此时可在 nginx 的静态资源服务器中加入以下配置

```javascript
location / {
    add_header Access-Control-Allow-Origin:*;
}
```

**nginx 反向代理接口跨域**

跨域原理：同源策略是浏览器的安全策略，不是 HTTP 协议的一部分。服务端调用 HTTP 接口只是使用 HTTP 协议，不会执行 JS 脚本，不需要同源策略，也就不存在跨域问题。

实现思路：通过 nginx 配置一个代理服务器（域名与 domain1相同，端口不同）做跳板机，反向代理访问 domain2 接口，并且可以顺便修改 cookie 中的 domain 信息，方便当前域 cookie 写入，实现跨域登录。

nginx 具体配置：

```xml
server{
    listen 	 		81;
    server_name 	www.domain1.com
    
    location / {
        proxy_pass http://www.domain2.com:8080, 
		proxy_cookie_domain www.domain2.com  www.domain1.com
		index index.html index.htm

		add_header 	Access-Control-Allow-Origin 	http://www.domain1.com
		add_header	Access-Control-Credentials	true
    }
}
```

前端代码：

```javascript
const xhr = new XMLHttpRequest();

xhr.withCredentials = true;

xhr.open('get','http://www.domain1.com:81/?user=admin',true)
xhr.send();
```

Node 后台实例：

```javascript
const http = require('http');
const server = http.createServer();
const qs = require('querystring');

server.on('request',(req,res)=>{
    const params = qs.parse(req.url.substring(2))
    
    res.writeHead(200,{
        'Set-cookie':'l+a123456;Path=/;Domain=www.domain2.com;HttpOnly'
    });
    res.write(JSON.stringify(params))
    res.send();
});

server.listen('8080')
console.log('server is running at port 8080');
```

### Nodejs 中间件代理跨域

node 中间件实现跨域代理，原理大致与 nginx 相同，都是通过一个代理服务器，实现数据的转发，也可以通过设置 cookieDomainRewrite 参数修改响应头中的 cookie 中域名，试下你当前域的 cookie 写入，方便接口登录认证。

**非 vue 框架的 跨域（2次跨域）**

node+express+http-proxy-middleware 搭建一个 proxy 服务器

前端代码：

```javascript
const xhr = new XMLHttpRequest();

xhr.WithCredentials = true;

xhr.open('get','http://www.domain1.com:3000/login?user=admin',true);
xhr.send();
```

中间件服务器：

```javascript
const express = require('express');
const proxy = require('http-proxy-middleware');
const app = new express();

app.use('/',proxy({
    // 代理跨域接口目标
    target:'http://www.domain2.com:8080',
    changeOrigin:true,
    
    // 修改响应头信息，实现跨域并允许带 cookie
    onProxyRes:function(proxyRes,req,res){
        res.header('Access-Control-Allow-Origin','http://www.domain1.com')
        res.header('Access-Control-Allow-Credentials','true')
    },
    // 修改响应信息中的 cookie 域名
    cookieDomainRewrite:'www.domain1.com' // 可以为 false，表示不修改
}))

app.listen(3000)
console.log('Proxy server is listen at port 3000...');
```

**vue 框架的跨域（1次跨域）**

node +webpack+webpack-dev-server 代理接口跨域。在开发环境下，由于 vue 渲染服务和接口代理服务都是 webpack-dev-server 同一个，所以页面与代理接口之间不再跨域，无需设置 headers 跨域信息。

webpack.config.js

```javascript
module.exports = {
    entry:{},
    module:{},
    devSever:{
        historyApiFallback:true,
        proxy:[{
            context:'/login',
            target:'http://www.domain2.com:8080',
            changeOrigin:true,
            secure:false,
            cookieDomainRewrite:'www.domain1.com'
        }],
        noInfo:true
    }
}
```

### WebSocket 协议跨域

websocket protocol 是 HTML5 一种新的协议，它实现了浏览器与服务器全双工通信，同时运行跨域通讯，是 server push 技术的一种很好的实现。

这里使用了 socket.io，它很好地封装了 webSocket 的接口，提供了更方便、灵活的接口，也对不支持 webSocket 的浏览器提供了向下兼容。

前端代码：

```html
<div>user input: <input type="text"></div>
<script src="https://cdn.bootcss.com/socket.io/2.2.0/socket.io.js"></script>
<script>
    const socket = io('http://www.domain2.com:8080');
    socket.on('connect',function(){
        // 监听服务端信息
        socket.on('message',function(msg){
            console.log(msg)
        })
        
        // 监听服务端关闭
        socket.on('disconnect',function(){
            console.log('server socket has closed.');
        })
    })
    
    document.getElementByTagName('input')[0].onblur = function(){
        socket.send(this.value);
    };
</script>
```

NodeJs socket 后台：

```javascript
const http  = require('http');
const socket = require('socket.io');

const server = http.createServer((req,res)=>{
    res.writeHead(200,{
        'Content-type':'text/html'
    })
    res.send();
})

server.listen('8080');
console.log('Server is running at port 8080');

// 监听 socket 连接
socket.listen(server).on('connnection',function(client){
    // 接受信息
    client.on('message',function(msg){
        client.send(msg);
        console.log(msg);
    })
    
    // 断开处理
    client.on('disconnect',function(){
        console.log('client socket has closed')
    })
});
```

### 旧的整理

### Cookie  

cookie是服务器写入浏览器的一小段信息，只有同源的网页才可以共享，但是如果两个网页的一级域名相同，只是二级域名不同，浏览器允许通过设置 `document.domain` 来共享Cookie.  

>A:http://w1.example.con/a.html 
B:http://w2.example.com/b.html   

那么只要设置相同的document.domain就可以共享cookie了  

>document.domain = 'example.com'

在A网页设置一个cookie
>document.cookie = "test = hello "

B网页就可以读到这个Cookie

>var allCookie = document.cookie;

这种方式只适用于`Cookie`和`iframe`窗口，LocalStorage和IndexDB无法通过这种方法，规范同源策略要使用PostMessage API,另外在设置Cookie的时候，要指定Cookie的所属域名为一级域名，比如.example.com。

>Set-Cookie: key=value; domain=.example.com; path=/

这样的话，二级域名和三级域名不用做任何设置，都可以读取这个Cookie。

### iframe  

如果两个网页不同源，就无法拿到对方的DOM，典型的例子是iframe窗口和window.open方法打开的窗口，它们与父窗口没有办法通信。

>document.getElementById("myIFrame").contentWindow.document
// Uncaught DOMException: Blocked a frame from accessing a cross-origin frame.

上面命令中，父窗口想获取子窗口的DOM，因为跨源导致报错。
反之亦然，子窗口获取主窗口的DOM也会报错。

>window.parent.document.body
// 报错

如果两个窗口一级域名相同，只是二级域名不同，那么设置上一节介绍的document.domain属性，就可以规避同源政策，拿到DOM。  
对于完全不同源的网站，目前有三种方法，可以解决跨域窗口的通信问题。  
（1）片段识别符（fragment identifier）  
（2）window.name  
（3）跨文档通信API（Cross-document messaging）  

2.1 片段识别符  
片段标识符指的是，URL的`#号`后面的部分，比如    
>http://example.com/x.html#fragment的 `#fragment`

父窗口可以把信息，写入子窗口的片段标识符。

>var src = originURL + `'#'` + data;
document.getElementById('myIFrame').src = src;

子窗口通过监听`hashchange`事件得到通知。

>window.onhashchange = checkMessage;
>function checkMessage() {
  var message = window.location.hash;
  // ...
}

同样的，子窗口也可以改变父窗口的片段标识符。

>parent.location.href= target + "#" + hash;

2.2 window.name  
浏览器窗口有`window.name`属性。这个属性的最大特点是，无论是否同源，只要在同一个窗口里，前一个网页设置了这个属性，后一个网页可以读取它。父窗口先打开一个子窗口，载入一个不同源的网页，该网页将信息写入`window.name`属性。  

>window.name = data;

接着，子窗口跳回与主窗口同域的网址

>location = 'http://parent.url.com/xxx.html';

然后，主窗口就可以读取子窗口的`window.name`了。

>var data = document.getElementById('myFrame').contentWindow.name;

这种方法的优点是，`window.name`容量很大，可以放置非常长的字符串；缺点是必须监听子窗口window.name属性的变化，影响网页性能。  

2.3 window.postMessage  
跨文档通信API（Cross-documentmessaging）  
这个API为`window`对象新增了一个`window.postMessage`方法，允许跨窗口通信，不论这两个窗口是否同源。举例来说，父窗口`http://aaa.com`向子窗口`http://bbb.com发`消息，调用`postMessage`方法就可以了。  

>var popup = window.open('http://bbb.com', 'title');
popup.postMessage('Hello World!', 'http://bbb.com');

postMessage 方法的第一个参数是具体的信息内容，第二个参数是接受信息的窗口的源（origin）,即“`协议+域名+端口`”,也可以设为`“*”`,表示不限制域名，向所有窗口发送。  
子窗口向父窗口发送信息的写法类似。  

>window.opener.postMessage('hello','http://aa.com');

父窗口或者是子窗口都可以通过`message `事件，监听对方的信息

>window.addEvenListener('message',function(e){
	console.log(e.data);
},false);

`message` 事件的事件对象 `event` 提供以下三个属性  
event.source 发送信息的窗口  
event.origin 消息发向的网站  
event.data 消息内容  
下面的例子是，子窗口通过`event.source`属性引用父窗口，然后发送消息。

```javascript
window.addEventListener('message', receiveMessage);  
function receiveMessage(event) {  
    event.source.postMessage('Nice to see you!', '*');  
}
```

`event.origin`属性可以过滤不是发给本窗口的消息。

```javascript
window.addEventListener('message', receiveMessage);   
function receiveMessage(event) {  
if (event.origin !== 'http://aaa.com') return;  
if (event.data === 'Hello World') {  
    event.source.postMessage('Hello', event.origin);  
} else {  
    console.log(event.data);  
}
}
```

### LocalStorage  

通过`window.postMessage`，读写其他窗口的 LocalStorage 也成为了可能。
下面是一个例子，主窗口写入iframe子窗口的localStorage。  

```javascript
window.onmessage = function(e) {
if (e.origin !== 'http://bbb.com') {
    return;
}
var payload = JSON.parse(e.data);
localStorage.setItem(payload.key, JSON.stringify(payload.data));
};
```

上面代码中，子窗口将父窗口发来的消息，写入自己的`LocalStorage`。

父窗口发送消息的代码如下。  

```javascript
var win = document.getElementsByTagName('iframe')[0].contentWindow;
var obj = { name: 'Jack' };
win.postMessage(JSON.stringify({key: 'storage', data: obj}), 'http://bbb.com');
```

加强版的子窗口接收消息的代码如下。  

```javascript
window.onmessage = function(e) {
if (e.origin !== 'http://bbb.com') return;
var payload = JSON.parse(e.data);
switch (payload.method) {
    case 'set':
    localStorage.setItem(payload.key, JSON.stringify(payload.data));
    break;
    case 'get':
    var parent = window.parent;
    var data = localStorage.getItem(payload.key);
    parent.postMessage(data, 'http://aaa.com');
    break;
    case 'remove':
    localStorage.removeItem(payload.key);
    break;
}
}; 
```

加强版的父窗口发送消息代码如下。  

```javascript
var win = document.getElementsByTagName('iframe')[0].contentWindow;
var obj = { name: 'Jack' };
// 存入对象
win.postMessage(JSON.stringify({key: 'storage', method: 'set', data: obj}), 'http://bbb.com');
// 读取对象
win.postMessage(JSON.stringify({key: 'storage', method: "get"}), "*");
window.onmessage = function(e) {
if (e.origin != 'http://aaa.com') return;
// "Jack"
console.log(JSON.parse(e.data).name);
};
```

### AJAX  

同源政策规定，AJAX请求只能发给同源的网址，否则就报错。 
除了架设服务器代理（浏览器请求同源服务器，再由后者请求外部服务），有三种方法规避这个限制。  

4.1 JSONP WebSocket CORS  
JSONP是服务器与客户端跨源通信的常用方法。最大特点就是简单适用，老式浏览器全部支持，服务器改造非常小。

它的基本思想是，网页通过添加一个`<script>`元素，向服务器请求JSON数据，这种做法不受同源政策限制；服务器收到请求后，将数据放在一个指定名字的回调函数里传回来。

首先，网页动态插入`<script>`元素，由它向跨源网址发出请求。

```javascript
function addScriptTag(src) {
var script = document.createElement('script');
script.setAttribute("type","text/javascript");
script.src = src;
document.body.appendChild(script);
}

window.onload = function () {
addScriptTag('http://example.com/ip?callback=foo');
}

function foo(data) {
console.log('Your public IP address is: ' + data.ip);
};
```

上面代码通过动态添加`<script>`元素，向服务器example.com发出请求。注意，该请求的查询字符串有一个callback参数，用来指定回调函数的名字，这对于JSONP是必需的。
服务器收到这个请求以后，会将数据放在回调函数的参数位置返回。

```javascript
foo({
"ip": "8.8.8.8"
});
```

由于`<script>`元素请求的脚本，直接作为代码运行。这时，只要浏览器定义了foo函数，该函数就会立即调用。作为参数的JSON数据被视为JavaScript对象，而不是字符串，因此避免了使用JSON.parse的步骤。  

4.2 WebSocket  
WebSocket是一种通信协议，使用ws://（非加密）和wss://（加密）作为协议前缀。该协议不实行同源政策，只要服务器支持，就可以通过它进行跨源通信。 

下面是一个例子，浏览器发出的WebSocket请求的头信息（摘自维基百科）。
>GET /chat HTTP/1.1  
Host: server.example.com  
Upgrade: websocket  
Connection: Upgrade  
Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==  
Sec-WebSocket-Protocol: chat, superchat  
Sec-WebSocket-Version: 13  
Origin: http://example.com  

上面代码中，有一个字段是Origin，表示该请求的请求源（origin），即发自哪个域名。  
正是因为有了Origin这个字段，所以WebSocket才没有实行同源政策。因为服务器可以根据这个字段，判断是否许可本次通信。如果该域名在白名单内，服务器就会做出如下回应。  

>HTTP/1.1 101 Switching Protocols  
Upgrade: websocket  
Connection: Upgrade  
Sec-WebSocket-Accept: HSmrc0sMlYUkAGmm5OPpG2HaGWk=  
Sec-WebSocket-Protocol: chat  

4.3 CORS  
CORS是跨源资源分享（Cross-Origin Resource Sharing）的缩写。它是W3C标准，是跨源AJAX请求的根本解决方法。相比JSONP只能发GET请求，CORS允许任何类型的请求。

