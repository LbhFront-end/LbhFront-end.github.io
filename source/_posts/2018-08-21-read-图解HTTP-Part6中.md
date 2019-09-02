---
title: 深入浅出HTTP，从开始到放弃（第六章中）—— HTTP 首部
date: 2018-08-22 08:55:54
tags: http
categories: 
	- http


---



公司上个周末出去玩了，所以没有更新，现在又有项目在身上，所以更新的比较慢。



## 第六章  HTTP 首部（中）

### 请求首部字段

请求首部字段是从客户端往服务器端发送请求报文中所使用的字段，用于补充请求的附加消息、客户端消息、对响应内容相关的优先级等内容。

#### Accept

![Accept](/images/2018-08-17-read-图解HTTP-Part6-Accept.png)

```http
Accept: text/html,application/xhtml+xml, application/xml;q=0.9,*/*;q=0
```

Accept 首部字段可通知服务器，用户代理能够处理的媒体类型及媒体类型的相对优先级。可使用 type/subtype 这种形式，一次指定多种媒体类型。

| 媒体类型                 | 例子                                                         |
| ------------------------ | ------------------------------------------------------------ |
| 文本文件                 | text/html, text/plain, text/cs..<br>application/xhtml+xml, application/xml |
| 图片文件                 | image/jpeg, image/gif, image/png                             |
| 视频文件                 | video/mpeg, video/quicktime                                  |
| 应用程序使用的二进制文件 | application/octet-stream, application/zip                    |

比如，如果浏览器不支持 PNG 图片的显示，那 Accept 就不指定 image/png，而指定可处理的 image/gif 和 image/jpeg 等图片类型。

若想要给显示的媒体类型增加优先级，则使用 q= 来额外表示权重值，用分号（;）进行分隔。权重值 q 的范围是 0~1（可精确到小数点后 3 位），且 1 为最大值。不指定权重 q 值时，默认权重为 q=1.0。

当服务器提供多种内容时，将会首先返回权重值最高的媒体类型。

#### Accept-Charset

![Accept-Charset](/images/2018-08-17-read-图解HTTP-Part6-Accept-Charset.png)

```http
Accept-Charset: iso-8859-5, unicode-1-1;q=0.8
```

Accept-Charset 首部字段可用来通知服务器用户代理支持的字符集及字符集的相对优先顺序。另外，可一次性指定多种字符集。与首部字段 Accept 相同的是可用权重 q 值来表示相对优先级。

该首字段应用于内容协商机制的服务器驱动协商。

#### Accept-Encoding

![Accept-Encoding](/images/2018-08-17-read-图解HTTP-Part6-Accept-Encoding.png)

```http
Accept-Encoding: gzip, deflate
```

Accept-Encoding 首部字段用来告知服务器用户代理支持的内容编码及内容编码的优先级顺序。可一次性指定多种内容编码。

内容编码的例子



| 内容编码 | 例子                                                         |
| -------- | ------------------------------------------------------------ |
| gzip     | 由文件压缩程序 gzip（GUN zip）生成的编码格式（RFC1952）,采用 Lempel-Ziv 算法（LZ77）以及 32 位循环冗余校验（Cyclic Redundancy Check，通称 CRC） |
| compress | 由 UNIX 文件压缩程序 compress 生成的编码格式，采用 Lempel-Ziv-Welch 算法（LZW） |
| deflate  | 组合使用 zlib 格式（RFC1950）及由 deflate 压缩算法（RFC1951）生成的编码格式 |
| identity | 不执行压缩或不会变化的默认编码格式                           |

采用权重 q 值来表示相对优先级，这点与首部字段 Accept 相同。另外，也可使用星号（*）作为通配符，指定任意的编码格式。

#### Accept-Language

```http
Accept-Language: zh-cn,zh;q=0.7,en-us,en;q=0.3
```

首部字段 Accept-Language 用来告知服务器用户代理能够处理的自然语言集（指中文或英文等），以及自然语言集的相对优先级。可一次指定多种自然语言集。
和 Accept 首部字段一样，按权重值 q 来表示相对优先级。在上述图例中，客户端在服务器有中文版资源的情况下，会请求其返回中文版对应的响应，没有中文版时，则请求返回英文版响应。

#### Authorization

![Authorization](/images/2018-08-17-read-图解HTTP-Part6-Authorization.png)

```http
Authorization: Basic dWVub3NlbjpwYXNzd29yZA==
```

首部字段 Authorization 是用来告知服务器，用户代理的认证信息（证书值）。通常，想要通过服务器认证的用户代理会在接收到返回的401 状态码响应后，把首部字段 Authorization 加入请求中。共用缓存在接收到含有 Authorization 首部字段的请求时的操作处理会略有差异。
有关 HTTP 访问认证及 Authorization 首部字段，稍后的章节还会详细说明

#### Expect

![Expect](/images/2018-08-17-read-图解HTTP-Part6-Expect.png)

```http
Expect: 100-continue
```

客户端使用 Expect 首部字段来告知服务器，期望出现的某些特定行为。因服务器无法理解客户端的期望做出回应而发生错误时，会返回状态码 417 Expection Failed

客户端可以利用该首部字段，写明所期望的扩展。虽然 HTTP/1.1 规范只定义了 100-continue（状态码 100 Continue 之意）

等待状态码 100 响应的客户端在发生请求时，需要指定 Expect:100-continue。

#### From

![From](/images/2018-08-17-read-图解HTTP-Part6-From.png)

首部字段 From 用来告知服务器使用用户代理的用户的电子邮件地址。通常，其使用目的就是为了显示搜索引擎等用户代理的负责人的电子邮件联系方式。使用代理时，应尽可能包含 From 首部字段（但可能会因代理不同，将电子邮件地址记录在 User-Agent 首部字段内）。

#### Host

![Host](/images/2018-08-17-read-图解HTTP-Part6-Host.png)

图：虚拟主机运行在同一个 IP 上，因此使用首部字段 Host 加以区分

```http
Host: www.hackr.jp
```

首部字段 Host 会告知服务器，请求的资源所处的互联网主机名和端口号。Host 首部字段在 HTTP/1.1 规范内是唯一一个必须被包含在请求内的首部字段。
首部字段 Host 和以单台服务器分配多个域名的虚拟主机的工作机制有很密切的关联，这是首部字段 Host 必须存在的意义。

请求被发送至服务器时，请求中的主机名会用 IP 地址直接替换解决。但如果这时，相同的 IP 地址下部署运行着多个域名，那么服务器就会无法理解究竟是哪个域名对应的请求。因此，就需要使用首部字段 Host 来明确指出请求的主机名。若服务器未设定主机名，那直接发送一个空值即可。如下所示。

```http
Host:
```

#### If-Match

![If-Match](/images/2018-08-17-read-图解HTTP-Part6-If-Match.png)

形如 If-xxx 这种样式的请求首部字段，都可称为条件请求。服务器接收到附带条件的请求后，只有判断指定条件为真时，才会执行请求。

![ETag](/images/2018-08-17-read-图解HTTP-Part6-ETag.png)

图：只有当 If-Match 的字段值跟 ETag 值匹配一致时，服务器才会接受请求

```http
If-Match: "123456"
```

首部字段 If-Match，属附带条件之一，它会告知服务器匹配资源所用的实体标记（ETag）值。这时的服务器无法使用弱 ETag 值。（请参照本章有关首部字段 ETag 的说明）
服务器会比对 If-Match 的字段值和资源的 ETag 值，仅当两者一致时，才会执行请求。反之，则返回状态码 412 Precondition Failed 的响应。
还可以使用星号（*）指定 If-Match 的字段值。针对这种情况，服务器将会忽略 ETag 的值，只要资源存在就处理请求。

#### If-Modified-Since

![If-Modified-Since](/images/2018-08-17-read-图解HTTP-Part6-If-Modified-Since.png)

图：如果在 If-Modified-Since 字段指定的日期时间后，资源发生了更新，服务器会接受请求

```http
If-Modified-Since: Thu, 15 Apr 2004 00:00:00 GMT
```

首部字段 If-Modified-Since，属附带条件之一，它会告知服务器若 If-Modified-Since 字段值早于资源的更新时间，则希望能处理该请求。而在指定 If-Modified-Since 字段值的日期时间之后，如果请求的资源都没有过更新，则返回状态码 304 Not Modified 的响应。
If-Modified-Since 用于确认代理或客户端拥有的本地资源的有效性。获取资源的更新日期时间，可通过确认首部字段 Last-Modified 来确定。

#### If-None-Match

![If-None-Match](/images/2018-08-17-read-图解HTTP-Part6-If-None-Match.png)

图：只有在 If-None-Match 的字段值与 ETag 值不一致时，可处理该请求。与 If-Match 首部字段的作用相反

首部字段 If-None-Match 属于附带条件之一。它和首部字段 If-Match作用相反。用于指定 If-None-Match 字段值的实体标记（ETag）值与请求资源的 ETag 不一致时，它就告知服务器处理该请求。

在 GET 或 HEAD 方法中使用首部字段 If-None-Match 可获取最新的资源。因此，这与使用首部字段 If-Modified-Since 时有些类似。

#### If-Range

![If-Range](/images/2018-08-17-read-图解HTTP-Part6-If-Range.png)

首部字段 If-Range 属于附带条件之一。它告知服务器若指定的 If-Range 字段值（ETag 值或者时间）和请求资源的 ETag 值或时间相一致时，则作为范围请求处理。反之，则返回全体资源。

![No-If-Range](/images/2018-08-17-read-图解HTTP-Part6-no-If-Range.png)

下面我们思考一下不使用首部字段 If-Range 发送请求的情况。服务器端的资源如果更新，那客户端持有资源中的一部分也会随之无效，当然，范围请求作为前提是无效的。这时，服务器会暂且以状态码 412Precondition Failed 作为响应返回，其目的是催促客户端再次发送请求。这样一来，与使用首部字段 If-Range 比起来，就需要花费两倍的功夫。

#### If-Unmodified-Since

```http
If-Unmodified-Since: Thu, 03 Jul 2012 00:00:00 GMT
```

首部字段 If-Unmodified-Since 和首部字段 If-Modified-Since 的作用相反。它的作用的是告知服务器，指定的请求资源只有在字段值内指定的日期时间之后，未发生更新的情况下，才能处理请求。如果在指定日期时间后发生了更新，则以状态码 412 Precondition Failed 作为响应返回。

#### Max-Forwards

![Max-Forwards](/images/2018-08-17-read-图解HTTP-Part6-Max-Forwards.png)

图：每次转发数值减 1。当数值变 0 时返回响应

通过 TRACE 方法或 OPTIONS 方法，发送包含首部字段 Max-Forwards 的请求时，该字段以十进制整数形式指定可经过的服务器最大数目。服务器在往下一个服务器转发请求之前，Max-Forwards 的值减 1 后重新赋值。当服务器接收到 Max-Forwards 值为 0 的请求时，则不再进行转发，而是直接返回响应。

使用 HTTP 协议通信时，请求可能会经过代理等多台服务器。途中，如果代理服务器由于某些原因导致请求转发失败，客户端也就等不到服务器返回的响应了。对此，我们无从可知。

可以灵活使用首部字段 Max-Forwards，针对以上问题产生的原因展开调查。由于当 Max-Forwards 字段值为 0 时，服务器就会立即返回响应，由此我们至少可以对以那台服务器为终点的传输路径的通信状况有所把握。

#### Proxy-Authorization

```http
Proxy-Authorization: Basic dGlwOjkpNLAGfFY5
```

接收到从代理服务器发来的认证质询时，客户端会发送包含首部字段Proxy-Authorization 的请求，以告知服务器认证所需要的信息。这个行为是与客户端和服务器之间的 HTTP 访问认证相类似的，不同之处在于，认证行为发生在客户端与代理之间。客户端与服务器之间的认证，使用首部字段 Authorization 可起到相同作用。有关 HTTP 访问认证，后面的章节会作详尽阐述。

#### Range

```http
Range: bytes=5001-10000
```

对于只需获取部分资源的范围请求，包含首部字段 Range 即可告知服务器资源的指定范围。上面的示例表示请求获取从第 5001 字节至第10000 字节的资源。

接收到附带 Range 首部字段请求的服务器，会在处理请求之后返回状态码为 206 Partial Content 的响应。无法处理该范围请求时，则会返回状态码 200 OK 的响应及全部资源。

#### Referer

![Referer](/images/2018-08-17-read-图解HTTP-Part6-Referer.png)

```http
Referer: http://www.hackr.jp/index.htm
```

首部字段 Referer 会告知服务器请求的原始资源的 URI。

客户端一般都会发送 Referer 首部字段给服务器。但当直接在浏览器的地址栏输入 URI，或出于安全性的考虑时，也可以不发送该首部字段。

因为原始资源的 URI 中的查询字符串可能含有 ID 和密码等保密信息，要是写进 Referer 转发给其他服务器，则有可能导致保密信息的泄露。

另外，Referer 的正确的拼写应该是 Referrer，但不知为何，大家一直沿用这个错误的拼写。

#### TE

```http
TE: gzip, deflate;q=0.5
```

首部字段 TE 会告知服务器客户端能够处理响应的传输编码方式及相对优先级。它和首部字段 Accept-Encoding 的功能很相像，但是用于传输编码。

首部字段 TE 除指定传输编码之外，还可以指定伴随 trailer 字段的分块传输编码的方式。应用后者时，只需把 trailers 赋值给该字段值。

```http
TE: trailers
```

#### User-Agent

```http
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64; rv:13.0) Gecko/201001
```

首部字段 User-Agent 会将创建请求的浏览器和用户代理名称等信息传达给服务器。
由网络爬虫发起请求时，有可能会在字段内添加爬虫作者的电子邮件地址。此外，如果请求经过代理，那么中间也很可能被添加上代理服务器的名称。

### 响应首部字段

响应首部字段是由服务器端向客户端返回响应报文中所使用的字段，用于补充响应的附加消息、服务器消息，以及对客户端的附加要求等消息。

#### Accept-Ranges

```http
Accept-Ranges: bytes 
```

首部字段 Accept-Ranges 是用来告知客户端服务器是否能处理范围请求，以指定获取服务器端某个部分的资源。

可指定的字段值有两种，可处理范围请求时指定其为 bytes ，反之则指定其为 none。

#### Age

![Age](/images/2018-08-17-read-图解HTTP-Part6-Age.png)

```http
Age: 600
```

首部字段 Age 能告知客户端，源服务器在多久前创建了响应。字段值的单位为秒。

若创建该响应的服务器是缓存服务器，Age 值是指缓存后的响应再次发起认证到认证完成的时间值。代理创建响应时必须加上首部字段 Age。

#### ETag

![ETag](/images/2018-08-17-read-图解HTTP-Part6-ETag1.png)

```http
ETag: "82e22293907ce725faf67773957acd12"
```

首部字段 ETag 告知客户端实体标识。它是一种可将资源以字符串形式做唯一性标识的告知方式。服务器会以没份资源分配对应的 ETag 值。另外，当资源更新时，ETag 值也需要更新。生成 ETag 值时，并没有统一的算法规则，而仅仅是由服务器来分配。

资源被缓存时，就会被分配唯一性标识。例如，当使用中文版的浏览器访问 http://www.google.com/ 时，就会返回中文版对应的资源，而使用英文版的浏览器访问时，则会返回英文版对应的资源。两者的URI 是相同的，所以仅凭 URI 指定缓存的资源是相当困难的。若在下载过程中出现连接中断、再连接的情况，都会依照 ETag 值来指定资源。

##### 强 ETag 值和弱 Tag 值

ETag  中有强 ETag  值和弱 ETag  值之分。

强 ETag  值，不论实体发生多么细微的变化都会改变其值。

```http
ETag: "usagi-1234"
```

弱 ETag  值，只用于提示资源是否相同。只有当资源发生了根本的变化，产生差异才会改变 ETag  值。这时，会在字段值最开始处附加 W/。

```http
ETag: W/"usagi-1234"
```

#### Location

![Location](/images/2018-08-17-read-图解HTTP-Part6-Location.png)

```http
Location: http://www.usagidesign.jp/sample.html
```

使用首部字段 Location 可以将响应接收方引导至某个与请求 URI 位置不同的资源。

基本上，该字段会配合 3xx ：Redirection 的响应，提供重定向的URI。

几乎所有的浏览器在接收到包含首部字段 Location 的响应后，都会强制性地尝试对已提示的重定向资源的访问。

#### Proxy-Authenticate

```http
Proxy-Authenticate: Basic realm="Usagidesign Auth"
```

首部字段 Proxy-Authenticate 会把由代理服务器所要求的认证信息发送给客户端。

它与客户端和服务器之间的 HTTP 访问认证的行为相似，不同之处在与其认证行为是在客户端与代理之间进行的，而客户端与服务器之间进行认证时，首部字段 WWW-Authorization 有着相同的作用。

#### Retry-After

```http
Retry-After: 120
```

首部字段 Retry-After 告知客户端应该在多久之后再次发送请求。主要配合状态码 503 Service Unavailable 响应，或 3xx Redirect 响应一起使用。

字段值可以指定为具体的日期时间（Wed, 04 Jul 2012 06：34：24GMT 等格式），也可以是创建响应后的秒数。

#### Server

![Server](/images/2018-08-17-read-图解HTTP-Part6-Server.png)

```http
Server: Apache/2.2.17 (Unix)
```

首部字段 Server 告知客户端当前服务器上安装的 HTTP 服务器应用程序的信息。不单单会标出服务器上的软件应用名称，还有可能包括版本号和安装时启用的可选项。

```http
Server: Apache/2.2.6 (Unix) PHP/5.2.5
```

#### Vary

![Vary](/images/2018-08-17-read-图解HTTP-Part6-Vary.png)

图：当代理服务器接收到带有 Vary 首部字段指定获取资源的请求时，如果使用的 Accept-Language 字段的值相同，那么就直接从缓存返回响应。反之，则需要先从源服务器端获取资源后才能作为响应返回

```http
Vary: Accept-Language
```

首部字段 Vary 可对缓存进行控制。源服务器会向代理服务器传达关于本地缓存使用方法的命令。

从代理服务器接收到源服务器返回包含 Vary 指定项的响应之后，若再要进行缓存，仅对请求中含有相同 Vary 指定首部字段的请求返回缓存。即使对相同资源发起请求，但由于 Vary 指定的首部字段不相同，因此必须要从源服务器重新获取资源。

#### WWW-Authenticate

```http
WWW-Authenticate: Basic realm="Usagidesign Auth"
```

首部字段 WWW-Authenticate 用于 HTTP 访问认证。它会告知客户端适用于访问请求 URI 所指定资源的认证方案（Basic 或是 Digest）和带参数提示的质询（challenge）。状态码 401 Unauthorized 响应中，肯定带有首部字段 WWW-Authenticate。

上述示例中，realm 字段的字符串是为了辨别请求 URI 指定资源所受到的保护策略。有关该首部，请参阅本章之后的内容。





太长了，内容琐碎，看来要再分一小节了，请求首部字段与响应首部字段，耐心看完后是不是有豁然开朗的喜悦(￣▽￣)~*



参考链接：https://book.douban.com/subject/25863515/