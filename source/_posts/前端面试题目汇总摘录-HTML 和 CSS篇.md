---
title: 前端面试题目汇总摘录（HTML 和 CSS篇）
date: 2018-10-15  11:54:54
tags: 前端面试题
categories: 
	- 前端面试
---




温故而知新，保持空杯心态
## HTML 和 CSS

### 你做的页面在哪些浏览器测试过？这些浏览器的内核分别是什么



| 浏览器名称      | 内核                                                         |
| --------------- | ------------------------------------------------------------ |
| IE              | trident                                                      |
| Firefox（火狐） | gecko                                                        |
| Safari          | webkit                                                       |
| Opera           | 以前是 presto ，现在已改用 Google Chrome 的 Blink 内核       |
| Chrome（谷歌）  | Blink（基于 webkit，[Google 与 Opera Software 共同开发](https://baike.baidu.com/item/%E6%B5%8F%E8%A7%88%E5%99%A8%E5%86%85%E6%A0%B8)） |

### 每个 HTML 文件里开头都有个重要的东西， Doctype,知道这个是干什么的吗？

<! DOCTYPE> 声明位于文档中的最前面的位置，处于 `<html>` 标签之前，此标签可以告知浏览器文档使用哪种 HTML 或 XHTML 规范。（重点：告诉浏览器按照哪种规范解析页面）



### Quirks 模式是什么？它和 Standards 模式有什么区别？

从 IE6 开始，引入了 Standards 模式，标准模式中，浏览器尝试符合标准的文档在规范上的正确处理达到在指定浏览器中的程度。

在IE6 之前 CSS 还不够成熟，所以 IE5 等之前的浏览器对 CSS 的支持很差，IE6 将对 CSS 提供更好的支持，然后这时问题就来了，因为有很多页面是基于旧的的布局方式写的，如果 IE6 支持 CSS 则将这些页面显示不正常，如何在即保证不破坏现有页面，又提供新的渲染机制？

在写程序时我们也会经常遇到这样的问题，如何保证原来的接口不变，提供更强大的功能，尤其是新功能不兼容旧功能的时候。遇到这种问题时的一个常见的做法就是增加参数和分支，即当某个参数为真，我们就使用新的功能，如果这个参数不为真的时候，就使用旧的功能，这样就可以不破坏原来的程序，又能提供新的功能。IE6 也是类似这样做，它将 DTD  当成了 这个参数，因为当前的页面大家都不会去写 DTD ，所以 IE6 就假定如果写了 DTD 就意味着这个页面将采用对 CSS 支持更好的布局，而如果没有，就采用兼容之前的布局方式，这就是 Quirks 模式（怪癖模式，诡异模式，怪异模式）

实例：

```html
<!DOCTYPE html>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN""http://www.w3.org/TR/html4/strict.dtd" >
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
```

第一种：表明该页面是遵守了 HTML5 规范的，浏览器会选择 Standards Mode ，这种 doctype 是最推荐的一种。

第二种：浏览器同样会选择 Standards Mode ,虽然第一种 doctype 有一些区别，但几乎可以认为是一样的

第三种：浏览器会选择 Almost Standards Mode ，需要注意的是乳沟今后需要把这个页面重新改写成 HTML5 规范，那么 table 标签中的分割图片问题可能会被错乱。

当 doctype 缺失（不注明、写错）时候，浏览器会选择 Quirks Mode,这是非常不推荐的方式，我们应该尽量避免 Quirks Mode

区别：

总体会有布局、样式解析和脚本执行三个方面的区别。

1. 早期的 IE 浏览器（IE 6 以前）将盒子的 padding 和 border 算到了盒子的尺寸中，这一模型被称为 IE 盒模型。在 IE 盒模型中

   1. box width = content width + padding left + padding right + border left + border right

      box width = content width + padding left + padding right + border left + border right

   而在 W3C 标准的盒模型中，box 的大小就是 content 的大小

   1. box width = content width，box height = content height

   这一区别将导致页面绘制时所有的块级元素都出现很大的差别，所以两种不同的文档模式下的页面也区别很大。

2. 设置行内元素的高宽，在 Standards 模式下，给 `<span>` 等行内元素设置 width 和 height 都不会生效，而在 quirks 模式下，会生效。

3. 设置百分比的高度，在 Standards 模式下，一个元素的高度是由其包含的内容来决定的，如果父元素没有设置百分比的高度，子元素设置了一个百分比的高度是无效用的。

4. margin:0 auto 设置水平居中，使用 margin:0 auto 在 standards 模式下可以使元素水平 居中，但在 quirks 模式下却会失效。

5. 在Quirks Mode下，为body设置一个margin是无效的。

6. 默认情况下，IE有一个垂直滚动条，尽管当没有东西可以滚动的时候，它是非活动状（迟钝状态），在Quirks Mode下，你可以通过设置body { overflow: auto;}删除它（当不需它的时候），但是在标准模式下，你仍然需要增加html { overflow: auto;}

7. 默认的浮动图片的水平margin是3像素（而不是0）。

8. 字体属性不会从body或其他封闭元素继承到table中。特别是font-size。字体，颜色，行高也都有可能。

（还有很多，答出什么不重要，关键是看他答出的这些是不是自己经验遇到的，还是说都是
看文章看的，甚至完全不知道。）

### div+css 的布局较 table 布局有什么优点

- 改版的时候更方便，只要改css 文件
- 页面加载速度更快、结构化清晰、页面显示简洁
- 表现与结构相分离
- 易于优化（seo）搜索引擎更加友好，排名更容易靠前

### img 的 alt 与 title 有何异同？strong 与 em 的异同

alt （alt text）：为不能显示图像、窗体或 applets 的用户代理（UA），alt属性用来指定替换文字。替换文字的语言由lang 属性指定。（在 IE 浏览器下会在没有 title 时把 alt 当成 tool tip 显示）

title （tool tip）: 该属性为设置该属性的元素提供建议性的消息



strong：粗体强调标签，强调，表示内容的重要性

em：斜体强调标签，更强烈强调，表示内容的强调点

### 描述一下渐进增强与优雅降级之间的不同

渐进增强（progressive enhancement）：针对低版本浏览器进行构建页面，保证最基本的功能，然后再针对高级浏览器进行效果、交互等改进和追加功能达到更好的用户体验。

优雅降级 （graceful degradation）：一开始就构建完整的功能，然后再针对低版本浏览器进行兼容

区别：优雅降级是从复杂的现状开始，并试图减少用户体验的攻击，而渐进增强则是从一个非常基础的，能够器作用的版本开始，并不断扩充，以适应未来环境的需要。降级（功能衰减）意味着往回看；而渐进增强则意味着朝前看，同时保证其根基处于安全地带。

“优雅降级”观点

“优雅降级”观点认为应该针对那些最高级、最完善的浏览器来设计网站。而将那些被认为“过时”或有功能缺失的浏览器下的测试工作安排在开发周期的最后阶段，并把测试对象限定为主流浏览器前的一个版本。

在这种设计范例下，旧版的浏览器被认为仅能提供“简陋却无妨（poor,but passable）” 的浏览体验，可以做一些小的调整来适应某个特定的浏览器。但由于它们并非我们所关注的焦点，因此除了修复较大的错误之外，其他的差异将被直接忽略。

“渐进增强”观点

“渐进增强”观点则认为应关注于内容本身

内容是我们建立网站的诱因。有的网站展示它，有的则收集它，有点寻求，有的操作，还有的网站甚至会包含以上的种种，但相同点是它们全都涉及到了内容。这使得“渐进增强“成为了一种更为合理的设计范例。这也是它立即被 Yahoo! 所采纳并用以构建其”分级式浏览器支持“策略的原因所在

### 可以利用多个域名来存储网站资源会更有效？

- CDN 缓存更方便
- 突破浏览器并发限制
- 节约 cookie 带宽
- 节约主域名的连接数，优化页面响应速度
- 防止不必要的安全问题

### 对网页标准和标准制定机构重要性的理解

网页标准和标准制定机构都是为了能让 web 发展的更 “健康”，开发者遵循了统一的标准，降低开发难度，开发成本，SEO 也会更好做，也不会因为滥用代码导致各种 BUG 、安全问题,最终提高网站易用性。

### 描述一下 cookies，sessionStorage 和 localStorage 的区别

sessionStorage （session）中的数据，这些数据只有在同一个会话中的页面才能访问并且当会话结束后数据也随之销毁。因为 sessionStorage 不是一种持久化的本地存储，仅仅是会话级别的存储。而 localStorage 用于持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的。

web storage 和 cookie 的区别

Web Storage 的概念和 cookie 相似，区别是它是为了更大容量存储设计的。Cookie 的大小是受限的，并且每次你请求一个新的页面的时候 Cookie 都会被发送过去，这样无形中浪费了带宽，另外 cookie 还需要指定作用域，不可以跨域调用。

除此之外，Web Storage 拥有 setItem,getItem,removeItem，clean 等方法，不像 cookie 需要前端开发者自己封装 setCookie,getCookie，但是 Cookie 也不是不可以或缺的：Cookie 的作用是与服务器进行交互，作为 HTTP 规范的一部分存在，而 Web Storage 仅仅是为了在本地“存储”数据而生。

前端设置用户设置，获取，清空 Cookie 

```javascript
// 写 cookies 
function setCookie(){
    var Days = 30;
    var exp = new Date();
    exp.setTime(exp.getTime() + Days*24*60*60*1000);
    document.cookie = name + "=" + encodeURIComponent(value) + "；expires=" + exp.toGMTString();
}

// 读取 cookies
function getCookie(name){
    var arr,reg = new RegExp("(^|)" + name + "+([^;]*)(;|$)");
    if(arr.document.cookie.match(reg)
    	return decodeURIComponent(arr[2]);
    else
        return null;
}

//删除 cookies
function delCookie(name){
    var exp = new Date();
    exp.setTime(exp.getTime() - 1);
    var cval = getCookie(name);
    if(!cval = null){
       document.cookie = name + "=" + cval + ";expires" + exp.toGMTString();
     }
}
```

### 简述一下 src 与 href 的区别

src 用于替换当前元素， href 用于在当前文档和应用资源之间建立联系。

src 是 srouce 的缩写，指向外部资源的位置，指向的内容和将会嵌入到文档中当前标签所在位置，在请求 src 资源时会将其指向的资源下载并应用到文档内，例如 js 脚本，img 图片和frame 等元素。

```javascript
<script src = "js.js"></script>
```

当浏览器解析到该元素时，会暂停其他资源的下载和处理，直到将该资源加载、编译、执行完毕，图片和框架等元素也是如此，类似于将所指向资源嵌入当前标签内，这也是为什么将js脚本放在底部而不是头部。

href 是 Hypertext Reference 的缩写，指向网络资源所在的位置，建立和当前元素（锚点）或当前文档（链接）之间的链接，如果在文档中添加

```javascript
<link href = "common.css" rel="stylesheet"/>
```

那么浏览器会识别该文档为 css 文件，就会并行下载资源并且不会停止对当前文档的处理。这也是为什么建议使用link 方式来加载 css,也不是使用 @import 方式

### 网页制作会用到的图片格式有哪些？

png-8,png-24,jpeg,gif,svg

希望听到的是 Webp(是否有关注新鲜技术)，WebP,谷歌开发的一种旨在加快图片加载速度的图片格式。图片压缩体积大约只有 JPEG 的 2/3 ，并能节省大量的服务器带宽资源和数据空间。Facebook Ebay 等知名网站已经开始测试并使用 WebP 格式。在质量相同的情况下，WebP格式图像的体积要比 JPEG 格式图像小 40%

支持情况：谷歌浏览器已经支持webp格式，Opera在版本号Opera11.10后也增加了支持，然而火狐和ie暂时还不支持webp格式，可以采用flash插件来显示webp，当然这样会耗费一些性能。

美中不足的是，WebP格式图像的编码时间“比JPEG格式图像长8倍”。

详细可以点击 [都说 WebP 厉害，究竟厉害在哪里？](https://www.cnblogs.com/upyun/p/7813319.html) 

### 知道什么是微格式(microformat)？谈谈理解，在前端构建中应该考虑微格式吗？

微格式（Microformats） 是一种让机器可读的语义化 XHTML 词汇的集合，是结构化数据的开放标准。是为特殊应用而制定的特殊格式。

优点：将智能数据添加到网页上，让网站内容在搜索引擎结果界面可以显示额外的提示。（应用范例：豆瓣）

#### 微格式在实际应用中的意义和作用

1. 在捉取Web内容时，能够更为准确地识别内容块的语义；
2. 对内容进行操作，包括提供访问、校对，还可以将其转化成其他的相关格式，提供给外部程序和Web服务使用

实例：

```html
// 以前写链接到首页的代码
<a href = "http://laibh.top">赖同学</a>
// 现在为a标签加上rel属性,标记a包括rel=”homepage”属性，该属性显示链接的目标页面是该网站的首页。通过为已有的链接元素添加语义化属性，就为这个链接添加了具体的结构和意义。
<a href = "http://laibh.top" rel = "homepage">赖同学</a>
```

#### hCard 微格式

hCard是一种微格式，用来发布个人，公司，组织，地点等详细的联系信息。 它可以使分析器（比如其他网站，Firefox的Operator插件）获得详细的信息，并通过别的网站或者地图工具进行显示，或者载入到地址簿等其他程序。

实例：

```html
<div>
    <div>赖同学</div>
	<div>某某公司</div>
	<div>电话：xxx-xxx-xxx</div>
	<a href = "http://laibh.top">赖同学的网站<a>
</div>
        
// 加入微格式后       
<div class = "vcard">
    <div class = "fn">赖同学</div>
	<div class = "org">某某公司</div>
	<div class = "tel">电话：xxx-xxx-xxx</div>
	<a class = "url" href = "http://laibh.top">赖同学的网站<a>
</div>
```

这里，正式名称（class=”fn”），组织（class=”org”），电话号码（class=”tel”）和url（class=”url”）分别用相应的class标示；同时，所有内容都包含在class="vcard"里。

目前看来，这似乎是一件费力不讨好的事情，因为 hCard 等微格式尚未得到浏览器和终端设备的良好支持。但是一旦这些不足得到了改善，hCard 就会为我们的数字生活带来很大的便利。

### 在 css/js 代码上线后之后开发人员经常会优化性能，从用户刷新网页开始，一次 js 请求一般情况下有哪些地方会有缓存处理？

dns缓存：短时间内多次访问某个网站，在限定时间内，不用多次访问DNS服务器。

cdn缓存：内容分发网络

浏览器缓存：浏览器在用户磁盘上，对最新请求过的文档进行了存储。

服务器缓存：将需要频繁访问的Web页面和对象保存在离用户更近的系统中，当再次访问这些对象的时候加快了速度。

另外 ，[关于更新发布CSS和JS文件的缓存问题](https://www.cnblogs.com/eric-qin/p/6255616.html) 

上文大概解决的思路：js文件的内容修改了,可以加个t参数表明一下日期,用这个日期来作为版本号，看到日期也能知道是哪天发布的，没有修改js文件就不用修改日期。

### 一个页面上有大量图片（大型电商网站），加载很慢，有哪些方法优化这些图片的加载，给用户更好的体验

图片懒加载，在页面上的未可视区域添加一个滚动事件，判断图片位置与浏览器顶端的距离与页面距离，如果前者小于后者，优先加载

如果为幻灯片、相册等，可以使用图片预加载技术，将当期的展示图片的前一张和后一张优先加载。

如果图片为css图片。可以使用 CSSsprite,SVGsprite,Iconfont,Base64等技术。

如果图片过大，可以使用特殊编码的图片，加载时会先加载一张压缩的特别厉害的缩略图，以提高用户体验。

如果图片展示区域小于图片的真实大小，则因在服务端根据业务需要先进行图片压缩，图片压缩后大小与展示一致。

### 如果去理解 HTML 结构的语义化

1. 为了去掉或样式丢失的时候能让页面呈现清晰的结构
2. 屏幕阅读器（如果访客有视障）会完全根据你的标记来“读”你的网页.
3. PDA/手机等设备可能无法像普通电脑的浏览器一样来渲染网页（通常是因为这些设备对css支持较弱）
4. 搜索引擎的爬虫也依赖于标记来确定上下文和各个关键字的权重，有利于SEO，和搜索引擎建立良好沟通
5. 便于团队开发和维护。遵循W3C标准的团队，可以减少很多差异化的东西，方便开发维护，提高效率，甚至实现模块化开发。

### 以前端角度出发做好SEO 需要考虑什么？

了解搜索引擎如何抓取网页和如何索引网页

需要知道一些搜索引擎的基本工作原理，各个搜索引擎之间的区别，搜索机器人（SEO robot 或叫 web crawler）如何进行工作，搜索引擎如何对搜索结果进行排序等等。

#### Meta标签语义化

主要包括主题（Title）,网站描述（Description）和关键词（Keywords），还有一些其他隐藏文字比如作者（AuThor）,目录（Category）,编码语种（Language）等

#### 如何选择关键词并在网页中放置关键词

搜索就得用关键词，关键词分析和选择是SEO的最重要的工作之一，首先要给网站确定主关键词（一般是5个上下），然后针对这些关键词进行优化，包括关键词密度（Density），相关度（Relavancy），突出性（Prominency）等等。

#### 了解主要的搜索引擎

虽然搜索引擎有很多，但是对网站流量起决定作用的就那么几个。比如英文的主要有Google，Yahoo，Bing 等；中文的有百度，搜狗，有道等。不同的搜索引擎对页面的抓取和索引、排序的规则都不一样。还要了解各搜索门户和搜索引擎之间的关系，比如 AOL 网页搜索用的是 Google 的搜索技术，MSN 用的是 Bing 的技术。

#### 主要的互联网目录

Open Directory 自身不是搜索引擎，而是一个大型的网站目录，他和搜索引擎的主要区别是网站内容的收集方式不同。目录是人工编辑的，主要收录网站主页；搜索引擎是自动收的，除了主页外还抓取大量的内容页面。

#### 按点击付费的搜索引擎

搜索引擎也需要生存，随着互联网商务的越来越成熟，收费的搜索引擎也开始大行其道。最典型的有 Overture 和百度，当然也包括 Google 的广告项目 Google Adwords。越来越多的人通过搜索引擎的点击广告来定位商业网站，这里面也大有优化和排名的学问，你得学会用最少的广告投入获得最多的点击。

#### 搜索引擎登录

网站做完了以后，别躺在那里等着客人从天而降。要让别人找到你，最简单的办法就是将网站提交（submit）到搜索引擎。如果你的是商业网站，主要的搜索引擎和目录都会要求你付费来获得收录（比如 Yahoo 要 299 美元），但是好消息是（至少到目前为止）最大的搜索引擎 Google 目前还是免费，而且它主宰着 60％以上的搜索市场。

#### 链接交换和链接广泛度（Link Popularity）

网页内容都是以超文本（Hypertext）的方式来互相链接的，网站之间也是如此。除了搜索引擎以外，人们也每天通过不同网站之间的链接来 Surfing（“冲浪”）。其它网站到你的网站的链接越多，你也就会获得更多的访问量。更重要的是，你的网站的外部链接数越多，会被搜索引擎认为它的重要性越大，从而给你更高的排名。

### 有哪项方式可以对一个 DOM  设置它的 CSS 样式

外部样式表，引入一个 外部 css 文件

内部样式表，将 css 代码直接在放在 `<head>` 标签内部

内联样式，将 css 样式 直接定义在  HTML 元素内部

### CSS都有哪些选择器

派生选择器（用 HTML 标签申明）

id 选择器（用 DOM 的 ID 申明）

类选择器（用一个样式类名申明）

属性选择器（用 DOM 的属性申明，属于 CSS2，IE6 不支持，不常用，不知道就算了）

除了前 3 种基本选择器，还有一些扩展选择器，包括

后代选择器（利用空格间隔，比如 div .a{ }）

群组选择器（利用逗号间隔，比如 p,div,#a{ }）

#### CSS 选择器的优先级是怎么样定义的？

基本原则：

一般而言，选择器越特殊，它的优先级越高。也就是选择器指向的越准确，它的优先级就越高。

**important > 内联 > ID > 类 > 标签 | 伪类 | 属性选择 > 伪对象 > 通配符 > 继承**

#### 复杂的计算方法：

用 1 表示派生选择器的优先级

用 10 表示类选择器的优先级

用 100 标示 ID 选择器的优先级

div.test1 .span var 优先级 1+10 +10 +1

span#xxx .songs li 优先级 1+100 + 10 + 1

\#xxx li 优先级 100 +1

### CSS 中可以通过哪些属性定义，使得一个DOM 元素不显示在浏览器可视范围内？

最基本的：设置 display 属性为 none，或者设置 visibility 属性为 hidden

技巧性：设置宽高为 0，设置透明度为 0，设置 z-index 位置在-1000

### 超链接访问过后 hover 样式就不出现的问题是什么？如何解决？

被点击访问过的超链接样式不在具有 hover 和 active 了,解决方法是改变 CSS 属性的排列顺序: L-V-H-A（link,visited,hover,active）

### 什么是 CSS hack? ie6,7,8 的 hack 分别是什么？

针对不同浏览器写不同的 CSS code 的过程就是 CSS hack。

```css
#test{
    width:300px;
    height:300px;
    background-color:blue; /*firefox*/
    background-color:red\9; /*all ie*/
    background-color:yellow; /*ie8*/
    +background-color:pink; /*ie7*/
    _background-color:orange; /*ie6*/    
}
:root #test { background-color:purple\9; } /*ie9*/
@media all and (min-width:0px){ #test { background=color:black； }} /*opera*/
@media screen and (-webkit-min-device-pixel-ratio:0){ #test { background-color: gray; }} /*Chrome and safari*/
```

### 请用 css 写一个简单的幻灯片效果页面

```css
    .ani{
      width: 480px;
      height: 320px;
      margin: 50px auto;
      overflow: hidden;
      box-shadow: 0 0 5px rgba(0,0,0,1);
      background-size: cover;
      background-position: center;
      -webkit-animation-name:"loops";
      -webkit-animation-duration:20s;
      -webkit-animation-iteration-count:infinite;
    }
    @-webkit-keyframes "loops" {
      0%{
        background:url(http://d.hiphotos.baidu.com/image/w%3D400/sign=c01e6adca964034f0fcdc3069fc27980/e824b899a9014c08e5e38ca4087b02087af4f4d3.jpg) no-repeat;
      }      25%{
        background:url(http://b.hiphotos.baidu.com/image/w%3D400/sign=edee1572e9f81a4c2632edc9e72b6029/30adcbef76094b364d72bceba1cc7cd98c109dd0.jpg) no-repeat;
      }      50%{
        background:url(http://b.hiphotos.baidu.com/image/w%3D400/sign=937dace2552c11dfded1be2353266255/d8f9d72a6059252d258e7605369b033b5bb5b912.jpg) no-repeat;
      }      75%{
        background:url(http://g.hiphotos.baidu.com/image/w%3D400/sign=7d37500b8544ebf86d71653fe9f9d736/0df431adcbef76095d61f0972cdda3cc7cd99e4b.jpg) no-repeat;
      }      100%{
        background:url(http://c.hiphotos.baidu.com/image/w%3D400/sign=cfb239ceb0fb43161a1f7b7a10a54642/3b87e950352ac65ce2e73f76f9f2b21192138ad1.jpg) no-repeat;
      }
    }
```

[具体代码点击这里](https://github.com/LbhFront-end/About-CSS3/blob/master/code/%E5%85%B6%E4%BB%96%E6%A1%88%E4%BE%8B/slide1.html)

[另外一个例子（通过透明度实现）](https://github.com/LbhFront-end/About-CSS3/blob/master/code/%E5%85%B6%E4%BB%96%E6%A1%88%E4%BE%8B/slide2.html)

### 行内元素和块级元素的具体区别是什么？行内元素的 padding 和 margin 可设置吗？

块级元素（block）特性：

总是独占一行，表现为另起一行，而且其后的元素也必须另起一行显示;

宽度（width）、高度（height）、内边距（padding）和外边距（margin）都可以控制

内联元素（inline）特性：

和相邻的内联元素在同一行：

宽度(width)、高度(height)、内边距的 top/bottom(padding-top/padding-bottom)和外边距的 top/bottom(margin-top/margin-bottom)都不可改变（也就是 padding 和 margin 的left 和 right 是可以设置的），就是里面文字或图片的大小。

#### 浏览器还有默认的天生 inline-block 元素（拥有内在尺寸，可设置高宽，但不会自动换行），有哪些？

```html
<input> 、<img> 、<button> 、<texterea> 、<label>。
```

### 什么是外边距重叠？重叠的结果是什么？

外边距重叠就是 margin-collapse 

在 CSS 当中，相邻的两个盒子（可能是兄弟关系也可能是祖先关系）的外边距可以结合成一个单独的外边距。这种合并边距的方式被称为折叠，并且因而所集合的外边距称为折叠外边距

折叠结构遵循下列计算规则：

两个相邻的外边距都是正数时，折叠结果是它们两者之间较大的值。

两个相邻的外边距都是负数时，折叠结果是两者绝对值的较大值

两个外边距一正一负，折叠结果是两者的相加的和。

### rgba() 和 opacity 的透明效果有什么不同？

rgba() 和 opacity 都能实现透明效果，但是最大的不同是 opacity 作用于元素，以及元素内的所有内容的透明度。

而 rgba() 只作用于元素的颜色和其背景色。（设置 rgba透明的元素的子元素不会继承透明效果！）

### css 中可以让文字垂直和水平方向上重叠的两个属性是什么？

垂直方向：line-height

水平方向：letter-spacing

letter-spacing 的妙用：可以用于消除 Inline-block 元素间的换行符空格间隙问题

### 如何垂直居中一个浮动元素

```css
/* 已知元素宽高 */
#target{
    background-color: #6699FF;
    width: 200px;
    height: 200px;
    position: absolute; /*父元素需要相对定位*/ 
    top: 50%;
    left: 50%;
    /* 二分之一的 height，width*/
    margin-top: -100px;
    margin-left: -100px;
}
/* 未知元素宽高 */
#target{
    background-color: #6699FF;
    width: 200px;
    height: 200px;
    margin: auto;
    position: absolute; /*父元素需要相对定位*/ 
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
}
/* 垂直居中一个<img> */
#container{
    display: table-cell;
    text-align: center;
    vertical-align: middle;
}
```

### px 和 和 em  的区别。

px 和 em 都是长度单位，区别是，px 的值是固定的，指定是多少就是多少，计算比较容易，em的值会继承父级元素的大小

浏览器的默认字体都是16px 所以未经调整的浏览器都符合 1em = 16px ，12px = 0.75em 10px = 0.625em



### 描述一个 ” reset ”的 的 CSS  文件并如何使用它 。 知道 normalize.css  吗？你了解他们的不同之处？

重置样式非常多，凡是一个前端开发人员肯定有一个常用的重置 CSS 文件并知道如何使用它们。他们是盲目的在做还是知道为什么这么做呢？原因是不同的浏览器对一些元素有不同的默认样式，如果你不处理，在不同的浏览器下会存在必要的风险，或者更有戏剧性的性发生。

你可能会用 Normalize 来代替你的重置样式文件。它没有重置所有的样式风格，但仅提供了一套合理的默认样式值。既能让众多浏览器达到一致和合理，但又不扰乱其他的东西（如粗体的标题）。

在这一方面，无法做每一个复位重置。它也确实有些超过一个重置，它处理了你永远都不用考虑的怪癖，像 HTML 的 audio 元素不一致或 line-height 不一致。

Reset样式的目的就是清除某些浏览器的默认样式,方便css的书写：例如：*{margin:0;padding:0;list-style:none;}

normalize的理念与reset的不同，他并不是清除浏览器的默认样式，而是尽量将所有的浏览器的默认样式统一。

### Sass、Less 是什么？为什么要用？

CSS 预处理器，是CSS 的一种抽象层，是一种特殊的语法或者说是语言编译成CSS。

例如 Less 是一种动态样式语言，将CSS 赋予了动态语言的特性，如变量，继承，运算，函数，Less 既可以在客户端上运行（支持 IE6+,Webkit,Firefox ）也可以在服务端运行（借助 Node.js ）

为什么要使用它们？

结构清晰，便于扩展。

可以方便地屏蔽浏览器私有语法差异，封装对浏览器语法差异的重复处理，可以减少无意义的机械劳动。

可以轻松实现多重继承。

可以完全兼容 CSS 代码，可以方便应用到旧项目中。Less 只是在 CSS  语法上做了扩展，所有老的 CSS 代码也可以与 Less 代码一起编译。

### display:none 与 visibility：hidden 的区别是什么？

display：隐藏对应元素但不挤占该元素原来的空间。

visibility：隐藏对应的元素并且挤占该元素原来的空间。

即是，使用 CSS display：none 属性后，HTML 元素（对象）的宽高等属性值都将丢失，而使用 visibility：hidden 属性后，HTML 元素（对象）仅仅是在视觉上看不见（完全透明），而它占据的空间位置仍然是存在的。

### CSS 中 link 和 @import 的区别是：

Link 属于 html 标签，而 @import 是 CSS 中提供的。

在页面加载的时候， link 会同时被加载，而 @import 引用的 CSS 会在页面加载完成后才会加载引用的 CSS 

@import 只有在 ie5 以上才可以被识别，而 link 是 html 标签，不存在浏览器兼容性问题

Link 引入样式的权重大于 @import 的引用（@import 是将引用的样式导入到当前的页面中）

### 简介盒子模型

CSS 的盒子模型有两种：IE 盒子模型，标准的 W3C 盒子模型

盒模型：内容、内边距、外边距（一般不计入盒子的实际宽度）、边框

![W3C盒子模型与IE模型 width属性的差异](http://jbcdn2.b0.upaiyun.com/2014/10/511748c935cab6e8c46d56b2e6e9c342.png)

### 为什么要初始化样式？

由于浏览器兼容的问题，不同的浏览器对标签的默认样式值不同，若不初始化会造成不同浏览器之间的差异

但是初始化 CSS 会对搜索引擎优化造成小影响。

### BFC 是什么？

BFC（块级格式化上下文），一个创建了新的 BFC 的盒子是独立布局的，盒子内的元素的布局不会影响盒子外面的元素，在同一个 BFC 中的两个相邻的盒子在垂直方向发生 margin 重叠问题。

BFC 是指浏览器中创建了一个独立的渲染区域，该区域内所有元素的布局不会影响到区域外远元素的布局，这个渲染其区域只对块级元素起作用。

### html 语义化是什么？

当页面样式加载失败的时候能够让页面呈现出清晰的结构

有利于 seo 优化，利于被搜索引擎收录（更便于搜索引擎的爬虫程序来识别）

便于项目的开发维护，使 html 代码具有可读性，便于其他设备解析。

### Doctype 的作用？严格模式与混杂模式的区别？

`<!DOCTYPE>` 用于告知浏览器该以何种模式来渲染文档 

严格模式下：页面排版以及 JS 解析是以该浏览器支持的最高标准来执行

混杂模式：不严格按照标准执行，主要是用来兼容旧的浏览器，向后兼容

### IE 的双边距BUG：块级元素 float 后设置横向 margin ，ie6 显示的margin 比设置的打，解决：加入 _display:inline



### HTML与 XHTML 两者的区别是什么？

1. 所有的标记都必须要有一个相应的结束标记
2. 所有标签的元素和属性的名字都必须使用小写
3. 所有 XML 标记必须合理嵌套起来
4. 所有属性必须使用引号 括起来
5. 把所有 `<` 和 `&` 特殊符号用编码表示
6. 给所有属性赋一个值
7. 不要在注释内容中使用 “——”
8. 图片必须有说明文字

### html 常见的兼容性问题？

1. 双边距 bug float 引起的，使用 display
2. 3像素问题，使用 float 引起的 使用 display: inline -3px
3. 超链接 hover 点击后失效 使用正确的书写顺序 link visited hover active
4. IE z-index 问题，给父级添加 position：relative 
5. Png 透明，使用 js代码改
6. Min-height 最小高度 !important 解决
7. select 在 ie6 下遮盖 使用 iframe 嵌套
8. 为什么没有办法定义 1px 左右的宽度容器（IE6 默认的行高造成的，使用 over:hidden,zoom:0.08,line-height:1px）
9. IE6 不支持 PNG 透明背景，解决方法：IE6 下使用 gif 图片
10. IE-5 不支持 opacity ,解决方法：

```css
.opacity{
    opacity:0.4
    filter: alpha(opacity = 60) /* for IE5-7 */
    -ms-filter:"progid:DXImageTransform.Microsoft.Alpha(Opacity=60)";/* for IE8 */
}
```

### 对 WEB 标准以及 W3C 的理解和认识

标签闭合、标签小写、不乱嵌套、提高搜索机器人搜索几率、使用外链css 和 js脚本、结构化行为表现的分离、文件下载与页面速度更快、内容能被更多的用户所访问、内容能被更广发的设备所访问、更少的代码和组件，容易维护，改版方便，不需要变动页面内容、提供打印版本而不需要复制内容、提供网站易用性。

### 行内元素有哪些？块级元素有哪些？CSS 的盒模型？空元素？

块级元素：div p h1-4 form ul ol li dl dt 

行内元素：a b br i span input select strong img

CSS 盒模型：border+margin+padding+content]

空元素：`<br>`、`<hr>`、`<img>`、`<link>`、`<meta>`、`<area>`、`base`、`<col>`、`<command>`、`<embed>`、`<keygen>`、`<param>`、`<source1>`、`<track>`、`<wbr>`

### 前端页面有那三层构成，分别是什么？作用是什么？

结构层 HTML 表示层 CSS 行为层 JS 

### Doctype 作用？严格模式和混杂模式如何处罚这两种模式，区分它们有何意义？

1. `<!DOCTYPE>`  声明位于文档中的最前面，处于 `<html>` 标签之前，告知浏览器的解析器，用什么文档类型规范来解析这个文档。
2. 严格模式的排版和 JS 运行模式是以该浏览器支持的最高标准运行
3. 混杂模式中，页面以宽松的向后兼容的方式显示，模拟老式浏览器的行为以防止站点无法工作。
4. `DOCTYPE` 不存在或格式不正确导致文档以混杂模式呈现。

### CSS 选择符有哪些？哪些属性可以继承？优先级算法如何计算？CSS3 新增伪类有哪些？

#### 选择符：

id 选择器（#id）

类选择器（.class）

标签选择器（div,h1,p）

相邻选择器（h1+p）

后代选择器（li a）

通配符选择器（*）

属性选择器（a[rel = "external"]）

伪类选择器（a:hover,li:nth-child）

#### 可以继承的属性

font-size,font-family color ul li dl dd dt

#### 不可以继承的属性

border padding margin width height

优先级就近原则，样式定义最近者为准，载入样式以最后载入的定位为准

#### 优先级

!important > id > class > tag

important 比 内联优先级高

#### CSS 新增伪类

p: first-of-type 选择属于其父元素的首个 `<p>` 元素的每个 `<p>` 元素

p: last-of-type 选择属于其父元素的最后 `<p>` 元素的每个 `<p>` 元素

p: only-of-type 选择属于其父元素唯一的 `<p>` 元素的每个 `<p>` 元素

p: only-child 选择属于其父元素的唯一子元素的每个 `<p>` 元素

p: only-child(2) 选择属于其父元素的第二个 子元素的每个 `<p>` 元素

:enable 、 ：disable 控制表单控件的禁用状态

:checked 单选框或复选框被选中



### 经常遇到的浏览器兼容性有哪些，原因，解决方法，常用的 hack 技巧

png24 的图片在 IE6 浏览器上出现背景，解决方案是做成 PNG8

浏览器默认的 margin 和 padding 不同，解决方案是加一个全局的 `*{margin:0,padding:0;}`来统一

IE6 双边距bug: 快属性标签 float 后，又有横行的 margin 的情况下，在 ie6 显示的 margin 比设置的大。

浮动 ie 产生的双倍距离 `#box{ float:left, width:10px; margin: 0 0 0 100px }`

在这种情况之下 IE 会产生 20px 的距离，解决方案是在 float 的标签样式控制加入 `_display:inline` 将其转化为行内属性（`_`这个符号只有 ie6 会识别）

渐进识别的方式，从总体中逐渐排除局部。

首先，巧妙的使用 `\9`这一标记，将IE 浏览器从所有情况中分离出来。

接着 再次使用 `+` 将 IE8 和 IE7/6 分离出来。

```css
.bb{
    background-color:#452142;/* 所有识别 */
    .background-color:#452143\9;/* IE6/7/8 识别 */
    +background-color:#452144;/* IE6/7 识别 */
    _background-color:#452145;/* IE6 识别 */
}
```

IE 下，可以使用获取常规属性的方法来获取自定义属性，也可以使用 `getAttribute()` 获取自定义属性

Firefox 下，只能使用 `getAttribute()`获取自定义属性

解决方法：统一通过 `getAttribute()` 获取自定义属性



IE 下， event 对象有 x,y属性，但是没有 pageX，pageY 属性；

Firefox 下，event 对象有 pageX，pageY 属性，但是没有x,y属性

（条件注释）缺点是在 IE 浏览器下可能会增加额外的 HTTP 请求数

Chrome 中文界面下默认会将小于 12px 的文本强制按照 12px 显示，可通过加入 CSS 属性 `-webkit-text-size-adjust:none；` 解决

### 列出 display 的值，说明它们的作用，position 的值，relative 和 absolute 原点是？

| display 的值 | 描述                                                     | 常见代表                                                |
| ------------ | -------------------------------------------------------- | ------------------------------------------------------- |
| none         | 此元素不会显示                                           | 无                                                      |
| block        | 此元素将会显示为块级元素，此元素前后带有换行符           | `<a>`、`<span>`、`<br/>`、`<i>`、`<em>`、`<strong>`     |
| inline       | 默认。此元素会被显示为内联元素，元素前后没有换行符       | `<div>`、`<p>`、`<h1>`...`<h6>`、`<ol>`、`<ul>`、`<dl>` |
| inline-block | 行内块元素，即是内联元素，又可以设置宽高以及行高，底边距 | `<img>`、`<input>`                                      |

| position的值 | 定位                                                         | 特点                                                         |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| static       | 默认值                                                       | 元素出现在正常的文档流中，不会受left、top、right、bottom的影响 |
| relative     | 相对定位，相对自身位置定位                                   | 可通过设置left、top、right、bottom的值来设置位置，并且它原本所占的空间不变，即不会影响其他元素布局，经常被用来作绝对元素的容器块 |
| absolute     | 绝对定位，相对于最近的除static定位以外的元素定位，若没有，则相对于html定位 | 脱离了文档流，不占据文档空间，若设置absolute，但没有设置top、left等值，其位置不变；若设置absolute，会影响未定义宽度的块级元素，使其变为包裹元素内容的宽度。 |
| fixed        | 固定定位 相对于浏览器窗口定位                                | 脱离文档流，不会随页面滚动而变化                             |

### position 跟 display 、margin collapse、overflow、float 这些特征相互叠加后会怎么样？

https://www.cnblogs.com/jackyWHJ/p/3756087.html

### CSS 的基本语句构成是？

选择器{属性1：值1；属性2：值2，......}

### CSS 中可以通过哪些属性定义，使得一个 DOM 元素不显示在浏览器可视范围内？

最基本的：设置 display 的属性为 none,或者设置 visibility  属性为 hidden 

技巧性： 设置宽高为0，设置透明度为0，设置 z-index 位置在 -1000

### 什么是外边距重叠？重叠的结果是什么？

外边距重叠就是 margin-collapse 在 CSS 当中，相邻的两个盒子（可能是小弟关系也可能是祖先关系）的外边距可以结合成一个单独的外边距，这种合并外边距的方式被称为折叠，并且因而所结合成的外边距被称为折叠外边距。

折叠结果遵循下列的计算规则：

1. 两个相邻的外边距都是正数时，折叠结果是它们两者之间的较大的数
2. 两个相邻的外边距都是负数时，折叠结果是两者绝对值较大的值
3. 两个外边距一正一负，折叠结果是两者相加的和。

### b 标签和 strong 标签， i 标签和 em 标签的区别？

后者有语义，前者则无。















### 参考链接：

1. [怪异模式（Quirks Mode）对 HTML 页面的影响](https://www.ibm.com/developerworks/cn/web/1310_shatao_quirks/)
2. [quirks模式是什么？它和standards模式有什么区别](https://www.jianshu.com/p/86be91568847)
3. [百度百科_webp格式](https://baike.baidu.com/item/webp%E6%A0%BC%E5%BC%8F/4077671?fr=aladdin)
4. [什么是微格式？在前端构建中应该考虑微格式吗？](https://www.jianshu.com/p/7e11c1f32a48)
5. [display和position的值与作用](https://blog.csdn.net/shjxa/article/details/56297656)