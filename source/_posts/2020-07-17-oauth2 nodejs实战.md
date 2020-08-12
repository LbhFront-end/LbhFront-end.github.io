---
title: oauth2 nodejs实战
date: 2020-07-17 11:17:40
tags: docker
categories: 
	- nodejs实战
---



# oauth2 nodejs实战

## 第一章：OAuth2.0是什么

### 是什么

OAuth2.0是一个授权协议，允许软件应用代表资源拥有者去访问资源拥有者的资源。应用向资源拥有者请求授权，然后取得令牌token并用它来访问资源。这一切都不需要应用去充当资源拥有者的身份，因为令牌明确表示了被授予的访问权，限制客户端只能执行资源拥有者授权的操作，或者OAuth2.0是一个安全协议，能让第三方应用以有限的权限访问HTTP服务，可以通过构建资源拥有者与HTTP服务间的许可交互机制，让第三方应用代表资源拥有者访问服务，或者通过授权权限给第三方应用，让其代表自己访问服务。

作为一个授权框架，OAuth关注的是如何让一个系统组件获取对另一个系统㢟的访问权限，最常见的情形是客户端应用代表资源拥有者（通常是最终用户）访问受保护资源。三个角色：

- 资源拥有者有权访问API,并将API访问权限委托出去。资源拥有者一般是能够使用浏览器的人

- 受保护资源是资源拥有者有限权限访问的组件。这样的组件有多种形式，一般是某种形式的Web API，资源听起来像是某种能下载的东西，但其实这些API支持读、写和其他操作。

- 客户端是代表资源拥有者访问受保护资源的软件。在OAuth中，只要软件使用了受保护的资源的API,就是客户端。

例子：假设度假拍的照片上传到了照片存储网站，现在想将它们打印出来。照片存储网络的API就是资源，打印服务则是那个API的客户端。作为资源拥有者，需要将一部分权利委托给照片打印服务，让它能读取照片。但你不想让打印服务读取所有照片也不想它有删除或者上传的权限。

### 旧时代：凭证共享与凭证盗用

#### 复制凭证

之前流行的做法是复制用户的凭证并用它登录另一个服务。就是照片打印服务假设用户在照片存储服务商使用的凭证与在打印服务上的相同。当用户登录照片打印服务后，该服务使用用户的用户名和密码登录照片存储网络，获取用户的账号访问权，假装用户。

这种情况下，用户需要使用某种凭证与客户端进行身份认证，这些凭证通常是被集中控制的，并受客户端和受保护资源一致认可。客户端先得到用户的用户名和密码或者会话cookie，然后用它们访问受保护资源，假装是用户。受保护资源将客户端视为用户并直接通过身份认证，而实际上与受保护资源建立连接的是客户端。

这种方法要求用户在客户端和受保护资源端使用相同的凭据，使得这种凭据盗用技术只能在同一安全域内使用。也就是说，如果是一个公司控制着客户端、授权服务器和受保护资源，并且这些组件都使用相同的策略和网络控制下运作，这种方法才行得通。如果打印服务和存储服务是由同一个公司提供的，就能采用这种方法，因为用户可以在两个服务使用相同的账户凭据。

这一技术还会将用户的密码暴露给客户端应用，即使在单一安全域使用同一组凭据，这也基本上无法避免。但无论如何，客户端是在扮演用户，受保护资源无法区分资源拥有者和扮演资源拥有者的客户端，因此两者都用相同的用户名和密码。

#### 索取并复制密码

如果两个服务处于不同的安全域中，如照片打印例子中的情况，不能复制用户提供的用于登录当前应用的密码了，因为这个密码对于另一个应用来说是无效的。对于这个问题可以采取另一种老套的手段来获取密码：向用户索取。

打印服务想要获取用户的照片，可以提示用户输入照片存储网络上的用户名和密码，打印服务用这些凭据访问受保护资源，扮演用户。在这种情况下，用户用于登录客户端的凭据和用于访问受保护资源的凭据可以不同。很多用户在实际中会运行这样的要求，特别是当使用受保护资源的是一个很有用的服务时。

因此这仍然是当前移动应用通过用户账号访问后端服务的最常见的方法之一：移动应用让用户输入用户名和密码，然后直接将这些凭证通过网络发送给后端API。为了可以持续访问API,客户端应用会保存用户的凭据，以便在必要的时候访问受保护资源，这种做法很危险，一旦任何一个正在使用中的客户端被攻破，这意味着用户在所有系统的账号都被攻破。

#### 开发者密钥

复制用户密码并不是一个好方法，如果授予打印服务全局的访问权限，使它能代表由它指定的任何用户并访问存储服务上的所有照片，常见的方式是为客户端颁发一个开发者密钥，让客户端使用该密钥直接调用受保护资源。

开发者密钥是一种全局的密钥，客户端可以用它来充当任意一个由其指定的用户，用户的指定很可能通过一个API参数来完成。这样做的好处是避免了向客户端暴露用户凭据，但代价是要向客户端提供功能强大的开发者密钥。有了这种密钥，打印服务随时都能任意地打印所有用户的所有照片，因此它实际上拥有了自由访问受保护资源的权利。这在一定程度上是可行，但前提是受保护资源充分了解并信任客户端。但是这样的关系几乎不可能存在于两个组织之间，例如照片打印例子中的两个服务。此外，如果客户端的密钥被盗，将对受保护资源造成灾难性损害，因为存储服务的所有用户都将受到影响，无论他们是否使用打印服务。

#### 特殊密码

给用户一个特殊密码，此密码仅用于透露给第三方服务。用户自己不会使用这个密码来登录，只是将它粘贴到所使用的第三方应用里。用户不再需要想客户端透露登录密码，受保护资源也不在需要相信客户端都能代表所有用户执行正确的操作。但是这种系统的可用性不好。要求用户除了管理自己的主密码之外，还要创建、分发、管理特殊凭据。因为需要用户来管理这些凭据，一般来说，客户端与凭据本身并没有对应关系，这使得撤销某个应用的访问权限变得很困难。

#### 更好的方法

如果能为每个客户端和每个用户的组合分别颁发这种受保护资源具有受限访问的凭据，就可以将受限访问分别与受限凭据绑定。如果有一个基于网络的协议，能够部署到整个互联网上，跨安全边界地生成安全分发这些首先呢的凭据，同时具有良好的用户体验。



### 授权访问

OAuth协议的设计目的是：让最终用户通过OAuth将他们在受保护资源上的部分权限委托给客户端应用，使客户端应用代表他们执行操作。为实现这一点，OAuth在系统中引入了另外一个组件：授权服务器。

受保护资源依赖授权服务器向客户端颁发专用的安全凭据——OAuth访问令牌。为了获取令牌，客户端首先将资源拥有者引导至授权服务器，请求资源拥有者为其授权。授权服务器先对资源拥有者进行身份认证，然后一般会让资源拥有者选择是否对客户端授权。客户端可以请求授权功能或权限范围的子集，该子集可能会被资源拥有者进一步缩小。一旦授权请求被许可，客户端就可以向授权服务器请求访问令牌。按照资源拥有者的许可，客户端可以使用该令牌对受保护资源上的API进行访问。

在这个过程中，没有讲资源拥有者的凭据暴露给客户端：资源拥有者向授权服务器进去身份认证的过程中所用的信息是独立于客户端交互的。客户端没有功能强大的开发者密钥，无法随意访问任何资源，而是必须在得到有效的资源拥有者的授权之后才能访问受保护资源。虽然大多数OAuth客户端可以向授权服务器进行身份认证，但仍然需要得到授权后才能访问资源。

用户通常不必查看或者直接处理访问令牌，OAuth不需要由用户生成令牌并粘贴到客户端，而是简化了这一过程：客户端请求令牌，用户对客户端授权，然后由客户端管理令牌，用户管理客户端应用。

#### 超越HTTP基本认证协议和密码共享反模式

上面很多传统方法都是密码反模式的案例，通过共享机密信息（密码）来直接代表当事方（用户）。用户通过与应用共享密码，使应用能够访问受保护的API，然而这种方式非常不安全。

HTTP API最开始是如何引入密码保护功能的呢？可以从 HTTP 协议的历史及其安全手段入手，HTTP 协议制定了一个机制，用户可以凭借该机制在浏览器中使用用户名和密码向一个网页进行身份认证，这就是所谓的 HTTP 基本认证协议 （HTTP basic auth）。还有一种更安全的认证协议，叫做 HTTP 摘要认证（HTTP digest auth）。它们都假设用户在场，并且要求 HTTP 服务器呈现用户的用户名和密码，此外由于 HTTP 是一个无状态的协议，因此每一个 HTTP 事务都要呈现这些凭据。

HTTP不会区分一个事务是由用户通过浏览器发起的，还是通过其他软件发起的，这种基本的灵活性是HTTP协议得到普及的关键原因。这样有一个问题是除了面向用户的网页或服务之外，当 HTTP 用于直接访问 API时，现有的安全机制顺理成章被沿用到新的应用场景，为API和网页服务不断呈现 密码。虽然浏览器可以使用cookie技术或其他会话管理技术，但是访问Web API的HTTP客户端没有这样的机制可用。

OAuth从一开始就被设计成一个可用的API协议，其中主要的交互过程都是在浏览器之外进行的。OAuth的整个流程通常是由最终用户在浏览器中启动的，实际上这也正是委托模式的灵活性和优势所在。但是最终接收令牌、使用令牌访问受保护资源的步骤对用户是不可见的。实际上，OAuth的一些主要事务过程都是发生在用户不在场的情况下，客户端仍然能够代表用户执行操作。OAuth让我们摒弃HTTP基本协议中的观念和假设，将一种功能强大、安全的方式引入现如今的API体系

#### 授权委托：重要性及应用

委托概念是OAuth强大功能的根基，虽然OAuth经常被称作授权协议，但它也是一个委托协议。通常，被委托的是用户权限的子集，但是OAuth本身并不承载或者传递权限。相反，它提供一种方法，让客户端可以请求用户将部分权限委托给自己，然后用户可以批准这个委托请求，被批准后，客户端就可以去执行那些操作了。

以照片打印为例子，照片打印服务可以询问用户：是否在这个存储服务存放了照片？如果是我可以帮打印出来。然后用户被引导至照片存储服务，存储服务也会问：打印服务想要获取照片，同意吗？用户可以决定是否同意，即决定是否将访问权限委托给打印服务。

委托协议和授权协议的区别很重要，因为OAuth令牌中携带的授权信息对系统中的大部分组件是不透明的。只有受保护资源需要了解授权信息，只要它能从令牌得知授权信息（既可以直接从令牌中获取，也可以通过某种服务来获取），就可以按照要求提供API服务

#### 用户主导的安全与用户的选择

由于OAuth的委托过程需要资源拥有者的参与，因此它提供了一种在很多其他安全模型中不存在的可能性：重要的安全决策可以由最终用户来做。传统上，安全决策一直由集权机构负责。由集权机构决定谁可以使用服务、使用什么客户端以及以何种目的使用。OAuth则允许集权机构将某些决策权交到最终使用软件的用户手中。

OAuth系统常遵循TOFU原则：首次使用时信任（trust on first use）。在TOFU模型中，需要用户在第一次运行时进行安全决策，而且并不为安全决策预设任何先决条件或者配置，仅提示用户做出决策。这个过程可以简单到只是询问用户“要连接新的应用吗”。很多实现运行在这个步骤中进行更多控制。无论用户遇到的是哪种情况，只要具有对应的权限，就能做出安全决策。系统会记住用户的决策，以便以后使用。只要首次建立了授权关系，系统就会在后续的处理过程中继续信任用户的决策：首次使用时信任。

### OAuth2.0：优点、缺点和丑陋方面

OAuth2.0非常善于获取用户的委托决策，并通过网络传递出去。运行多方参与安全决策过程，尤其是在运行期间让最终用户参与决策。由多个可移动的组件构成的协议，但是在很多方面都比其他方案更简单、更安全。

OAuth2.0设计中有一个重要的阶段，就是不受控的客户端总是比授权服务器或者受保护资源多出好几个数量级。因为单个授权服务器可以很轻松地保护多个资源服务器，并且很可能有许多不同类型的客户端想要访问特定的API。一台授权服务器甚至可以有多个不同的客户端信任等级。这样的架构决策导致的结果就是， 尽可能将复杂性从客户端转移到服务端。这对于客户端开发人员来说是好事，因为客户端成了系统最简单的部分。客户端人员不再需要像在先前的安全协议中，处理签名规范化以及解析复杂的安全策略文档，也不需要担心处理敏感的用户凭据。OAuth令牌提供了一种比密码略复杂的机制，如果使用得当，其安全性比密码高很多。

另一方面，授权服务和受保护资源要承担更多复杂性和安全性方面的责任。客户端只要保护好自身的客户端凭据和用户的令牌即可，单个客户端被攻破会造成损害，但只有该客户端的用户会受到影响。被攻破的客户端也不会泄露资源拥有者的凭据，因此客户端根本没有机会接触这些凭据。然而，授权服务器则需要管理和保护系统中所有客户端和用户的凭证和令牌。虽然这确实使它更容易称为攻击目标，但是保护单个授权服务器要比保护上千台由不同开发人员开发的客户端容易得多。

OAuth2.0的可拓展性和模块化是其最大的优势之一，因为这使得该协议适用于各种环境。然而正是这种灵活性导致不同的实现之间存在基本的兼容性问题。当开发人员想在不同的系统上实现OAuth时，它提供的众多自定义选项更让人困惑。

### OAuth2.0不能做什么

#### 没有定义HTTP协议之外的情形

由于使用bearer令牌的OAuth2.0并不提供消息签名，因此不应该脱离HTTPS使用。机密信息需要在网络上传播，所以OAuth需要TLS这样的传输机制来保护这些信息。

#### 不是身份认证协议

OAuth本身并不透露关于用户的信息，本质上是一个部件，能用于在更宏大的技术方案中提供其他功能，另外OAuth 在多个地方用到了身份认证，最典型的就是资源拥有者和客户端软件要向授权服务器进行身份认证。但这种内嵌身份认证的行为并不会使OAuth自身成为身份认证协议

#### 没有定义用户对用户的授权机制

尽管它在根本上是一个用户向软件授权的协议。OAuth假设资源拥有者能够控制客户端。要使资源拥有者向另一个用户授权，仅适用OAuth是不行的。但这种授权并不罕见，User Managed Access协议就是为此而生，规定了如何使用OAuth 构建一个支持用户对用户授权的系统。

#### 没有定义授权处理机制

提供了一种传达授权委托已发生这一事实，但是它并不定义授权的内容。相反，由服务API定义使用权限范围、令牌之类的OAuth组件来定义一个给定的令牌适用于哪些操作

#### 没有定义令牌格式

明确声明了令牌的内容对客户端是完全不透明的。颁发令牌的授权服务器和接收令牌的受保护资源需要理解令牌。这个层面的互操作性要求催发了 JSON Web Token(JWT)格式和令牌内省协议。虽然令牌本身对客户端还是不透明的，但现在它的格式能被其他组件理解。

#### 没有定义加密方法

没有新的加密机制，而是允许借用通用的加密机制，这些加密机制不止适用于OAuth。这种有意的遗漏催生了JSON对象签名和加密（JOSE）规范套件，该套件提供了一系列通用的加密机制，可以配合OAuth使用，也可以脱离OAuth使用。

#### 不是单体协议

规范被分成了多个定义和流程，每个定义和流程都有各自适用的场景。在某种程度上，可以将OAuth2.0视为一个安全协议生成器，因为它可用于许多不同的应用场景设计安全架构。

### 小结

OAuth是一个应用广泛的安全标准，提供了一种安全访问受保护资源的方式，特别适用于Web API

- 关注的是如何获取令牌和如何使用令牌
- 是一个委托协议，提供跨系统授权的方案
- 用可用性和安全性更高的委托协议取代了密码共享反模式
- 专注于很好地解决小问题集，因而是整个安全系统中一颗很合用的螺丝钉

## 第二章：协议与组件

### 获取和使用令牌

OAuth是一个复杂的安全协议，需要不同的组件互相通信，从根本上说，OAuth事务中的两个重要步骤是颁发令牌和使用令牌。令牌表示授予客户端的访问权，在OAuth2的各个部分都起到了核心作用。尽管每个步骤的细节都会因多种因素而异，但是一个规范的OAuth事务包含以下事件

1. 资源拥有者向客户端表示他希望客户端代表他执行一些任务
2. 客户端在授权服务器上向资源拥有者请求授权
3. 资源拥有者许可客户端的授权请求
4. 客户端接收到来自授权服务器的令牌
5. 客户端向受保护的资源出示令牌

### 授权许可的完整过程

首先，资源拥有者访问客户端应用，并表明他希望客户端代表自己去使用某一受保护资源。例如，用户会在这一步示意打印服务去使用某个照片存储服务。该服务是个API,客户端知道如何调用它，并且还知道需要通过OAuth来调用。

当客户端发现需要获取一个新的OAuth访问令牌时，它会将资源拥有者重定向至授权服务器，并附带一个授权请求，表示它要向资源拥有者请求一些权限。例如，为了能读取照片，照片打印服务可以向照片存储服务请求访问权限。由于是的是Web客户端，因此采用HTTP重定向的方式将用户代理重定向至授权服务器的授权端点，客户端的响应如下：

```http
HTTP/1.1 302 Moved Temporarily
x-powered-by: Express
Location: http://localhost:9001/authorize?response_type=code&scope=foo&client_id=oauth-client-1&redirect_uri=http%3A%2F%2Flocalhost%3A9000%2Fcallback&state=Lwt50DDQKUB8U7jtfLQCVGDL9cnmwHH1
Vary: Accept
Content-Type: text/html; charset=utf-8
Content-Length: 444
Date: Fri, 31 Jul 2015 20:50:19 GMT
Connection: keep-alive
```

这个重定向响应导致浏览器向授权服务器发送一个GET请求。

```http
GET /authorize?response_type=code&scope=foo&client_id=oauth-client-1&redirect_uri=http%3A%2F%2Flocalhost%3A9000%2Fcallback&state=Lwt50DDQKUB8U7jtfLQCVGDL9cnmwHH1 HTTP/1.1
Host: localhost:9001
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.10; rv:39.0)Gecko/20100101 Firefox/39.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Referer: http://localhost:9000/
Connection: keep-alive
```

客户端通过在发送给用户的URL中包含参数，来标识自己的身份和要请求的授权详情，如权限范围等。虽然请求并不是由客户端直接发出的，但授权服务器会解析这些参数并做出适当反应。

然后授权服务器会要求用户进行身份认证，进一步对确认资源拥有者的身份以及能向客户端授予哪些权限来说至关重要。

用户身份认证直接在用户（和用户浏览器）与授权服务器之间进行，这个过程对客户端应用不可见。这一重要特性避免了用户将自己的凭据透露给客户端应用，对抗这种反模式发明OAuth的原因。

另外因为资源拥有者通过浏览器与授权端点交互，所有也要通过浏览器来完成身份认证，因此，有很多身份认证技术可以用于用户身份认证流程。OAuth没有规定应该使用哪种身份认证技术，授权服务器可以自由选择，例如用户名/密码、加密证书、安全令牌、联合单点登录或者其他方式。

这种隔离方案还得客户端不会因用户身份认证方式发生变化而受到影响，让简答的客户端应用也能受益于授权服务器使用的一些新兴技术，例如基于风险的启发式认证（risk-based heuristic authentication）技术。然而这种做法并没有向客户端传递任何有关认证用户的信息。

然后用户向客户端应用授权，这一步，资源拥有者选择将一部分权限授予客户端应用，授权服务器提供了很多不同的选项来实现这一点。客户端可以在授权请求中指明其想要获得哪些权限。授权服务器可以运行用户拒绝一部分或者全部权限范围，也可以让用户批准或者拒绝整个授权请求。

此外，很多授权服务器允许将授权决策保存下来，以便以后使用。如果使用了这种方式，那么未来同一个客户端请求同样的授权时，用户将不会得到提示。用户仍然会被重定向到授权端点，并且仍然需要登录，但是会跳过批准授权环节而沿用前一次的授权决策。授权服务器甚至可以通过像客户端白名单或者黑名单这样的内部策略来否决用户的决策。

然后，授权服务器将用户重定向到客户端应用。这一步采用HTTP重定向的方式，回到客户端的 redirect_uro

```http
HTTP 302 Found
Location: http://localhost:9000/oauth_callback?code=8V1pr0rJ&state=Lwt50DDQKUB8U7jtfLQCVGDL9cnmwHH1
```

这又导致浏览器向客户端发送如下请求

```http
GET /callback?code=8V1pr0rJ&state=Lwt50DDQKUB8U7jtfLQCVGDL9cnmwHH1
HTTP/1.1 Host: localhost:9000
```

这个HTTP请求是发送给客户端不是给授权服务器的

```http
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.10; rv:39.0)Gecko/20100101 Firefox/39.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Referer: http://localhost:9001/authorize?response_type=code&scope=foo&client_id=oauth-client-1&redirect_uri=http%3A%2F%2Flocalhost%3A9000%2Fcallback&state=Lwt50DDQKUB8U7jtfLQCVGDL9cnmwHH1
Connection: keep-alive
```

由于使用的是授权码许可类型，因此该重定向链接中包含了一个特殊的查询参数code，这个参数的值被解析为授权码，它是一次性的凭据，表示用户授权决策的结果。客户端会在接收到请求之后解析该参数以获取授权码，并在下一次使用授权码。客户端还会检查state参数值是否与它在前一个步骤中发送的值匹配。

现在客户端已经得到授权码，将其发送给授权服务器的令牌端点。客户端发送一个POST请求，在HTTP主体中以表单的格式传递参数，并在HTTP基本认证头部设置`client_id`和`client_secret`。这个HTTP请求由客户端直接发送给授权服务器，浏览器或者资源拥有者不参与此过程。

```http
POST /token
Host: localhost:9001
Accept: application/json
Content-Type: application/x-www-form-encoded
Authorization: Basic b2F1dGgtY2xpZW50LTE6b2F1dGgtY2xpZW50LXNlY3JldC0x grant_type=authorization_code&redirect_uri=http%3A%2F%2Flocalhost%3A9000%2Fcallback&code=8V1pr0rJ
```

这种将不同的HTTP连接分开的做法保证了客户端能够直接进行身份认证，让其他组件无法查看或操作令牌请求。

授权服务器接收该请求，如果请求有效，则办法令牌。授权服务器需要执行多个步骤以确保请求是合法的。首先，它需要验证客户端凭据（通过Authorization头部传递）以确定是哪个客户端请求授权。然后，从请求主体中读取code参数的值，并从中读取关于该授权码的信息，包括发起初始授权请求的是哪个客户端，执行授权的是哪个用户，授权的内容是什么。如果授权码有效尚未使用过，而且发起该请求的客户端与最初发起授权请求的客户端相同，则授权服务器会生成一个新的访问令牌并返回到客户端。

该令牌以JSON对象的格式通过HTTP响应返回给客户端

```http
HTTP 200 OK
Date: Fri, 31 Jul 2016 21:32:32 GMT
Content-Type: application/json

{
	"access_token": "987tghjkiu6trfghjuytrghj",
	"token_type": "Bearer"
}
```

然后客户端可以解析令牌响应并从中获取令牌的值来访问受保护资源。在这个案例，使用了 OAuth bearer令牌，这是通过响应中的 token_type字段描述的。令牌响应还可以包含一个刷新令牌（用于获取新的访问令牌而不必重新请求授权），以及一些关于访问令牌的附加信息，比如令牌的权限访问和过期时间。客户端可以将访问令牌存储在一个安全的地方，以便以后在用户不在场的时候也能够随时访问。

有了令牌，客户端就可以在访问受保护资源时出示令牌。客户端出示的令牌的方式有很多种，备受推崇的方式，使用Authorization头部

```http
GET /resource HTTP/1.1
Host: localhost:9002
Accept: application/json
Connection: keep-alive
Authorization: Bearer 987tghjkiu6trfghjuytrghj
```

受保护资源可以从头部解析出令牌，判断它是否有效，从中得知授权者是谁以及授权内容，然后返回响应。受保护资源检查令牌的方式有很多种，最简单的就是让授权服务器和资源服务器共享存储令牌信息的数据库。授权服务器在生成新的令牌时将其写入数据库，资源服务器在收到令牌时从数据库中读取它们。

### OAuth中的角色：客户端、授权服务器、资源拥有者、受保护资源

这些组件分别负责OAuth协议的不同部分，并且相互协作使OAuth协议运转。

客户端：是代表资源拥有者访问受保护资源的软件，使用OAuth来获取访问权限，客户端通常是这系统中最简单的组件，它的职责主要是从授权服务器获取令牌以及在受保护资源上使用令牌。客户端不需要理解令牌，也不需要查看令牌的内容。相反，客户端只需要将令牌视为一个不透明的字符串即可。OAuth客户端可以是Web应用、原生应用，甚至是浏览器内的JavaScript应用。

受保护资源：通过HTTP服务进行访问，在访问时需要OAuth访问令牌，受保护资源需要验证收到的令牌，并决定是否响应以及如何响应请求，在OAuth架构中，受保护资源对是否认可令牌拥有最终决定权。在云打印的例子里面，照片存储网站就是属于受保护资源。

资源拥有者：有权将访问权限授权给客户端的主体，与OAuth系统中的其他组件不同，资源拥有者不是软件，在大多数情况下，资源拥有者是一个人，使用客户端软件访问受他控制的资源。至少在部分过程中，资源拥有者要使用Web浏览器（通常称为用户代理）与授权服务器交互。资源拥有者可能还会使用浏览器与客户端交互，但这完全取决于客户端性质。在云打印例子中，资源拥有者就是想要打印照片的最终用户。

OAuth授权服务器：是一个HTTP服务器，在OAuth系统中充当中央组件。授权服务器对资源拥有者和客户端进行身份认证，让资源拥有者向客户端授权、为客户端颁发令牌。某些授期服务器还会提供额外的功能，例如令牌内省、记忆授权决策。在云打印例子中，照片存储网络拥有自己的授权服务器，用户保护资源。

### OAuth组件：令牌、权限范围和授权许可

#### 访问令牌

OAuth访问令牌，有时也被称为令牌，由授权服务器办法给客户端，表示客户端已被授予权限。OAuth没有定义令牌本身的格式和内容，但它总是代表着：客户端请求的访问权限、对客户端授予的资源拥有者，以及被授予的权限（通常包含一些受保护资源标志）。

令牌对客户端来说是不透明的，也就是说客户端不需要，通常也不能查看令牌内容。客户端要做的是持有令牌，向授权服务器请求令牌，并向受保护资源出示令牌。OAuth令牌并非对系统中所有组件都不透明：授权服务器的任务是颁发令牌，受保护资源的任务则是验证令牌。因此，它们都需要理解令牌本身，并知道其含义。然后客户端对这一切一无所知，这使得客户端简单地多，同时也使得授权服务器和受保护资源十分灵活地部署令牌。

#### 权限范围

表示一组访问受保护资源的权限，OAuth协议中使用字符串表示权限范围，可以用空格隔开的列表将它们合并成为一个集合。因此，权限范围的值不能包含空格，OAuth并没有规定权限范围值的格式和结构。

权限范围是一种重要机制，界定了客户端的权限范围。权限范围是由受保护资源根据其自身提供的API来定义的。客户端可以请求某些权限范围，授权服务器则运行资源拥有者在客户端发出请求时许可或者否决特定的权限范围。权限范围具有可叠加的特征。

云打印的例子，照片存储服务的API为照片定义了多种权限范围：read-photo/read-metadata/update-photo/update-metadata/create/delete。照片打印服务只要能读取照片就足以完成工作，所以它会请求read-photo权限范围。只要拥有一个该权限范围的令牌，就能读取并按要求打印出来。如果用户想要依据照片日期将照片打印成册的高级功能，则打印服务还需要read-metadata权限范围。由于这是一个额外的访问权限，照片打印服务则需要通过正常的OAuth流程来请求用户授予它这个额外的权限范围。只要照片打印服务拥有包含这两个权限范围的令牌，就能使用该令牌执行相应的操作。

#### 刷新令牌

OAuth刷新令牌在概念上与该令牌很相似，它也是由授权服务器颁发给客户端的令牌，客户端也不知道不关系该令牌的内容。但不同的是，该令牌从来不会被发送到保护资源。相反，客户端使用刷新令牌向授权服务器请新的访问令牌，而不需要用户参与。

为什么客户端需要刷新令牌？在OAuth中，访问令牌随时可能失效，令牌有可能被用户撤销，或者过期，或者其他系统导致令牌失效，客户端在使用时会受到错误响应。当然，客户端可以再次向资源拥有者请求权限，但是如果资源拥有者不在场呢？

在OAuth1.0客户端除了等资源拥有者回来重新授权之外别无他法。未避免这种情况，OAuth1.0的令牌玩玩会一直保持有效，直到被明确地撤销。这是有问题的，因此它增加了被盗令牌的攻击面：攻击者可以永远使用该令牌。OAuth2.0提供了让令牌自动过期的选项，但是需要在用户不在场的时候仍然能访问资源，现在刷洗令牌取代了永不过期的访问令牌，但它的作用不是访问令牌，而是获取新的访问令牌来访问资源。这种做法是一种独立但互补的方式，限制了刷新令牌和访问令牌的暴露范围。

刷新令牌还可以让客户端缩小它的权限范围，如果客户端被授予A/B/C三个权限范围，但是它知道某特定请求只需要A权限范围，则它可以使用刷新令牌重新获取一个仅包含A权限范围的访问令牌。这让足够智能的API可以遵循最小权限安全原则，但也不会给不那么智能的客户端带来负担，即无须查明某个API需要哪些权限。

如果刷新令牌失效了，用户在场，客户端可以随时劳烦用户再次授权，换句话说，客户端退回到了需要重新进行OAuth授权的状态。

#### 授权许可

授权许可是OAuth协议中的权限获取方法，OAuth客户端用它来获取受保护资源的访问权限，成功之后客户端会得到一个令牌。令牌表示用户授权所使用的特定方式，也表示授权这个行为本身。开发人员看到回传给客户端的授权码，有时候会误认为这个授权码就是授权许可，虽然授权码代表用户的授权决策，但不是授权许可本身。相反，整个OAuth流程才是授权许可：客户端将用户重定向至授权端点，然后接收授权码，最后用授权码换取令牌。

换句话说，授权许可就是获取令牌的方式。

### OAuth的校色与组件的交互：后端信道、前端信道和端点

OAuth是一个基于HTTP的协议，但是与大多数基于HTTP的协议不同，OAuth中的交互不总是简单的HTTP请求和响应来完成。

#### 后端通信信道

OAuth流程中的很多部分都是用标准的HTTP请求和响应格式来互相通信，由于这些请求通常都发生在资源拥有者和用户代理的可见范围之外，因此它们统称为后端信道通信。

这些请求和响应使用了所有常规的HTTP机制来通信：头部，查询参数、HTTP方法和HTTP主体都能承载对OAuth事务至关重要的信息。

授权服务器提供一个授权端点，供客户端请求访问令牌和刷新令牌。客户端直接向该端点发出请求，携带一组表单格式的参数，授权服务器解析并处理这些参数，然后授权服务器返回一个代表令牌的JSON对象

另外当客户端连接受保护资源的时候，它也是在后端信道上直接发出HTTP请求。这种连接的细节完全依赖受保护资源，因为OAuth能保护的API和系统种类繁多、风格各异。对于任何类型的受保护资源，都需要客户端出示令牌，并且受保护资源必须能理解令牌以及其代表的权限。

#### 前端信道通信

在标准HTTP通信中，HTTP客户端向服务器直接发送一个请求，其中包括头部、查询参数、主体以及其他信息。然后HTTP服务器可以查看这些信息，并决定如何响应请求，响应中包含头部、主体以及其他信息。然而，在OAuth中，在某些情况下两个组件是无法至二级相互发送请求和响应的。例如客户端与授权服务器的授权端点交互的时候。前端信道通信就是一种间接通信方法，它将Web浏览器作为媒介，使用HTTP请求实现两个系统间的间接通信。

这一技术隔离了浏览器两端的会话，实现了跨安全域工作。例如，如果用户需要向其中一个组件进行身份认证，并不需要将凭据暴露给另外一个系统。这样在保持信息隔离的情况下，仍然能让用户在通信中发挥作用。

两个互不交互的软件是如何实现通信的？前端信道通信是这样实现的：发起方在一个URL中附加参数并指示浏览器跳转到该URL。然后接收方可以解析该入站URL（由浏览器跳转来的），并使用其中包含的信息。之后，接收方可以将浏览器重定向至发起方托管的URL,并使用同样的方式在URL附加参数。这样两个软件就以Web浏览器为媒介，实现了间接通信。这样意味着每个前端信道的请求和响应实际上是一对HTTP请求/响应事务。

例如，授权码许可中，客户端需要将用户重定向至授权端点，但是也需要将其请求的内容信息传递给授权服务器。为此，客户端向浏览器发送一个HTTP重定向，这个重定向的目标是授权服务器的URL,并且其查询参数中附有特定参数。

```http
HTTP 302 Found
Location: http://localhost:9001/authorize?client_id=oauth-client-1&response_type=code&state=843hi43824h42tj
```

授权服务器可以像处理一般的HTTP请求一样解析传入的URL,从参数中获取信息，这个环节中，授权服务器可以与资源拥有者进行交互，通过浏览器执行一系列HTTP事务，对资源拥有者进行身份认证并请求其授权。当需要给客户端返回授权码时，授权服务器也向浏览器返回重定向响应，但是这一次重定向的目标是客户端的redirect_uri。授权服务器也会在重定向的查询参数中附带信息。

```http
HTTP 302 Found
Location: http://localhost:9000/oauth_callback?code=23ASKBWe4&state=843hi43824h42tj
```

浏览器执行这个重定向时，会向客户端应用发送一个HTTP请求，然后客户端可以解析请求中的参数，这样客户端和授权服务器就以浏览器为媒介实现了通信，而不用直接交互。

> Web应用和原生应用都可以使用OAuth，但是都需要使用前端信道机制来接收授权端点返回的信息。前端信道通常需要使用到Web浏览器和HTTP重定向，但常规的Web服务器一般是不提供这些支持的。可以用内部Web服务器、应用专有的URI方案、使用后端服务向客户端推送通知等。只要能触发浏览器对该URI的调用即可。

所有通过前端信道传递的信息都可供浏览器访问，既能被读取，也可能在最终请求发出之前被篡改。OAuth协议已经考虑到了这一点，它限制了能通过前端信道传输的信息类别，并确保只要是通过前端信道传输的信息，就不能在授权任务中单独使用。上面的典型案例中，授权码不能被浏览器直接使用，相反它必须通过后端信道与客户端凭据一并出示。这有些协议中，比如OpenID Connect，要求客户端或授权服务器对前端信道中传输的消息签名，通过这样的安全机制增加一层保护。

### 小结

虽然OAuth协议包含很多移动组件，但它将一些简单的操作组合起来，形成了一套安全的授权方法。

- OAuth是关于获取令牌和使用令牌的
- OAuth系统中的不同组件各自负责授权流程中的不同环节
- 组件使用直接的后端信道和间接的前端信道HTTP链接相互通信

## 第三章：构建简单的OAuth客户端

OAuth协议的焦点在于客户端如何获取令牌，以及如何使用令牌代表资源拥有访问受保护资源。在本章建立一个简单的OAuth客户端，使用授权码许可类型从授权服务器获取bearer令牌，并使用该令牌访问受保护资源。

### 向授权服务器注册OAuth客户端

OAuth客户端和授权服务器需要互相了解才能通信，OAuth协议本身不关心如何实现这一点。OAuth客户端由一个称为“客户端标识符”的特殊字符串来标识。OAuth协议的多个组件被称为`client_id`，在给定一个授权服务器下，每个客户端的标识必须 唯一，因此，客户端标识几乎总是由授权服务器分配，这种分配可以通过开发者门户来完成，也可以使用动态客户端注册。

实例使用手动配置

```javascript
// client.js
// 授权服务器给客户端分配好了标识符以及共享密钥。另外还有redirect_uri、要请求的权限范围集合和其他选项由客户端软件设定
const client = {
  "client_id": "oauth-client-1",
  "client_secret": "oauth-client-secret-1",
  "redirect_uris": ["http://localhost:9000/callback"]
}

// 授权端点与令牌端点
const authServer = {
  authorizationEndpoint: 'http://localhost:9001/authorize',
  tokenEndpoint: 'http://localhost:9001/token'
}

```

### 使用授权码许可类型获取令牌

OAuth客户端要从授权服务器获取令牌，需要资源拥有者以某种形式授权。下面使用一种被称为授权许可类型的交互授权形式，由客户端将资源拥有者重定向至授权服务器的授权端点，然后，服务器通过`redirect_uri`将授权码返回给客户端。最后，客户端将受到的授权码发送到授权服务器的令牌端点，换取OAuth访问令牌，再进行解析和存储。

#### 发送授权请求

```javascript
/**
 * @param {String} base
 * @param {Object} options
 * @returns {String} 函数接收一个URL基础和一个对象，对象包含所有要添加到URL中的查询参数。
*/
const buildUrl = (base, options, hash) => {
  const newUrl = url.parse(base, true);
  delete newUrl.search;
  if (!newUrl.query) {
    newUrl.query = {}
  }
  __.each(options, (value, key, list) => {
    newUrl.query[key] = value;
  })
  if (hash) {
    newUrl.hash = hash;
  }

  return url.format(newUrl)
}


app.get('/authorize', (req, res) => {
  const authorizeUrl = buildUrl(authServer.authorizationEndpoint, {
    response_type: 'code',
    client_id: client.client_id,
    redirect_uri: client.redirect_uris[0]
  })

  res.redirect(authorizeUrl);
})
```

#### 处理授权响应

```javascript
const encodeClientCredentials = (clientId, clientSecret) => {
  return new Buffer(querystring.escape(clientId) + ':' + querystring.escape(clientSecret).toString('base64'));
}


// 查看传入参数，并从code参数中读取授权服务器返回的授权码，授权服务器通过重定向让浏览器向客户端发起请求，而不是直接响应客户端请求
app.get('/callback', (req, res) => {
  const { query: { code } } = req;

  const form_data = qs.stringify({
    grant_type: 'authorization_code',
    code,
    // 根据OAuth规范，如果在授权请求中重定向了URI，在令牌请求中也
    // 必须包含该URI,可以防止攻击者使用被篡改的重定向URI获取受害人授权码
    // 让并无恶意的客户端将受害用户的资源访问权限关联到攻击者账号
    redirect_uri: client.redirect_uris[0]
  })

  // HTTP基本认证,Authorization 头部是一个base64 编码的字符串，编码内容是拼接后的用户名和密码，以冒号隔开。
  // OAuth2.0要求将客户端ID作为用户名，将客户端密钥作为密码，但是用之前应该先对它们进行URL编码
  const headers = {
    'Content-Type': 'application/x-www-form-urlencoded',
    'Authorization': `Basic ${encodeClientCredentials(client.client_id, client.client_secret)}`
  }

  // 使用POST请求将这些信息传送到服务器的授权端点
  const tokRes = request('POST', authServer.tokenEndpoint, {
    body: form_data,
    headers
  })


  // 请求成功，授权服务器将返回一个包含访问令牌以及其他信息的JSON对象：
  // {"access_token":"987tghjkiu6trfghjuytrghj","token_type":"Bearer"}
  // 应用需要读取结果并解析JSON对象，获取访问令牌，保存起来
  const body = JSON.parse(tokRes.getBody());
  access_token = body.access_token;

  res.send('index', {
    access_token: body.access_token
  })
})
```

#### 使用state参数添加跨站保护

每当有人访问 `localhost:9000/callback`，客户端会接收收到的code值，并试图发送给授权服务器。这意味着攻击者有可能会用客户端向授权服务器暴力搜索有效授权码，浪费客户端和授权服务器资源，而且还有可能导致客户端获取一个从未请求过的令牌

可以使用名为state的可选OAuth参数缓解这个问题，将该参数设置为一随机值，应在应用中用变量保存，在丢弃旧的访问令牌后，创建一个state值

> state = randomstring.generate();

需要将值保存起来，因为当通过回调访问 redirect_uri时，还要用到这个值。由于此阶段使用前端信道通信，因此重定向至授权端点的请求一旦发出，客户端应用就会放弃对OAuth协议流程的控制，直到该回调发生，还需要将state添加到通过授权端点URL发送的参数列表中

```javascript
  const authorizeUrl = buildUrl(authServer.authorizationEndpoint, {
    response_type: 'code',
    client_id: client.client_id,
    redirect_uri: client.redirect_uris[0],
    state
  })
```

当授权服务器收到一个带有state参数的授权请求时，必须总是该state参数和授权码一起原样返回客户端。意味着可以检查传入 `redirect_uri`页面的state值，并与之前保存的值对比，如果不一致，则向最终用户提示错误

```javascript
  const {
    query: {
      code,
      error,
      state: cbState
    }
  } = req;

  if (error) {
    res.render('error', { error })
    return;
  }

  if (cbState !== state) {
    console.log(`State DOES NOT MATCH: expected ${cbState} got ${state}`)
    res.render('error', { error: 'State value did not match' })
    return;
  }
```

如果state与所期望的值不一样，可能是会话固化攻击、授权码暴力搜索或者其他恶意行为。此时客户端会终止所有授权去请求处理，并向用户展示错误页面

### 使用令牌访问受保护资源

受保护资源接收到有效的令牌后，会返回一些有用信息。客户端要做的就是使用令牌向保护资源发出调用请求，有3个合法的位置可用于携带令牌。在客户端中，使用HTTP Authorization头部来传递令牌。

> 得到的这种访问令牌叫做bearer令牌，它意味着无论是谁，只要持有就可以向保护资源出示，OAuth bearer令牌使用规范明确给出了发送令牌值的3种方法：
>
> 使用HTTP Authorization 头部、使用表单格式的请求体参数、使用URL编码的查询参数
>
> 由于另外两种方法存在一定的局限性，因此建议尽可能使用Authorization头部。在使用查询参数时，访问令牌的值有可能被无意地泄露到服务端日志中吗，因为查询参数是URL请求的一部分；使用表单的方式，会限制受保护资源只能接受表单格式的输入参数，并且要使用POST方法。如果要API已经按这样的限制运行了，那这种方法没有问题，毕竟不会面临与查询参数方法一样的安全局限。
>
> 使用Authorization头部是这3种方法中最灵活和最安全的。由于对客户端来说，使用起来很困难。一个健壮的OAuth客户端或者服务端库应该完整地提供这3种方式，以适应不同情况。

```javascript
app.get('/fetch_resource', (req, res) => {
  // 确认是否已拥有访问令牌，没有需要向用户提示错误并退出
  if (!access_token) {
    res.render('error', {
      error: 'Missing access token.'
    })
    return;
  }
})
```

请求受保护资源，并将获取的响应数据渲染到页面，protectedResource变量设置了一个URL,将向该URL发送请求并期待返回JSON响应。使用OAuth定义的Authorization头发送令牌，将令牌设置为这个头部的值

```javascript
app.get('/fetch_resource', (req, res) => {
  // 确认是否已拥有访问令牌，没有需要向用户提示错误并退出
  if (!access_token) {
    res.render('error', {
      error: 'Missing access token.'
    })
    return;
  }

  const headers = {
    Authorization: `Bearer ${access_token}`
  }
  const resource = require('POST', protectedResource, {
    headers
  })

  if (resource.statusCode >= 200 && resource.statusCode < 300) {
    const body = JSON.parse(resource.getBody());
    res.render('data', {
      resource: body
    })
    return;
  } else {
    res.render('error', {
      error: `Server returned response code: ${resource.statusCode}`
    })
    return;
  }
})
```

### 刷新访问令牌

OAuth2.0提供了一种无须用户参与的情况下最新访问令牌的方式：刷新令牌。用户在初次授权完成之后不会一直在场，而OAuth经常要在这样的情况下使用。

客户端如何才能知道自己的访问令牌是否有效，唯一的方法就是使用它，然后看结果。如果令牌具有预设的过期时间，授权服务器可以在令牌响应中使用一个可选的`expires_in`字段来表示预设的有效期。这是一个从令牌发放到预设失效时间之间的秒数值，一个中规中矩的客户端应该会关注这个值，并将过期的令牌丢弃掉。

然后，仅仅指定过期时间还不足以让客户端掌握令牌的状态。很多OAuth实现中，资源拥有者可以在令牌过期之前将其撤销。一个设计良好的客户端应该始终能预料到访问令牌可能随时突然失效，并能做出反应。

可以提示用户重新授权并获取一个新的令牌，刷新令牌最初是与访问令牌在同一个JSON对象中返回给客户端的：

```json
{
    "access_token": "987tghjkiu6trfghjuytrghj",
    "token_type": "Bearer",
    "refresh_token": "j2r3oj32r23rmasd98uhjrk2o3i"
}
```

客户端将刷新令牌保存在 `refresh_token`变量中。授权服务器启动前先清空数据库，再将刷新令牌自动插入数据库。没有插入对应的访问令牌，模拟访问令牌失效但刷新令牌仍有效

```javascript
let access_token = '987tghjkiu6trfghjuytrghj';
let scope = null;
let refresh_token = 'j2r3oj32r23rmasd98uhjrk2o3i';

if(resource.statusCode >=2000 && resource.statusCode < 300){
    const body = JSON.parse(resource.getBody());
    res.render('data',{resource:body})
    return;
}else{
    access_token = null;
    if(refresh_token){
        refreshAccessToken(req,res)
        return;
    }else{
        res.render('error',{error:resource.statusCode});
        return;
    }
}
// 向令牌端点发起一个请求，刷新访问令牌是授权许可的一种特殊情况
function refreshAccessToken(){
    // 使用refresh_token作为grant_type参数的值，刷新令牌也作为参数包含在其中
    const form_data = qs.stringfy({
        grant_type:'refresh_token',
        refresh_token
    })
    const header = {
        'Content-Type':'application/x-www-form-unlencoded',
        'Authorization':`Basic ${encodeClientCredentials(client.client_id,client.client.secret)}`
    }
    const tokRes = requst('POST',authServer.tokenEndpoint,{
        body:form_data,
        headers,
    })
    if(tokRes.statusCode >=200 && tokRes.statusCode < 300){
        const body = JSON.parse(tokRes.getBody)
        access_token = body.access_token;
        if(body.refresh_token){
            refresh_token = body.refresh_token
        }
        scope = body.scope;
        // 重新获取受保护资源
        res.redirect('/fetch_resource')
        return;
    }else{
        // 如果刷新令牌失效，则将刷新令牌与访问领票都丢弃掉，并渲染一个错误提示
        console.log('NO refresh token,asking the user to get a new access token');
        refresh_token = null;
        res.render('error',{
            error:'Unable to refresh token.'
        })
        return;
    }
}
```

### 小结

Oauth客户单是OAuth生态系统中使用最广泛的部分：

- 使用授权码许可类型获取令牌只需要几个简单的步骤
- 如果刷新令牌可用，则可以使用它获取新的访问令牌，而不需要用户参与
- 使用Oauth2.0的bearer令牌比获取令牌更简单，只需要将一个简单HTTP头部添加到所有HTTP请求中即可

## 第四章：构建简单的OAuth受保护资源

受保护资源，供客户端用访问令牌调用。对于大多数基于Web的API,增加OAuth安全层是一个轻量级的过程。资源服务器需要做的就是传入的HTTP请求中解析出OAuth令牌，验证该令牌，并确定它能用于哪些请求。

尽管受保护资源和授权服务器在概念上是OAuth系统的不同组件，但许多OAuth实现将二者放在一起。这种做法在两个系统耦合紧密下的情况下适用。下面的例子，会在同一台机器使用独立进程运行受保护资源，但是它能够访问授权服务器所用的数据库。

### 解析HTTP请求中的OAuth令牌

受保护资源接受OAuth bearer令牌，因为授权服务器生成的就是bearer令牌，OAuth bearer令牌使用规范定义了3种受保护传递 bearer令牌的方法：使用HTTP Authorization 头部、使用表单参数以及使用查询参数。首选 Authorization头部。

由于要在多个资源URL上执行此操作，会使用一个辅助函数来检查令牌。Express的方法第三个参数是 next，next是一个函数可以调用它来继续处理请求，使得可以使用多个函数处理单个请求，并把令牌检查功能添加到整个应用的请求处理流程中

```javascript
const getAccessToken = (req,res,next)=>{
    // ...
}
```

OAuth bearer令牌使用规范规定，在使用HTTP Authorization头部传递令牌时，HTTP头的值以关键字 Bearer开头，后跟一个空格，再跟令牌值本身。而且，OAuth 规范还规定了 Bearer关键字不区分大小写。此外，HTTP规范还规定了Authorization头部关键字本身不区分大小写。这意味着下面的所有HTTP头都是等价的

```http
Authorization: Bearer xxx
Authorization: bearer xxx
authorization: BEARER xxx
```

首先，尝试从请求中获取Authorization头部，然后检查是否包含OAuth bearer令牌，由于express自动将所有HTTP头名称转为小写，我们使用字符authorization检查传入的请求对象，还要在头部值转为小写后检查关键字 bearer

```javascript
let inToken = null;
const auth = req.header['authorization'];
if(auth && auth.toLowerCase().indexOf('bearer') === 0){
    inToken = auth.slice('bearer '.length);
}else if(req.body && req.body.access_token){
    inToken = req.body.access_token;
}else if(req.query&&req.query.access_token){
    inToken = req.query.access_token;
}
```

检查通过，将头部的bearer关键字和后面的空格去掉，获取令牌值。令牌值本身不区分大小写的，所以要从从初始字符串提取令牌，而不是转换之后的字符串中提取

处理通过表单参数传递令牌，表单参数在请求主体中。OAuth规范不推荐这种方法，因为它认为限制了API的输入只能是表单形式。如果API的本来输入载体是JSON格式，那么客户端就无法在请求主体中加入令牌。在这种情况下，使用Authorization头部才是首选。但是对于那些输入载体就是表单格式的API，这种方法既简单又能和API保持一致，而且不需要处理Authorization头部。

最后一种方法是通过查询参数传递令牌。建议在其他两种方法不能使用的时候才采用该方法。使用这种方法，访问令牌很可能被无意地记录在服务器访问日志中或者通过HTTP Referrer头泄露，它们会整体复制URL。然而，有时候客户端应用无法直接访问HTTP Authorization头部（受限于平台或库），也不能使用表单参数（比如HTTP GET 方法）。另外，这种方法不仅可以在URL中包含资源本身的定位符，而且还可以包含访问方法。在这些情况下，只要有适当的安全措施，OAuth运行客户端通过查询参数来传递令牌。

### 根据数据存储验证令牌

我们可以访问授权服务器用于存储令牌的数据库，这是小型OAuth系统中常用的配置方案，这样的系统将授权服务器与受保护的API放在一起。

例子的授权服务器使用了一个NoSQL数据库，它将数据库存储在磁盘上的文件中，通过一个简单Nodejs模块来访问。如果想实时查看程序运行时数据库的内容，可以监控联系目录的database.sql文件。

根据传入的令牌值执行简单的查找，从数据库中找出访问令牌，服务器将每一个访问令牌和刷新令牌分别作为单独的元素存储在数据中，所以只需要使用数据库的查询功能找出正确的令牌即可。查询函数的细节对于NoSQL数据库来说是特有的，但是其他数据库也会提供类似的查询方法

```javascript
nosql.one((token)=>{
    if(token.access_token === inToken){
        return token;
    }
},(err,token)=>{
    if(token){
        console.log(`We found a matching token: ${inToken}`)
    }else{
        console.log('No matching token was found.')
    }
    req.access_token = token;
    next();
    return;
})
```

传入的第一个函数将获取令牌与数据库中的访问令牌进行对比，如果发现匹配项，就会停止搜索并返回令牌。第二个函数会在发生匹配的令牌时或者数据库遍历到尽头被调用。如果在数据库找到令牌，它会被作为token参数传入，否则为null。无论找到什么，都将它赋值个req对象的`access_token`成员，然后调用next函数，req对象会自动传递给处理函数的下一个处理步骤

返回的令牌对象与授权服务器在生成令牌时插入数据库的对象完全相同，例如，示例授权服务器会像下面这样将访问令牌以及权限范围保存在下一个JSON对象中

```json
{
    access_token:'xxx',
    client_id:'xx',
    scope:['xxx']
}
```

> 使用共享数据是一种非常常见的OAuth部署模式，但不是唯一的选择。有一个叫做令牌内省（token introspection）的Web协议，它可以有授权服务器提供接口，让资源服务器可以在运行时检查令牌的状态。这使得资源服务器可以像客户端那样将令牌本身视为不透明的。代价是使用而更加的网络流量。还有另一种方式：可以在令牌内包含受保护资源能够直接解析并理解的信息。JWT就是这样的一种数据结构，它可以使用受加密保护的JSON对象携带声明信息。
>
> 是否必须将令牌以原始值存储在数据中，虽然这是一种简单且常见的做法，也可以存储令牌的散列值，这种方式类似存储用户密码，在查询令牌时，要将令牌再次进行散列计算，并同数据库中内容进行比较。还可以将唯一标识符添加到令牌中，并使用服务器的密钥对它签名。在数据库中只存储这唯一的标识符。当需要查找这个令牌的时候，资源服务器可以验证签名，解析令牌得到标识符，然后在数据库中查找这个标识符对应的令牌信息。

接入服务，在Express应用中，有两个选择，一个是用于每个请求，二是只将它用于需要检查OAuth令牌的请求。为了将这一处理应用到每个请求，需要设置一个新的监听器。将令牌检查函数链接到处理流程中。令牌检查函数需要在路由中其他所有函数之前连接，因为这些函数是按照在代码中被添加的顺序来执行的。

```javascript
app.all('*',getAccessToken)
```

另外可以将新函数插入已有的处理函数设置，让新函数先被调用。

```javascript
app.post('/resource',(req,res)=>{})
```

要让令牌处理函数先被调用，需要在路由的处理函数定义之前添加函数

```javascript
app.post('/resource',getAccessToken,(req,res)=>{})
```

当路由处理函数被调用时，请求对象会附加一个`access_token`成员，如果令牌被找到，这个字段就会包含从数据库中取出的令牌对象。如果令牌未被找到，这个字段就会是null,需要根据情况判断

```javascript
if(req.access_token){
    res.json(resource)
}else{
    res.status(401).end()
}
```

### 根据令牌提供内容

很多API设计中，不同的操作需要不同访问权限，还有一些API会根据授权者不同而返回不同的结果，或者根据不同权限返回某一部分信息。加一个 `requireAccessToken`处理函数，会在令牌不存在时直接返回错误，在令牌存在时将控制权交给最终处理函数进行后续处理

```javascript
const requireAccessToken = (req,res,next)=>{
    if(req.access_token){
        next();
    }else{
        res.status(401).end();
   }
}
```

#### 不同权限对应不同的操作

不同类型的操作需要不同的权限范围，才能使调用成功。这使得资源服务器可以根据客户端能执行的操作来划分功能。这也是单个授权服务器对应的多个资源服务器之间使用单个令牌的常用方法。

客户端有一个页面提供访问资源API的所有功能，读取显示并带上时间戳，用于资源服务器上添加新单词，删除功能等

应用中注册了三个路由，分别对应不同的动作，只要传入的令牌有效，无论什么类型，都会执行

```javascript
app.get('/words',getAccessToken,requireAccessToken,(req,res)=>{
    res.json({
        words:saveWords.json(' '),
        timestamp:Date.now()
    })
})

app.post('/words',getAccessToken,requireAccessToken,(req,res)=>{
    if(req.body.word){
        saveWords.push(req.body.word)
    }
    res.status(201).end();
})

app.delete('/words',getAccessToken,requireAccesToken,(req,res)=>{
    saveWords.pop()
    res.status(204).end;
})
```

现在，逐个修改它们，确保令牌中至少包含与各个功能对应的权限范围，鉴于在数据库中存储方式，需要获取令牌对应的`scope`成员，对于GET功能，我们需要客户端拥有与之对一个的read权限范围，客户端还可以拥有其他权限范围

```javascript
app.post('/words', getAccessToken, requireAccessToken, (req, res) => {
	if (__.contains(req.access_token.scope, 'write')) {
		if (req.word.word) {
			saveWords.push(req.body.word)
		}
		res.status(201).end()
	} else {
		res.set('WWW-Authenticate', 'Bearer realm=localhost:9002,error="insufficient_scope",scope="write"');
		res.status(403)
	}
})
```

使用 `WWW-Authenticate`头部返回错误，告诉客户端该资源需要接受一个OAuth bearer令牌，并且令牌中至少要包含read权限范围，才能调用成功。在另外两个函数中加入类似的代码，也会检查write,delete的权限范围，在任何情况下，即使令牌有效，只要权限范围不正确，就会返回错误：

```javascript
app.post('/words', getAccessToken, requireAccessToken, (req, res) => {
	if (__.container(req.access_token.scope, 'write')) {
		if (req.word.word) {
			saveWords.push(req.body.word)
		}
		res.status(201).end()
	} else {
		res.set('WWW-Authenticate', 'Bearer realm=localhost:9002,error="insufficient_scope",scope="write"');
		res.status(403)
	}
})


app.delete('/words', getAccessToken, requireAccessToken, (req, res) => {
	if (__.container(req.access_token.scope, 'delete')) {
		saveWords.pop()
		res.status(204).end()
	} else {
		res.set('WWW-Authenticate', 'Bearer realm=localhost:9002,error="insufficient_scope",scope="delete"');
		res.status(403)
	}
})
```

这样一来，要为客户端指定不同的权限范围组合，需要重新对客户端应用授权

#### 不同的权限范围对应不同的数据结果

在这种风格的API设计中，同一个处理函数可以根据传入的令牌中包含的权限范围不同，而返回不同类别的信息。如果数据结构复杂，且希望通过同一个API端点为客户端提供多种信息子集的访问，这样设计就非常有用。

在受保护资源没有为不同的农产品类别提供多个独立的处理函数，而是在一个处理函数中处理对所有农产品的请求，这个处理函数返回的对象中包含所有种类的农产品列表

```javascript
app.get('/produce',getAccessToken,requireAccessToken,(req,res)=>{
    const produce = {
        fruit:['apple','banana','kiwi'],
        veggies:['lettuce','onion','potato'],
        meats:['bacon','steak','chicken breast']
    }
    res.json(produce)
})
```

在做修改之前，使用有效的令牌访问该API,会得到包含所有农产品的列表，如果对客户端授权让它得到访问令牌，但是不勾选任何权限范围，就会得到所有数据。

切分数据结构，将数据片段放入控制语句，检查每个农产品类别的权限范围

```javascript
const produce = {
    fruit:[],
    veggies:[],
    meats:[]
}
if(__.contains(req.access_token.scope,'fruit')){
    Object.assign(produce,{fruit:['apple','banana','kiwi']})
}

if(__.contains(req.access_token.scope,'veggies')){
    Object.assign(produce,{fruit:['lettuce','onion','potato']})
}

if(__.contains(req.access_token.scope,'meats')){
    Object.assign(produce,{fruit:['bacon','steak','chicken breast']})
}
```

#### 不同用户对应不同的数据结果

同一个处理函数可以根据授权客户端的用户不同的信息。这是一种常见的API设计方式，使得客户端应用在不知道用户是谁的情况下，调用同一个URL也能获取个性化的结果。虽然客户端与受保护资源之间建立的连接上并没有资源拥有者的登录或者身份认证信息，但是生成的令牌中包含资源拥有者的信息，资源拥有者需要在授权批准的环节进行身份认证。

> 授权服务器的批准页面会选择替哪个用户授权，通常这一步是通过授权服务器对资源拥有者进行身份认证来完成的，而且一般认为运行一个未经身份认证的用户随意冒充任何人是极不安全的做法。

我们要做的就是根据授权者是谁来返回对应的数据，授权服务器已经讲资源拥有者的用户名存在访问令牌记录的user字段中，根据这个字段确定返回内容

```javascript

app.get('/favorites', getAccessToken, requireAccessToken, (req, res) => {
	const {
		access_token: {
			user
		}
	} = req;
	const unknown = { user: 'Unknown', favorites: { movies: [], foods: [] } }
	switch (user) {
		case 'alice':
			res.json({ user: 'Alice', favorites: aliceFavorites })
			break;
		case 'bob':
			res.json({ user: 'Bob', favorites: bobFavorites })
		default:
			res.json(unknown)
	}
})
```

在授权服务器上以Alice或者Bob名义授权了客户端，就会在客户端上得到他们的个性化数据。

在OAuth处理流程中，客户端绝不知道与之交互的是Alice还是Bob或者其他的。客户端只是碰巧知道了Alice的名字，因为它调用的API的响应包含了她的名字，而这个人信息也很容易被去掉。这是一个重要的设计模式，因为它可以避免不必要的暴露资源拥有者的个人身份信息，从而保护隐私。如果与分享信息的API结合起来，可以用OAuth构建一个身份认证协议。

#### 额外的访问控制

使用OAuth能对受保护资源实现的访问控制远不止上面那些，而且当今使用OAuth的受保护资源都有各自的应用模式。因此，OAuth并不插手授权决策的过程，而只通过使用令牌和权限范围充当授权信息的载体。这样的设计思路使得OAuth广泛应用于互联网上各种类型的API.

资源服务器可以根据令牌及其附属信息（如权限范围）直接作出授权决策。资源服务器还可以将访问令牌中的权限范围与其他访问控制信息结合起来，用于决定是否响应API调用已经响应什么内容。例如,资源服务器可以限制特定的客户端和用户只能在特定的时间段内访问资源，无论令牌是否有效。资源服务器甚至可以以令牌作为输入，调用外部策略引擎，以实现组织内对复杂授权规则的集中管理。

在任何情况下，资源服务器都对访问令牌的含义拥有最终决定权，不管资源服务器外包了多少决策过程，最终都由它来决定如何处理给定请求。

### 小结

使用OAuth保护Web Api非常简单：

- 从传入的请求中解析出令牌
- 通过授权服务器验证令牌
- 根据令牌的权限范围作出响应，令牌的权限范围有多种。

## 第五章：构建简单的OAuth授权服务器

上面构建了OAuth客户端应用，可以从授权服务器获取令牌，并使用令牌访问受保护资源，还构建了一个供用户访问的受保护资源，下面构建了一个简单的授权服务器，支持授权码许可类型，这个组件要管理客户端，执行OAuth核心的授权操作，还要向客户端颁发令牌

授权服务器是OAuth生态系统中最复杂的组件，是整个OAuth系统的安全权威中心。只有授权服务器能够给用户进行身份认证，注册客户端，颁发令牌。在OAuth2.0规范的制定过程中，已经尽可能将复杂性从客户端和受保护资源转移到授权服务器。这很大程度是由各个组件的数量决定的：客户端的数量远多于受保护资源的数量，受保护资源的数量又远多于授权服务器的数量。

### 管理OAuth客户端注册

