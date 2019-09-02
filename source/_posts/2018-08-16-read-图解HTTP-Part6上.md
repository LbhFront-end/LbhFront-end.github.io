---
title: 深入浅出HTTP，从开始到放弃（第六章上）—— HTTP 首部
date: 2018-08-17 17:20:54
tags: http
categories: 
	- http

---



内容太多了，分成两节记录。



## 第六章  HTTP 首部（上）

HTTP协议的请求和响应报文中必定包含HTTP首部，只是我们在使用Web的过程中感受不到它。

### HTTP报文首部

![HTTP报文首部](/images/2018-08-17-read-图解HTTP-Part6-HTTP 报文的结构.png)

HTTP报文协议的请求和响应报文中必定包含HTTP首部。首部内容为客户端和服务器分别处理请求和响应提供所需要的消息。对于客户端用户来说，这些信息中大部分内容都无须亲自查看。

#### HTTP 请求报文

在请求中，HTTP报文由方法、URI、HTTP版本、HTTP首部字段等部分构成。

![HTTP请求报文](/images/2018-08-17-read-图解HTTP-Part6-HTTP 报文的结构.png)

访问  http://hackr.jp  时，请求报文的首部消息

```http
GET / HTTP/1.1
Host: hackr.jp
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64; rv:13.0) Gecko/20100101 Firefox/13.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*; q=0.8
Accept-Language: ja,en-us;q=0.7,en;q=0.3
Accept-Encoding: gzip, deflate
DNT: 1
Connection: keep-alive
If-Modified-Since: Fri, 31 Aug 2007 02:02:20 GMT
If-None-Match: "45bae1-16a-46d776ac"
Cache-Control: max-age=0
```

#### HTTP 响应报文

在响应中，HTTP报文由HTTP版本、状态码（数字和原因短语）、HTTP首部字段3部分构成。

![HTTP响应报文](/images/2018-08-17-read-图解HTTP-Part6-响应报文.png)

请求访问 http://hackr.jp 时，返回的响应报文的首部消息。

```http
HTTP/1.1 304 Not Modified
Date: Thu, 07 Jun 2012 07:21:36 GMT
Server: Apache
Connection: close
Etag: "45bae1-16a-46d776ac"
```

HTTP中首部字段同时存在于请求和响应报文内，并涵盖HTTP报文相关的内容消息。



### HTTP 首部字段

#### HTTP 首部字段传递重要消息

HTTP首部字段是构成HTTP报文的要素之一。在客户端与服务器之间以HTTP协议进行通信的过程中，无论是请求还是响应都会使用首部字段，它能起到额外重要消息的作用。

使用首部字段是为了给浏览器和服务器提供报文主体大小、所使用语言、认证消息等内容。

#### HTTP 首部字段结构

HTTP 首部字段是由首部字段和字段值构成的，中间用冒号“：”分隔

```http
首部字段名：字段值
```

例如，在HTTP首部中以 Content-Type 这个字段来表示报文主体的对象类型

```http
Content-Type: text/html
```

另外，字段值对应单个HTTP首部字段可以有多个值。

```http
Keep-Alive: timeout=15, max=100
```

若HTTP首部字段重复了会如何

当HTTP报文首部中出现了两个或者两个以上具有相同首部字段名时会怎么样？这种情况在规范内还没明确，根据浏览器内部处理逻辑的不同而不同。有些浏览器会优先处理第一次出现的首部字段，有些则反之。

#### 4种 HTTP 首部字段类型

HTTP 首部字段根据实际用途被分为以下4种类型

| HTTP 首部字段类型                      | 描述                                                         |
| -------------------------------------- | ------------------------------------------------------------ |
| 通用首部字段（General Header Fields）  | 请求报文和响应报文两方都会使用的首部                         |
| 请求首部字段（Request  Header Fields） | 从客户端向服务器端发送请求报文时使用的首部。补充了请求的附加内容、客户端消息、响应内容相关优先级等消息 |
| 响应首部字段（Response Header Fields） | 从服务器端向客户端返回响应报文时使用的首部。补充了响应的附加内容，也要求客户端附加额外的内容消息。 |
| 实体首部字段（Entity Header Fields）   | 针对请求报文和响应报文的实体部分使用的首部。补充了资源内容更新时间等与实体有关的消息。 |

#### HTTP/1.1 首部字段一览

HTTP/1.1 规范定义了如下47种首部字段

##### 通用首部字段

| 首部字段名        | 说明                       |
| ----------------- | -------------------------- |
| Cache-Control     | 控制缓存的行为             |
| Connection        | 逐跳首部、连接的管理       |
| Date              | 创建报文的日期时间         |
| Pragma            | 报文指令                   |
| Trailer           | 报文末端的首部一览         |
| Transfer-Encoding | 指定报文主体的传输编码方式 |
| Upgrade           | 升级为其他协议             |
| Via               | 代理服务器的相关消息       |
| Warning           | 错误通知                   |

##### 请求首部字段

| 首部字段名          | 说明                                          |
| ------------------- | --------------------------------------------- |
| Accept              | 用户代理可处理的媒体类型                      |
| Accept-Charset      | 优先的字符集                                  |
| Accept-Encoding     | 优先的内容编码                                |
| Accept-Language     | 优先的语言（自然语言）                        |
| Authorization       | Web 认证信息                                  |
| Expect              | 期待服务器的特定行为                          |
| From                | 用户的电子邮箱地址                            |
| Host                | 请求资源所在服务器                            |
| If-Match            | 比较实体标记（ETag）                          |
| If-Modified-Since   | 比较资源的更新时间                            |
| If-None-Match       | 比较实体标记（与If-Match相反）                |
| If-Range            | 资源未更新时发送实体Byte的范围请求            |
| If-Unmodified-Since | 比较资源的更新时间（与If-Modified-Since相反） |
| Max-Forwards        | 最大传输逐跳数                                |
| Proxy-Authorization | 代理服务器要求客户端的认证信息                |
| Range               | 实体的字节范围请求                            |
| Referer             | 对请求中 URI 的原始获取方                     |
| TE                  | 传输编码的优先级                              |
| User-Agent          | HTTP 客户端程序的信息                         |

##### 响应首部字段

| 首部字段名         | 说明                         |
| ------------------ | ---------------------------- |
| Accept-Ranges      | 是否接受字节范围请求         |
| Age                | 推算资源创建经过的时间       |
| ETag               | 资源的匹配消息               |
| Location           | 令客户端重定向至指定的 URI   |
| Proxy-Authenticate | 代理服务器对客户端的认证消息 |
| Retry-After        | 对再次发起请求的时机要求     |
| Server             | HTTP服务器的安装消息         |
| Vary               | 代理服务器缓存的管理消息     |
| WWW-Authenticate   | 服务器对客户端的认证消息     |

##### 实体首部字段

| 首部字段名       | 说明                         |
| ---------------- | ---------------------------- |
| Allow            | 资源可支持的HTTP方法         |
| Content-Encoding | 实体主体适用的编码方式       |
| Content-Language | 实体主体的自然语言           |
| Content-Length   | 实体主体的大小（单位：字节） |
| Content-Location | 替换对应资源的 URI           |
| Content-MD5      | 实体主体的报文摘要           |
| Content-Range    | 实体主体的位置范围           |
| Content-Type     | 实体主体的媒体类型           |
| Expires          | 实体主体的过期的日期时间     |
| Last-Modified    | 资源的最后修改时间           |

#### 非 HTTP/1.1首部字段

在HTTP协议通信交互中使用到的首部字段，不限于RFC2616中定义的47中首部字段。还有Cookie、Set-Cookie和Content-Disposition等在其他RFC中定义的首部字段，它们的使用频率也很高。

这些非正式的首部字段统一归纳在RFC4229HTTP Header Field Registrations中。

#### End-to-end 首部和Hop-by-hop 首部

HTTP首部字段将定义成缓存代理和非缓存代理的行为，分为2种类型。

| 缓存代理类型                  | 描述                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| 端到端首部（End-to-end）      | 分在此类别中的首部都会转发给请求/响应对应的最终接收目标，且必须保存在由缓存生成的响应中，另外规定它必须被转发。 |
| 逐跳首部（Hop-by-hop-Header） | 分在此类别中的首部只对单次转发有效，会通过缓存或代理而不再转发。HTTP/1.1和之后的版本中，如果要使用 hop-by-hop首部，需提供Connection 首部字段。 |

下面列举了HTTP/1.1中的逐跳首部字段。除了这8个首部字段之外，其他所有字段都属于端到端首部。

- Connection
- Keep-Alive
- Proxy-Authenticate
- Proxy-Authorization
- Trailer
- TE
- Transfer-Encoding
- Upgrade

### HTTP/1.1 通用首部字段

通用首部字段是指，请求报文和响应报文双方都会使用的首部

#### Cache-Control

通过制定首部字段 Cache-control 的指令，就能操作缓存的工作机制。

指令的参数是选的，多个指令之间通过“，”分隔。首部字段 Cache-Control 的指令可用于请求及响应时。

```http
Cache-Control: private, max-age=0,no-cache
```

##### 缓存请求指令

| 指令               | 参数   | 说明                         |
| ------------------ | ------ | ---------------------------- |
| no-cache           | 无     | 强制向源服务器再次验证       |
| no-store           | 无     | 不缓存请求或响应的任何内容   |
| max-age = [秒]     | 必需   | 响应的最大Age值              |
| max-stale（=[秒]） | 可省略 | 接收已过期的响应             |
| min-fresh = [秒]   | 必需   | 期望在指定时间内的响应仍有效 |
| no-transform       | 无     | 代理不可更改媒体类型         |
| noly-if-caches     | 无     | 从缓存获取资源               |
| cache-extension    | -      | 新指令标记（token）          |

##### 缓存响应指令

| 指令             | 参数   | 说明                                           |
| ---------------- | ------ | ---------------------------------------------- |
| public           | 无     | 可向任意方提供响应的缓存                       |
| private          | 可省略 | 仅向特定用户返回响应                           |
| no-cache         | 可省略 | 缓存前必须先确认其有效性                       |
| no-store         | 无     | 不缓存请求或响应任何内容                       |
| no-transform     | 无     | 代理不可更改媒体类型                           |
| must-revalidate  | 无     | 可缓存但必须再向源服务器进行确认               |
| proxy-revalidate | 无     | 要求中间缓存服务器对缓存的响应有效性再进行确认 |
| max-age = [秒]   | 必需   | 响应的最大Age值                                |
| s-maxage=[秒]    | 必需   | 公共缓存服务器相应的最大Age值                  |
| cache-extension  | -      | 新指令标记（token）                            |

##### 表示是否能缓存的指令

###### public 指令

```http
Cache-Control: public
```

当指定使用 public 的指令时，则明确表明其他用户也可以利用缓存。

###### private 指令

```http
Cache-control: private
```

当指定 private 指令后，响应只以特定的用户作为对象，这与 public 指令的行为相反。缓存服务器会对该特定用户提供资源缓存的服务，对于其他用户发送过来的请求，代理服务器则不会返回缓存。

###### no-cache 指令

```http
Cache-control: no-cache
```

使用 no-cache 指令的目的是为了防止从缓存中返回过期的资源。

客户端发送的请求中如果包含 no-cache 指令，则表示客户端将不会接收缓存过的响应。于是，“中间”的缓存服务器必须把客户端请求转发给源服务器。

如果源服务器返回的响应中包含 no-cache 的指令，那么缓存服务器不能对资源进行缓存。源服务器以后也将不会对缓存服务器请求中提出的资源有效性进行确认，且禁止其对响应资源进行缓存操作。

```http
Cache-control: no-cache=Location
```

由服务器返回的响应中，若报文首部字段 Cache-Control 中对 no-cache 字段名具有指定参数值，那么客户端在接收到这个被指定参数值的首部字段对应的响应报文后，就不能使用缓存。换言之，无参数值的首部字段可以使用缓存，只能在响应指令中指定该参数。

注意：从字面意思上很容易把 no-cache 误解成为不缓存，但事实上 no-cache 代表不缓存过期的资源，缓存会向源服务器进行有效期确认后处理资源，也许称为 do-not-serve-from-cache-without-revalidation 更合适。no-store 才是真正地不进行缓存。

##### 控制可执行缓存的对象指令

###### no-store 指令

```http
Cache-control: no-store
```

当使用 no-store指令时，暗示请求（和对应的响应）或响应中包含机密信息。因此，该指令规定缓存不能在本地存储请求或响应的任一部分。

##### 指定缓存期限和认证的指令

###### s-maxage 指令

```http
Cache-control: s-maxage = 604800（单位 ：秒）
```

s-maxage 指令的功能和 max-age 指令的相同，它们的不同点是 s-maxage 指令只适用于供多用户使用的公共缓存服务器（代理）。也就是说，对于同一个用户重复返回相应的服务器来说，这个指令是没有任何作用。

另外，当使用 s-maxage 指令后，则直接忽略对 Expires 首部字段及 max-age 指令的处理。

###### max-age 指令

```http
Cache-control: max-age = 604800（单位 ：秒）
```

当客户端发送的请求中包含 max-age 指令时，如果判定缓存资源的缓存时间数值比指定时间的数值更小，那么客户端就接受缓存的资源。另外，当指定 max-age 的值为 0，那么缓存服务器通常需要将请求转发给源服务器。

当服务器返回的响应中包含 max-age 指令时，缓存服务器将不对资源的有效性再次确认，而 max-age 数值代表资源保存为缓存的最长时间。

应用 HTTP/1.1 版本的缓存服务器遇到同时存在 Expires 首部字段的情况时，会优先处理 max-age 指令，而忽略掉 Expires 首部字段。而HTTP/1.0 版本的缓存服务器的情况却相反，max-age 指令会被忽略掉。

###### min-fresh 指令

```http
Cache-Control: min-fresh=60（单位：秒）
```

min-fresh 指令要求缓存服务器返回至少还未过指定时间的缓存资源。比如：当指定了 min-fresh 为 60秒之后，过了60秒的资源都无法作为响应返回了。

###### max-stale 指令

```http
Cache-Control: max-stale=3600（单位：秒）
```

使用 max-stale 可指示缓存资源，即使过期也照常接收。如果指令为指定参数值，那么无论经过多久，客户端都会接收响应。如果指令中指定了具体数值，那么即使过期，只要仍然处于 max-stale 指定的时间内，仍旧会被客户端接收。

###### on-if-cached 指令

```http
Cache-Control: only-if-cached
```

使用 only-if-cached 指令表示客户端仅在缓存服务器本地缓存目标资源的情况下才会要求其返回。换言之，该指令要求缓存服务器不重新加载响应，也不会再次确认资源有效性。若发生请求缓存服务器的本地缓存无响应，则返回状态码 504 Gateway Timeout。

###### must-revalidate 指令

```http
Cache-Control: must-revalidate
```

使用 must-revalidate 指令，代理会向源服务器再次验证即将返回的响应缓存目前是否仍然有效。若代理无法连通源服务器再次获取有效资源的话，缓存必须给客户端一条 504（Gateway Timeout）状态码。
另外，使用 must-revalidate 指令会忽略请求的 max-stale 指令（即使已经在首部使用了 max-stale，也不会再有效果）。

###### proxy-revalidate 指令

```http
Cache-Control: proxy-revalidate
```

proxy-revalidate 指令要求所有的缓存服务器在接收到客户端带有该指令的请求返回响应之前，必须再次验证缓存的有效性。

###### no-transform 指令

```http
Cache-Control: no-transform
```

使用 no-transform 指令规定无论是在请求还是响应中，缓存都不能改变实体主体的媒体类型。这样做可防止缓存或代理压缩图片等类似操作。

##### Cache-Control 扩展

###### cache-extension token

```http
Cache-Control: private, community="UCI"
```

通过 cache-extension 标记（token），可以扩展 Cache-Control 首部字段内的指令。
如上例，Cache-Control 首部字段本身没有 community 这个指令。借助extension tokens 实现了该指令的添加。如果缓存服务器不能理解community 这个新指令，就会直接忽略。因此，extension tokens 仅对能理解它的缓存服务器来说是有意义的。

#### Connection

Connection 首部字段具备以下两个作用

- 控制不再转发给代理的首部字段

  ![控制不再转发给代理的首部字段](/images/2018-08-17-read-图解HTTP-Part6-Connection1.png)

  ```http
  Connection: 不再转发的首部字段名
  ```

- 管理持久连接

  ![管理持久连接](/images/2018-08-17-read-图解HTTP-Part6-Connection2.png)

  ```http
  Connection: close
  ```

  HTTP/1.1 版本的默认连接都是持久连接。为此，客户端会在持久连接上连续发送请求。当服务器端想明确断开连接时，则指定Connection 首部字段的值为 Close。

  ```http
  Connection: Keep-Alive
  ```

  HTTP/1.1 之前的 HTTP 版本的默认连接都是非持久连接。为此，如果想在旧版本的 HTTP 协议上维持持续连接，则需要指定Connection 首部字段的值为 Keep-Alive。

#### Date

首部字段 Date 表明创建 HTTP 报文的日期和时间。

```http
Date: Tue, 03 Jul 2012 04:40:59 GMT
```

之前的 HTTP 协议版本中使用在 RFC850 中定义的格式，如下所示。

```http
Date: Tue, 03-Jul-12 04:40:59 GMT
```

除此之外，还有一种格式。它与 C 标准库内的 asctime() 函数的输出格式一致。

```http
Date: Tue Jul 03 04:40:59 2012
```

#### Pragma

Pragma 是 HTTP/1.1 之前版本的历史遗留字段，仅作为与 HTTP/1.0的向后兼容而定义。

```http
Pragma: no-cache
```

该首部字段属于通用首部字段，但只用在客户端发送的请求中。客户端会要求所有的中间服务器不返回缓存的资源。

所有的中间服务器如果都能以 HTTP/1.1 为基准，那直接采用 Cache-Control: no-cache 指定缓存的处理方式是最为理想的。但要整体掌握全部中间服务器使用的 HTTP 协议版本却是不现实的。因此，发送的请求会同时含有下面两个首部字段。

```http
Cache-Control: no-cache
Pragma: no-cache
```

#### Trailer

首部字段 Trailer 会事先说明在报文主体后记录了哪些首部字段。该首部字段可应用在 HTTP/1.1 版本分块传输编码时。

```http
HTTP/1.1 200 OK
Date: Tue, 03 Jul 2012 04:40:56 GMT
Content-Type: text/html
...
Transfer-Encoding: chunked
Trailer: Expires
...(报文主体)...
0
Expires: Tue, 28 Sep 2004 23:59:59 GMT
```

以上用例中，指定首部字段 Trailer 的值为 Expires，在报文主体之后（分块长度 0 之后）出现了首部字段 Expires。

#### Transfer-Encoding

首部字段 Transfer-Encoding 规定了传输报文主体时采用的编码方式。

HTTP/1.1 的传输编码方式仅对分块传输编码有效。

```http
HTTP/1.1 200 OK
Date: Tue, 03 Jul 2012 04:40:56 GMT
Cache-Control: public, max-age=604800
Content-Type: text/javascript; charset=utf-8
Expires: Tue, 10 Jul 2012 04:40:56 GMT
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Content-Encoding: gzip

Transfer-Encoding: chunked

Connection: keep-alive


cf0 ←16进制(10进制为3312)
...3312字节分块数据...
392 ←16进制(10进制为914)
...914字节分块数据...
0
```

以上用例中，正如在首部字段 Transfer-Encoding 中指定的那样，有效使用分块传输编码，且分别被分成 3312 字节和 914 字节大小的分块数据。

#### Upgrade

首部字段 Upgrade 用于检测 HTTP 协议及其他协议是否可使用更高的版本进行通信，其参数值可以用来指定一个完全不同的通信协议。

![Upgrade](/images/2018-08-17-read-图解HTTP-Part6-Upgrade.png)

上图用例中，首部字段 Upgrade 指定的值为 TLS/1.0。请注意此处两个字段首部字段的对应关系，Connection 的值被指定为 Upgrade。Upgrade 首部字段产生作用的 Upgrade 对象仅限于客户端和邻接服务器之间。因此，使用首部字段 Upgrade 时，还需要额外指定Connection:Upgrade。
对于附有首部字段 Upgrade 的请求，服务器可用 101 SwitchingProtocols 状态码作为响应返回。

#### Via

使用首部字段 Via 是为了追踪客户端与服务器之间的请求和响应报文的传输路径。报文经过代理或网关时，会先在首部字段 Via 中附加该服务器的信息，然后再进行转发。这个做法和traceroute 及电子邮件的 Received首部的工作机制很类似。首部字段 Via 不仅用于追踪报文的转发，还可避免请求回环的发生。所以必须在经过代理时附加该首部字段内容。

![Via](/images/2018-08-17-read-图解HTTP-Part6-Via.png)

上图用例中，在经过代理服务器 A 时，Via 首部附加了“1.0gw.hackr.jp (Squid/3.1)”这样的字符串值。行头的 1.0 是指接收请求的服务器上应用的 HTTP 协议版本。接下来经过代理服务器 B 时亦是如
此，在 Via 首部附加服务器信息，也可增加 1 个新的 Via 首部写入服务器信息。

Via 首部是为了追踪传输路径，所以经常会和 TRACE 方法一起使用。比如，代理服务器接收到由 TRACE 方法发送过来的请求（其中Max-Forwards: 0）时，代理服务器就不能再转发该请求了。这种情况下，代理服务器会将自身的信息附加到 Via 首部后，返回该请求的响应。

#### Warning

HTTP/1.1 的 Warning 首部是从 HTTP/1.0 的响应首部（Retry-After）演变过来的。该首部通常会告知用户一些与缓存相关的问题的警告。

```http
Warning: 113 gw.hackr.jp:8080 "Heuristic expiration" Tue, 03 Jul 2012
```

Warning 首部的格式如下。最后的日期时间部分可省略。

```http
Warning: [警告码][警告的主机:端口号]“[警告内容]”([日期时间])
```

HTTP/1.1 中定义了 7 种警告。警告码对应的警告内容仅推荐参考。另外，警告码具备扩展性，今后有可能追加新的警告码。

| 警告码 | 警告内容                               | 说明                                               |
| ------ | -------------------------------------- | -------------------------------------------------- |
| 110    | Response is stale（响应已过期）        | 代理返回已过期的资源                               |
| 111    | Revalidation failed（再验证失败）      | 代理再验证资源有效性时失败（服务器无法到达等原因） |
| 112    | Disconnection operation（断开连接操    | 代理与互联网连接被故意切断                         |
| 113    | Heuristic expiration（试探性过期）     | 响应的使用期超过24小时（有效缓存                   |
| 199    | Miscellaneous warning（杂项警告）      | 任意的警告内容                                     |
| 214    | Transformation applied（使用了转换）   | 代理对内容编码或媒体类型等执行了                   |
| 299    | Miscellaneous persistent warning（持久 | 任意的警告内容                                     |

参考链接：https://book.douban.com/subject/25863515/