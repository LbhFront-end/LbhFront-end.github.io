---
title: 深入浅出HTTP，从开始到放弃（第九章）—— 基于 HTTP 的功能追加协议
date: 2018-09-15 11:30:54
tags: http
categories: 
	- http
---









## 第九章  基于 HTTP 的功能追加协议

### 基于 HTTP 的协议

随着时代的发展，Web 的用途更具多样性，比如演化成在线购物网站、SNS（Social Networking Service，社交网络服务）、企业或组织内部的各种管理工具，等等。

而这些网站所追求的功能可通过 Web 应用和脚本程序实现。即使这些功能已经满足需求，在性能上却未必最优，这是因为 HTTP 协议上的限制以及自身性能有限。

HTTP 功能上的不足可通过创建一套全新的协议来弥补。可是目前基于 HTTP 的 Web 浏览器的使用环境已遍布全球，因此无法完全抛弃HTTP。有一些新协议的规则是基于 HTTP 的，并在此基础上添加了新的功能。



### 消除 HTTP 瓶颈的 SPDY（现如今逐渐被淘汰）

Google 在 2010 年发布了 SPDY（取自 SPeeDY，发音同 speedy），其开发目标旨在解决 HTTP 的性能瓶颈，缩短 Web 页面的加载时间（50%）。

- SPDY - The Chromium Projects

  http://www.chromium.org/spdy/

#### HTTP 的瓶颈

使用 HTTP 协议探知服务器上是否有内容更新，就必须频繁地从客户端到服务器端进行确认。如果服务器上没有内容更新，那么就会产生徒劳的通信。

若想在现有 Web 实现所需的功能，以下这些 HTTP 标准就会成为瓶颈。

- 一条连接上只能发送一个请求
- 请求只能从客户端开始。客户端不可以接受除响应以外的指令
- 请求/ 响应 首部未经压缩就发送，首部消息越多延迟越大。
- 发送冗长的首部，每次互相发送相同的首部造成的浪费较多。
- 可任意选择数据压缩格式，非强制压缩发送。

![以前的HTTP通信](/images/2018-09-15-read-图解HTTP-Part9-以前的HTTP通信.png)

##### Ajax 的解决方法

Ajax（Asynchronous JavaScript and XML，异步 JavaScript 与 XML 技术）是一种有效利用 JavaScript 和 DOM （Document Object Model，文档对象模型）的操作，以达到局部 Web 页面替换加载的异步通信手段。和以前的通信相比，由于它只更新一部分页面，响应中传输的数据量会因此减少，这一优点是显而易见的。

Ajax 的核心技术是名为 XMLHttpRequest 的 API，通过 JavaScript 脚本语言的调用就能和服务器进行 HTTP 通信。借由这种手段，就能从已加载完毕的 Web 页面上发起请求，只更新局部页面。

而利用 Ajax 实时地从服务器获取内容，有可能会导致大量请求产生。另外，Ajax 仍未解决 

HTTP 协议本身存在的问题。

![Ajax 通信](/images/2018-09-15-read-图解HTTP-Part9-Ajax 通信.png)

##### Comet 的解决方法

一旦服务器端有内容更新，Comet 不会让请求等待，而是直接给客户端返回响应。这是一种通过延迟应答，模拟实现服务器端向客户端推送（Server Push）的功能。

通常，服务器端接收到请求，在处理完毕后就会立即返回响应，但为了实现推送功能，Comet 会先将响应置于挂起状态，当服务器端有内容更新时，再返回该响应。因此，服务器端一旦有更新，就可以立即反馈给客户端。

内容上虽然可以做到实时更新，但为了保留响应，一次连接的持续时间也变长了。期间，为了维持连接会消耗更多的资源。另外，Comet也仍未解决 HTTP 协议本身存在的问题。

![Comet 通信](/images/2018-09-15-read-图解HTTP-Part9-Comet 通信.png)

##### SPDY 的目标

陆续出现的 Ajax 和 Comet 等提高易用性的技术，一定程度上使 HTTP得到了改善，但 HTTP 协议本身的限制也令人有些束手无策。为了进行根本性的改善，需要有一些协议层面上的改动。
处于持续开发状态中的 SPDY 协议，正是为了在协议级别消除 HTTP所遭遇的瓶颈。

（Google宣布计划淘汰该公司在2009年推出的[SPDY协议](http://www.williamlong.info/archives/3119.html)，SPDY原本定位为替代HTTP协议的新协议，Google原本打算以它来加速HTTP的传输速度并推动成为标准，不过现在决定将支持HTTP/2，并逐渐淘汰SPDY）

#### SPDY 的设计与功能

SPDY 没有完全改写 HTTP 协议，而是在 TCP/IP 的应用层与运输层之间通过新加会话层的形式运作。同时，考虑到安全性问题，SPDY 规定通信中使用 SSL。

SPDY 以会话层的形式加入，控制对数据的流动，但还是采用 HTTP建立通信连接。因此，可照常使用 HTTP 的 GET 和 POST 等方 法、Cookie 以及 HTTP 报文等。

![SPDY 的设计](/images/2018-09-15-read-图解HTTP-Part9-SPDY 的设计.png)

使用 SPDY 后，HTTP 协议额外获得以下功能。

##### 多路复用流

通过单一的 TCP 连接，可以无限制处理多个 HTTP 请求。所有请求的处理都在一条 TCP 连接上完成，因此 TCP 的处理效率得到提高

##### 赋予请求优先级

SPDY 不仅可以无限制地并发处理请求，还可以给请求逐个分配优先级顺序。这样主要是为了在发送多个请求时，解决因带宽低而导致响应变慢的问题。

##### 压缩 HTTP 首部

压缩 HTTP 请求和响应的首部。这样一来，通信产生的数据包数量和发送的字节数就更少了。

##### 推送功能

支持服务器主动向客户端推送数据的功能。这样，服务器可直接发送数据，而不必等待客户端的请求。

##### 服务器提示功能

服务器可以主动提示客户端请求所需的资源。由于在客户端发现资源之前就可以获知资源的存在，因此在资源已缓存等情况下，可以避免发送不必要的请求。

#### SPDY 消除 Web 瓶颈了吗

SPDY 基本上只是将单个域名（ IP 地址）的通信多路复用，所以当一个 Web 网站上使用多个域名下的资源，改善效果就会受到限制。

SPDY 的确是一种可有效消除 HTTP 瓶颈的技术，但很多 Web 网站存在的问题并非仅仅是由 HTTP 瓶颈所导致。对 Web 本身的速度提升，还应该从其他可细致钻研的地方入手，比如改善 Web 内容的编写方式等。



### 使用浏览器进行全双工通信的WebSocket

利用 Ajax 和 Comet 技术进行通信可以提升 Web 的浏览速度。但问题在于通信若使用 HTTP 协议，就无法彻底解决瓶颈问题。WebSocket网络技术正是为解决这些问题而实现的一套新协议及 API。

当时筹划将 WebSocket 作为 HTML5 标准的一部分，而现在它却逐渐变成了独立的协议标准。WebSocket 通信协议在 2011 年 12 月 11 日，被 RFC 6455 - The WebSocket Protocol 定为标准。

#### WebSocket 的设计与功能

WebSocket，即 Web 浏览器与 Web 服务器之间全双工通信标准。其中，WebSocket 协议由 IETF 定为标准，WebSocket API 由 W3C 定为标准。仍在开发中的 WebSocket 技术主要是为了解决 Ajax 和 Comet里 XMLHttpRequest 附带的缺陷所引起的问题。

#### WebSocket 协议

一旦 Web 服务器与客户端之间建立起 WebSocket 协议的通信连接，之后所有的通信都依靠这个专用协议进行。通信过程中可互相发送JSON、XML、HTML 或图片等任意格式的数据。

由于是建立在 HTTP 基础上的协议，因此连接的发起方仍是客户端，而一旦确立 WebSocket 通信连接，不论服务器还是客户端，任意一方都可直接向对方发送报文。

##### WebSocket 协议的主要特点。

推送功能

支持由服务器向客户端推送数据的推送功能。这样，服务器可直接发送数据，而不必等待客户端的请求。

减少通信量

只要建立起 WebSocket 连接，就希望一直保持连接状态。和 HTTP 相比，不但每次连接时的总开销减少，而且由于 WebSocket 的首部信息很小，通信量也相应减少了。

为了实现 WebSocket 通信，在 HTTP 连接建立之后，需要完成一次“握手”（Handshaking）的步骤。

握手·请求

为了实现 WebSocket 通信，需要用到 HTTP 的 Upgrade 首部字段，告知服务器通信协议发生改变，以达到握手的目的。

```http
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Origin: http://example.com
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
```

Sec-WebSocket-Key 字段内记录着握手过程中必不可少的键值。
Sec-WebSocket-Protocol 字段内记录使用的子协议。

子协议按 WebSocket 协议标准在连接分开使用时，定义那些连接的名称。

握手·响应

对于之前的请求，返回状态码 101 Switching Protocols 的响应。

```http
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
Sec-WebSocket-Protocol: chat
```

Sec-WebSocket-Accept 的字段值是由握手请求中的 Sec-WebSocket-Key 的字段值生成的。

成功握手确立 WebSocket 连接之后，通信时不再使用 HTTP 的数据帧，而采用 WebSocket 独立的数据帧。

![WebSocket 通信](/images/2018-09-15-read-图解HTTP-Part9-WebSocket 通信.png)

WebSocket API

JavaScript 可调用“The WebSocketAPI”（http://www.w3.org/TR/websockets/，由 W3C 标准制定）内提供的 WebSocket 程序接口，以实现 WebSocket 协议下全双工通信。
以下为调用 WebSocket API，每 50ms 发送一次数据的实例。

```javascript
var socket = new WebSocket('ws://game.example.com:12010/updates');
socket.onopen = function () {
    setInterval(function() {
        if (socket.bufferedAmount == 0)
            socket.send(getUpdateData());
        }, 50);
};
```

### 期盼已久的 HTTP/2.0

HTTP/2 （原名HTTP/2.0）即[超文本](https://baike.baidu.com/item/%E8%B6%85%E6%96%87%E6%9C%AC)[传输协议](https://baike.baidu.com/item/%E4%BC%A0%E8%BE%93%E5%8D%8F%E8%AE%AE) 2.0，是下一代[HTTP协议](https://baike.baidu.com/item/HTTP%E5%8D%8F%E8%AE%AE)。是由[互联网工程任务组](https://baike.baidu.com/item/%E4%BA%92%E8%81%94%E7%BD%91%E5%B7%A5%E7%A8%8B%E4%BB%BB%E5%8A%A1%E7%BB%84/707674)（[IETF](https://baike.baidu.com/item/IETF)）的Hypertext Transfer Protocol Bis (httpbis)工作小组进行开发。是自1999年[http](https://baike.baidu.com/item/http)1.1发布后的首个更新。HTTP 2.0在2013年8月进行首次合作共事性测试。在开放互联网上HTTP 2.0将只用于https://网址，而 http://网址将继续使用HTTP/1，目的是在开放互联网上增加使用加密技术，以提供强有力的保护去遏制主动攻击。DANE RFC6698允许[域名](https://baike.baidu.com/item/%E5%9F%9F%E5%90%8D/86062)管理员不通过第三方[CA](https://baike.baidu.com/item/CA)自行发行证书。（摘自百度百科）

[HTTP/2.0英文文档](https://httpwg.org/specs/rfc7540.html)

### Web 服务器管理文件的 WebDAV

WebDAV（Web-based Distributed Authoring and Versioning，基于万维网的分布式创作和版本控制）是一个可对 Web 服务器上的内容直接进行文件复制、编辑等操作的分布式文件系统。它作为扩展 HTTP/1.1的协议定义在 RFC4918。
除了创建、删除文件等基本功能，它还具备文件创建者管理、文件编辑过程中禁止其他用户内容覆盖的加锁功能，以及对文件内容修改的版本控制功能。

![WebDAV](/images/2018-09-15-read-图解HTTP-Part9-Ajax WebDAV.png)

使用 HTTP/1.1 的 PUT 方法和 DELETE 方法，就可以对 Web 服务器上的文件进行创建和删除操作。可是出于安全性及便捷性等考虑，一般不使用。

#### 扩展 HTTP/1.1 的 WebDAV

针对服务器上的资源，WebDAV 新增加了一些概念，如下所示。

![WebDAV 扩展的概念](/images/2018-09-15-read-图解HTTP-Part9-WebDAV 扩展的概念.png)

- 集合（Collection）：是一种统一管理多个资源的概念。以集合为单位可进行各种操作。也可实现类似集合的集合这样的叠加。
- 资源（Resource）：把文件或集合称为资源。
- 属性（Property）：定义资源的属性。定义以“名称 = 值”的格式执行。
- 锁（Lock）：把文件设置成无法编辑状态。多人同时编辑时，可防止在同一时间进行内容写入。

#### WebDAV 内新增的方法及状态码

WebDAV 为实现远程文件管理，向 HTTP/1.1 中追加了以下这些方法。

PROPFIND ：获取属性
PROPPATCH ：修改属性
MKCOL ：创建集合
COPY ：复制资源及属性
MOVE ：移动资源
LOCK ：资源加锁
UNLOCK ：资源解锁
为配合扩展的方法，状态码也随之扩展。
102 Processing ：可正常处理请求，但目前是处理中状态
207 Multi-Status ：存在多种状态
422 Unprocessible Entity ：格式正确，内容有误
423 Locked ：资源已被加锁
424 Failed Dependency ：处理与某请求关联的请求失败，因此不再维持依赖关系

507 Insufficient Storage ：保存空间不足

#### WebDAV 的请求实例

下面是使用 PROPFIND 方法对 http://www.example.com/file 发起获取属性的请求。

```http
PROPFIND /file HTTP/1.1
Host: www.example.com
Content-Type: application/xml; charset="utf-8"
Content-Length: 219
<?xml version="1.0" encoding="utf-8" ?>
<D:propfind xmlns:D="DAV:">
    <D:prop xmlns:R="http://ns.example.com/boxschema/">
    <R:bigbox/>
    <R:author/>
    <R:DingALing/>
    <R:Random/>
    </D:prop>
</D:propfind>
```

WebDAV 的响应实例

下面是针对之前的 PROPFIND 方法，返回http://www.example.com/file 的属性的响应。

```http
HTTP/1.1 207 Multi-Status
Content-Type: application/xml; charset="utf-8"
Content-Length: 831
<?xml version="1.0" encoding="utf-8" ?>
<D:multistatus xmlns:D="DAV:">
    <D:response xmlns:R="http://ns.example.com/boxschema/">
        <D:href>http://www.example.com/file</D:href>
        <D:propstat>
        <D:prop>
            <R:bigbox>
            <R:BoxType>Box type A</R:BoxType>
            </R:bigbox>
            <R:author>
			<R:Name>J.J. Johnson</R:Name>
			</R:author>
		</D:prop>
	<D:status>HTTP/1.1 200 OK</D:status>
    </D:propstat>
    <D:propstat>
        <D:prop><R:DingALing/><R:Random/></D:prop>
        <D:status>HTTP/1.1 403 Forbidden</D:status>
        <D:responsedescription> The user does not have access to the DingALing property.
        </D:responsedescription>
     </D:propstat>
	</D:response>
	<D:responsedescription> There has been an access violation error.
	</D:responsedescription>
</D:multistatus>
```

参考链接：https://book.douban.com/subject/25863515/