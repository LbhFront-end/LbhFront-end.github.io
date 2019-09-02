---
title: 面试官问我HTML5是什么（上）
date: 2019-05-23  09:30:54
tags: HTML5
categories: 
	- 前端面试


---

学习链接：
[HTML 5与CSS 3权威指南](https://read.douban.com/ebook/15160963/)

[W3cScholl](https://www.w3cschool.cn/html/)

## HTML5 与 HTML4 的区别

常见代码区别：

新增的一些属性：

```html
<!--html4-->
<form>
    <p>
        <label>
            <input name="search" id="search">
        </label>
    </p>
</form>
<script>
    document.getElementById('search').focus();
</script>
<!--html5-->
<form>
    <p>
        <label>
            <input name="search" autofocus>
        </label>
    </p>
</form>
```

结构上：

```html
<!--html4-->
	<div id="header"></div>
	<div id="nav"></div>
	<div class="article">
		<div class="section"></div>
	</div>
	<div id="side-bar"></div>
	<div id="footer"></div>
<!--html5-->
<header></header>
<nav></nav>
<article>
    <section></section>
</article>
<aside></aside>
<footer></footer>
```

### HTML 5 要解决的三个问题

- Web 浏览器之间的兼容性很低
- 文档结构不明确
- Web应用程序的功能受到了限制

### 语法的改变

HTML 语法是在 SGML(Standard Generalized Markup Language)语言的基础上建立起来的。对于 HTML 的执行在各个浏览器之间没有统一的一个标准。

HTML5 就是围绕这个Web标准，重新定义了一套在现有的 HTML 的基础上修改而来的语法，使它运行在各浏览器时它们都能符合这个通用标准。

#### HTML5 的标记方法

1.内容类型（ContentType）

扩展符仍为 '.html'或者 '.htm'。内容类型仍然为 'text/html'

2.DOCTYPE 声明

```html
<!--html4-->
<!DOCTYPE html PUBLIC "-//W3C/DTD 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/Xhtml1-transitional.dtd">
<!--html5-->
<!DOCTYPE html>
```

3.指定字符编码

```html
<!--html4-->
<meta http-equive="Content-Type" content="text/html;charset=UTF-8">
<!--html5-->
<meta charset="UTF-8">
```

#### 确保与之前的HTML 版本兼容

1.可以省略标记的元素

```shell
不允许写结束标记的元素有：area/base/br/col/command/embed/hr/img/input/keygen/link/meta/param/source/track/wbr
可以省略结束标记的元素：
li/dt/dd/p/rt/rp/optgroup/option/colgroup/thead/tbody/tfoot/tr/td/th
可以省略全部标记的元素(隐式存在，在文档结构仍然存在)：
html/head/body/colgroup/tbody
```

2.具有 boolean 值的属性

例如 disable/readonly/checked 等，只写属性不写属性值或者属性值为空字符表示属性值为 true。

3.省略引号

```html
<input type="text">
<input type='text'>
<input type=text>
```

### 新增的元素和废除的元素

### 新增-`html5(html4)`

#### 结构元素

`section(div)`

表示页面中的一个内容区块，用于章节、页眉、页脚或者页面中的其他部分。与 h1-h6元素结合使用，表示文档结构

`article(div)`

表示页面中的一块与上下文不相关的独立内容，例如博客中的一篇文章或者报纸中的一篇文章

`aside(div)`

aside 元素表示 article 元素的内容之外的，与 article 元素内容相关的辅助信息

`header(div)`

表示页面一个内容区块或者整个页面的标题

`hgroup(div)`

用于整个页面或者页面中的一个内容块的标题进行组合

`footer(div)`

整个页面或者页面中的一个内容区块的脚注。一般来说，会包括作者的姓名、创作日期以及作者的联系信息。

`nav(ul)`

页面中导航链接部分

`figure(dl)`

一段独立的流内容，一般表示文档主体流内容中的一个独立单元，使用 `figcaption` 元素 为 `figure` 元素组添标题

```html
<!--html5-->
<figure>
    <figcaption>Title</figcaption>
    <p>hahahahaha</p>
</figure>
<!--html4-->
<dl>
    <h1>Title</h1>
    <p>hahahahaha</p>
</dl>
```

#### 多媒体元素

`video(object)`

定义视频，比如电影片段或者其他视频流

```html
<!--html5-->
<video src='movie.ogg' controls='controls'>video元素</video>
<!--html4-->
<object type='video/ogg' data='movie.ogv'>
    <parma name='src' value="movie.ogv">
</object>
```

`audio(object)`

定义音频，比如音乐或者其他音频流

```html
<!--html5-->
<audio src='music.wav'>audio元素</audio>
<!--html4-->
<object type='application/ogg' data='someaudio.wav'>
    <parma name='src' value="someaudio.wav">
</object>
```

`embed(object)`

用来插入各种多媒体，格式可以是 Midi/Wav/AIFF/AU/MP3

```html
<!--html5-->
<embed src='music.swf'>embed元素</embed>
<!--html4-->
<object type='application/x-shockwave-flash' data='music.swf'></object>
```

`source`

为媒介元素定义媒介资源

```html
<!--html5-->
<audio controls>
   <source src="horse.ogg" type="audio/ogg">
   <source src="horse.mp3" type="audio/mpeg">
 Your browser does not support the audio element.
</audio>
<!--html4-->
<object type='application/ogg' data='someaudio.wav'>
    <parma name='src' value="someaudio.wav">
</object>
```

#### 语义元素

`mark(span)`元素

用来在视觉上向用户呈现那些需要突出显示或者高亮显示的文字。mark 元素的一个比较经典的应用就是在搜索结果中向用户高亮显示搜索关键词

`progress(无)`

表示进程运行中的进程，可以用 progress 来显示 javascript 中耗费时间的函数的进程

`meter(无)`

表示度量衡。仅用于已知最大值和最小值的度量。必须定义度量的范围，既可以在元素的文本中，也可以在 min/max 属性中定义。

`time(span)`

表示日期或者时间，也可以同时表示两者

`ruby(无)`

表示 ruby 注释（中文注音或者字符）

在东亚使用，显示的是东亚字符的发音。

与 `<ruby>` 以及 `<rt>` 标签一同使用：

ruby 元素由一个或多个字符（需要一个解释/发音）和一个提供该信息的 rt 元素组成，还包括可选的 rp 元素，定义当浏览器不支持 "ruby" 元素时显示的内容。

```html
<<ruby>
 漢 <rt> ㄏㄢˋ </rt>
</ruby>
```

`rt(无)`

表示元素字符的解释或者发音

`rp（无）`

在 ruby 注释中使用，以定义不支持 ruby 元素的浏览器所显示的内容

`wbr`

表示软换行，wbr 与 br 区别在于后者表示此处必须换行，前者是浏览器窗口或者父级元素的宽度足够宽的时候不进行换行，而当宽度不够时，主动在此处进行换行。wbr 元素好像对字符型的语言作用挺大，但是对中文没有多大用处。

```html
<p>学习 AJAX ,您必须熟悉 <wbr>Http<wbr>Request 对象。</p>
```

`canvas`

canvas 表示图形，比如图表和其他图像。元素本身没有行为，仅提供一块画布，但它把一个绘图 API 展示给 客户端的 javascript 以使得脚本能够把想绘制的东西绘制到这块画布上面。

```html
<!--html5-->
<canvas id='myCanvas' width='200' height='200'></canvas>
<!--html4-->
<object type='image/svg+xml' data='inc/hdr.svg' width='200' height='200'></object>
```

`details`

details 元素表示用户要求得到并且可以得到的细节信息，可以与 `summary`元素配合使用。`summary`提供标题或者图例。标题是可见的，用户点击标题时，会显示出细节信息。`summary`元素应该是 `details`元素的第一个子元素。

```html
<details>
    <summary>H5</summary>
    hahahahha
</details>
```

`datalist`

表示可选数据的列表，与 input 元素配合使用，可以制作出 输入值的下拉列表。

```html
	<input list="cars" />
	<datalist id="cars">
		<option value="BMW">
		<option value="Ford">
		<option value="Volvo">
	</datalist>
```

`datagrid`

表示可选数据的列表，以树形列表的形式来显示

```html
	<datagrid>
		<ol>
		 <li> (datagrid row 0) </li>
		 <li> (datagrid row 1)
			<ol style="list-style-type:lower-alpha;">
			 <li> (datagrid row 1,0) </li>
		 <li> (datagrid row 1,1) </li>
			</ol>
		 </li>
		 <li> (datagrid row 2) </li>
		</ol>
	 </datagrid>
```

`output(span)`

表示不同类型的输出，比如脚本输出

```html
	 <form oninput="x.value=parseInt(a.value)+parseInt(b.value)">
		0
		<input type="range" id='a' value="50">100+
		<input type="number" id="b" value="50">=
		<output name="x" for="a b"></output>
	</form>
```

`menu`

表示菜单列表，当希望列出表单控件的时候使用该标签

```html
<menu type="toolbar">
 <li>
  <menu label="File">
   <button type="button" onclick="file_new()">新建</button>
   <button type="button" onclick="file_open()">打开</button>
   <button type="button" onclick="file_save()">保存</button>
  </menu>
 </li>
 <li>
  <menu label="Edit">
   <button type="button" onclick="edit_cut()">剪切</button>
   <button type="button" onclick="edit_copy()">复制</button>
   <button type="button" onclick="edit_paste()">粘贴</button>
  </menu>
 </li>
</menu>
```

但是目前主流所有浏览器都不支持这个标签

#### input元素类型

`email`

表示必须输入 E-mail 

`url`

输入 URL 地址

`number`

输入数值

`range`

输入一定范围内数字值

`Date Pickers`

拥有多个选择日期和时间的新型输入文本框

data-日、月、年

month-月、年

week-周、年

time-小时、分钟

datetime-日、月、年（UTC）

datetime-local-日、月、年（本地时间）

### 废除（替代元素）

1.能用css替代的元素

```shell
basefont/big/center/font/s/strike(del)/tt/u
```

2.不再使用 frame 框架

```shell
frameset/frame/noframes，现只支持 iframe框架。
```

3.只有部分浏览器支持的元素

```shell
applet(embed/obejct)/bgsound(audio)/blink/marquee
```

4.其他被废除的元素

```shell
rb(ruby)/acronym(abbr)/dir(ul)/isindex(form+input)/listing(pre)/xmp(code)/nextid(GUIDS)/plaintext('text/plain' MIME 类型)
```

### 新增的属性和废除的属性

### 新增

#### 表单相关的属性

- `input[type=text]`、`select`与 `button` 指定 `autofucus`属性，以指定的方式让元素在画面打开的时候自动获得焦点
- `input[type=text]`与 `textarea`指定 `placeholder`属性，会对用户的输入进行提示，提示用户输入的内容
- `input`、`output`、`select`、`textarea`、`button`与 `fieldset`指定 `form`属性，声明它们属性哪个表单，然后将其放置任何位置，而不是在表单之内
- `input[type=text]`与 `textarea`指定 `required`。表示用户提交的时候进行检查，检查该元素内一定要有输入内容
- `input`其他新增的属性：`autocomplete`、`min`、`max`、`multiple`、`pattern`、`step`。同时还有一个 新的 `list`元素可以与 `datalist`配合使用。`datalist`与 `autocomplete`属性配合使用。`multiple`属性允许在上传文件的时候一次上传多个文件。
- `input`、`button`增加了新的属性 `formaction`、`formenctype`、`formmethod`、`formnovalidate`与 `formtarget`，它们可以重载 `form`元素的 `action`、`enctype`、`method`、`novalidate`与 `target`属性。为  `fileset`增加了 `disabled`，可以把它的子元素设为 `disabled(无效)`状态
- `input`、`button`、`form`增加了 `novalidate`属性，该属性可以取消提交时进行的有关检查，表单可以被无条件提交。

#### 链接相关属性

- `a`与 `area`增加 `media`属性，该属性规定目标 URL是什么类型的媒介/设备进行优化，只能在 `href`属性存在时使用
- 为 `area`元素增加了 `hreflang`属性与 `rel`属性，以保持与 `a`元素、`link`元素的一致。
- `link`元素增加了新的属性 `sizes`。该属性可以与 `icon`属性元素结合使用(通过 `rel`属性)，该属性指定关联图标（`icon`元素）的大小。
- 为 `base`元素增加了`target`属性，主要目的是保持与 `a`元素的一致性。

#### 其他属性

除了上面介绍的与表单和链接相关的属性外，HTML5 还增加了下面的属性：

- `ol`元素增加 `reversed`,它指定了列表倒序显示
- `meta`增加 `charset`属性。因为这个属性被广泛支持了，而且为文档的字符编码的执行提供了一种良好的方式
- 为 `style`属性增加`scoped`属性，用来规定样式的作用范围，例如只对页面某个树起作用。
- `script`增加 `async`属性，定义脚本是否异步执行
- `html`增加 `mainfest`，开发离线Web 应用程序时它与 API 结合使用，定义一个 URL,在这个 URL 上描述文档的缓存信息。
- `iframe`元素增加了三个属性 `sandbox`、`seamless`与 `srcdoc`，用来提高页面安全性，防止不信任的 Web 页面执行某些操作。

### 废除

| **在HTML 4中使用的属性**                                     | **使用该属性的元素**                                   | **在HTML 5中的替代方案**                                     |
| ------------------------------------------------------------ | ------------------------------------------------------ | ------------------------------------------------------------ |
| **rev**                                                      | link、a                                                | rel                                                          |
| **charset**                                                  | link、a                                                | 在被链接的资源的中使用HTTP Content-type头元素                |
| **shape**、coords                                            | a                                                      | 使用area元素代替a元素                                        |
| **longdesc**                                                 | img、iframe                                            | 使用a元素链接到校长描述                                      |
| **target**                                                   | link                                                   | 多余属性，被省略                                             |
| **nohref**                                                   | area                                                   | 多余属性，被省略                                             |
| **profile**                                                  | head                                                   | 多余属性，被省略                                             |
| **version**                                                  | html                                                   | 多余属性，被省略                                             |
| **name**                                                     | img                                                    | id                                                           |
| **scheme**                                                   | meta                                                   | 只为某个表单域使用scheme                                     |
| **archive****、chlassid、codebose、codetype、declare、standby** | object                                                 | 使用data与typc属性类调用插件。需要使用这些属性来设置参数时，使用param属性 |
| **valuetype**、type                                          | param                                                  | 使用name与value属性，不声明之的MIME类型                      |
| **axis**、abbr                                               | td、th                                                 | 使用以明确简洁的文字开头、后跟详述文字的形式。可以对更详细内容使用title属性，来使单元格的内容变得简短 |
| **scope**                                                    | td                                                     | 在被链接的资源的中使用HTTP Content-type头元素                |
| **align**                                                    | caption、input、legend、div、h1、h2、h3、h4、h5、h6、p | 使用CSS样式表替代                                            |
| **alink**、link、text、vlink、background、bgcolor            | body                                                   | 使用CSS样式表替代                                            |
| **align**、bgcolor、border、cellpadding、cellspacing、frame、rules、width | table                                                  | 使用CSS样式表替代                                            |
| **align**、char、charoff、height、nowrap、valign             | tbody、thead、tfoot                                    | 使用CSS样式表替代                                            |
| **align**、bgcolor、char、charoff、height、nowrap、valign、width | td、th                                                 | 使用CSS样式表替代                                            |
| **align**、bgcolor、char、charoff、valign                    | tr                                                     | 使用CSS样式表替代                                            |
| **align**、char、charoff、valign、width                      | col、colgroup                                          | 使用CSS样式表替代                                            |
| **align**、border、hspace、vspace                            | object                                                 | 使用CSS样式表替代                                            |
| **clear**                                                    | br                                                     | 使用CSS样式表替代                                            |
| **compace**、type                                            | ol、ul、li                                             | 使用CSS样式表替代                                            |
| **compace**                                                  | dl                                                     | 使用CSS样式表替代                                            |
| **compace**                                                  | menu                                                   | 使用CSS样式表替代                                            |
| **width**                                                    | pre                                                    | 使用CSS样式表替代                                            |
| **align**、hspace、vspace                                    | img                                                    | 使用CSS样式表替代                                            |
| **align**、noshade、size、width                              | hr                                                     | 使用CSS样式表替代                                            |
| **align**、frameborder、scrolling、marginheight、marginwidth | iframe                                                 | 使用CSS样式表替代                                            |
| **autosubmit**                                               | menu                                                   |                                                              |

### 全局属性

#### contentEditable

允许用户编辑元素中的内容，该元素必须是可以获得用户鼠标焦点的元素，在点击鼠标后要向用户提供一个插入符号，提示用户该元素中的内容允许被编辑。`contentEditable`属性是一个布尔值属性，可以被指定 `true`或者 `false`

除此之外，还有一个隐藏的 `inherit`状态，属性为  `true`，元素被指定为允许编辑，属性为 `false`时，元素被指定为不允许编辑。未指定 `true`或者 `false`时，则由 `inherit`状态来决定，如果元素的父元素是可以编辑的，则该元素就是可编辑的。

元素还具有一个叫做 `isContentEditable`属性，当元素可编辑时，该属性为 true，当元素不可编辑时，该属性 为 false。

```html
	<ul contentEditable>
		<li>元素列表1</li>
		<li>元素列表2</li>
		<li>元素列表3</li>
	</ul>
```

#### designMode

`designMode` 属性用来指定整个页面是否可编辑，当页面可编辑时，页面中任何支持上文所述的 `contentEditable` 属性的元素都变成了可编辑状态。`designMode`只能在 javascript 脚本里被编辑修改。该属性有两个值——“on” 和 "off"。属性被指定为 `on`时，页面可编辑，被指定为 `off`时，页面不可编辑。使用 javascript 来指定 designMode 属性的方法如下：

```javascript
document.designMode = 'on'
```

针对 `designMode` 属性，各个浏览器的支持情况也不一样：

- IE8：安全考虑，不允许使用 `designMode `属性让页面进行编辑状态
- IE9：允许使用 `designMode`属性让页面进入编辑状态
- Chrome3 和 Safari：使用内嵌 `frame`方式，该内嵌 `frame`是可编辑的
- Firefox 和 Opera：允许使用 `designMode`属性让页面进入编辑状态

#### hidden 

在 HTML5 中，所有的元素都允许有一个 `hidden`属性，该属性类似于 `input` 中的 `hidden`元素，功能是通知浏览器不渲染该元素，使该元素处于不可见状态。但是元素中的内容还是浏览器创建的，也就是说页面装载后允许使用 javascript 脚本将该属性取消，取消后该元素变为可见状态，同时元素中的内容页即时显示出来。`hidden`属性是一个布尔值的属性，为设为 `true`后，元素处于不可见状态，当设为 `false`后，元素属于可见状态。

#### spellcheck

`spellcheck` 是针对 `input`与 `textarea`这两个文本输入框提供的一个新属性，它的功能为对用户输入的文本内容进行拼写和语法检查。是一个布尔值属性，具有 true 和 false 两种值。必须明确书写属性值

```html
<textarea contentEditable spellcheck="true"></textarea>
<input type="text" spellcheck="true">
<p contenteditable="true" spellcheck="true">这是可编辑的段落。请试着编辑文本。</p>
```

需要注意的是如果元素的 `readonly`或者  `disabled`设为 `true`，则不执行拼写检查。

#### tabindex

当不断敲击 Tab 键让窗口或者页面中的控件获得焦点，对窗口或者页面的所有控件进行遍历的时候，每一个控件的 `tabindex`表示该控件是第几个页面访问到的。

过去的这个属性在编辑网页的时候非常有用，但如今控件的遍历顺序是由元素在页面上所处的位置决定的，所以不再需要了。

但是 `tabindex`还有另外一个作用，在默认属性下，只有链接元素与表单可以通过按键获得焦点。如果对其他元素使用 `tabindex`属性后，也能让该元素获得焦点，那么当脚本中执行 `focus()`语句的时候，就可以让该元素获得焦点了。但这样做会有一个副作用：钙元素也可以通过按 Tab 键获得焦点，而这时有可能也不是开发者想要的结果。

把元素的 `tabindex`设为为负数（通常为-1）后就可以解决这个问题。`tabindex`的值为负数后，仍然可以通过编程的方式让元素获得焦点，但按下 Tab 键时该元素就不能获得焦点了。这在复杂的页面中或复杂的 Web 应用程序中是非常有用的。在 HTML4 中，-1 是一个无用的属性值，但到了 HTML5 中，通过巧妙运用让该属性得到了极大的应用。

```html
<a href="http://laibh.top" tabindex="2">赖同学</a><br />
<a href="http://www.google.com/" tabindex="1">Google</a><br />
<a href="http://www.microsoft.com/" tabindex="3">Microsoft</a>
```

#### data-*

使用 data-* 属性来嵌入自定义数据

```html
<ul id="target">
    <li id="e1" data-animal-type="鸟类">喜鹊</li>
    <li id="e2" data-animal-type="鱼类">金枪鱼</li>
    <li id="e3" data-animal-type="蜘蛛">蝇虎</li>
</ul>
<script>
    function showDetails(animal) {
        let animalType = animal.getAttribute("data-animal-type");
        console.log(animal.innerHTML + '是一种' + animalType);
    }
    const ul = document.getElementById('target');
    ul.onclick = function (e) {
        let ev = e || window.event;
        let target = ev.target || ev.srcElement;
        showDetails(target);
    }
</script>
```

#### draggable

规定元素是否可以拖动，链接和图像默认是可以拖动的。

语法：

```html
<element draggable="true|false|auto">
```

例子：

```html
<style>
	#dropbox{
		width: 400px;
		height: 400px;
		border:1px solid #aaaaaa;
	}
</style>
<div id="dropbox" ondrop="drop(event)" ondragover="allowDrop(event)"></div>
<br>
<p id="drag" draggable="true" ondragstart="drag(event)">这是一段可移动的段落，请把该段落拖入上面的矩形</p>
<script>
    function allowDrop(ev){
        ev.preventDefault();
    }
    function drag(ev){
        ev.dataTransfer.setData('Text',ev.target.id)
    }
    function drop(ev){
        let data = ev.dataTransfer.getData('Text');
        ev.target.appendChild(document.getElementById(data));
        ev.preventDefault()
    }
</script>
```

### 新结构元素使用样式

兼容旧版本浏览器的 hack

```html
<style>
    article,aside,dialog,figure,footer,header,legend,nav,section{
        display:block;
    }
    nav{
        float:left;
        width:20%;
    }
    article{
        float:right;
        width:79%;
    }
</style>

<!--IE8之前的浏览器不支持使用 CSS方法来使用这些尚未支持的结构元素，需要使用脚本定义-->
<script>
    document.createElement('header');
    document.createElement('nav');
    document.createElement('article');
    document.createElement('footer');
</script>

<!--或者引入一个 js来hack-->
<head>
    <title>HTML5 HACK</title>
    <!--[if lt IE9]>
	<script src="http://apps.bdimg.com/libs/html5shiv/3.7/html5shiv.min.js"></script>
    <![end if]-->
</head>


```

## 表单与文件

**新的 form 属性**：

- autocomplete
- novalidate

**新的 input 属性**：

- autocomplete
- autofocus
- form
- form overrides (formaction, formenctype, formmethod, formnovalidate, formtarget)
- height 和 width
- list
- min, max 和 step
- multiple
- pattern (regexp)
- placeholder
- required

**浏览器支持**

| Input type        | IE   | Firefox | Opera | Chrome | Safari |
| :---------------- | :--- | :------ | :---- | :----- | :----- |
| autocomplete      | 8.0  | 3.5     | 9.5   | 3.0    | 4.0    |
| autofocus         | No   | No      | 10.0  | 3.0    | 4.0    |
| form              | No   | No      | 9.5   | No     | No     |
| form overrides    | No   | No      | 10.5  | No     | No     |
| height and width  | 8.0  | 3.5     | 9.5   | 3.0    | 4.0    |
| list              | No   | No      | 9.5   | No     | No     |
| min, max and step | No   | No      | 9.5   | 3.0    | No     |
| multiple          | No   | 3.5     | No    | 3.0    | 4.0    |
| novalidate        | No   | No      | No    | No     | No     |
| pattern           | No   | No      | 9.5   | 3.0    | No     |
| placeholder       | No   | No      | No    | 3.0    | 3.0    |
| required          | No   | No      | 9.5   | 3.0    | No     |

### 新增属性

`form`

在 HTML4 中表单内的从属元素必须写在表单内容，但是 HTML5 中，可以把它书写在页面的任何地方，然后给该元素指定一个 `form`属性。属性值为该表单的 id，这样就可以声明该元素从属于指定表单了。

```html
<form id="testform">
    <input type="text">
</form>
<textarea form="testform"></textarea>
```

目前只有 Opera 支持这个属性

`formaction`

HTML4中，一个表单内的所有元素都只能通过表单的 `action`属性统一提交到另一个页面，而在 HTML5 可以给所有的提交按钮，`<input type="submit">`、`<input type="image">`、`<button type="submit">`都增加 `formaction`属性，使得点击不同的按钮，可以将表单提交到不同的页面

```html
<form id="testform">
    <input type="submit" name="s1" value="v1" formaction="s1.jsp">提交到 s1
    <input type="submit" name="s2" value="v2" formaction="s2.jsp">提交到 s2
    <input type="submit" name="s3" value="v3" formaction="s3.jsp">提交到 s3
</form>
```

目前没有浏览器支持这一属性

`formmethod`

在 HTML4 中只有一个表单内有 `action`属性来对表单内所有元素统一指定提交页面，所以每个表单内只有一个 `method`属性来指统一指定提交方法。在 HTML5 中，可以使用 `formaction`属性来对每个表单元素分别指定不同的提交页面，也可以用 `formmethod`对每个表单元素分别指定不同的提交方式。

```html
<form id="testform" action="serve.jsp">
    <input type="submit" name="s1" value="v1" formaction="s1.jsp" formmethod="get">提交到 s1
    <input type="submit" name="s2" value="v2" formaction="s2.jsp" formmethod="post">提交到 s2
</form>
```

目前没有浏览器支持这一属性

`placeholder`

是指文本框为输入状态时，文本框里面显示的输入提示。

```html
<input type="text" placeholder="input me">
```

`autofocus`

给文本框、选择框或者按钮控件加上该属性，当画面打开的时候，该控件自动获得光标焦点。

```html
<input type="text" autofocus>
```

一个页面只能有一个控件具有该属性。不要滥用，建议只有当一个页面是以使用某个控件为主要目的的时候才使用。例如搜索页面中的搜索文本框。

`list`

为单行文本框添加一个 `list`属性，它的值为某个 `datalist`元素的 id。类似于 `select`，不同的是它除了可以选择之外，还可以自己输入。

```html
	<input list="cars" />
	<datalist id="cars">
		<option value="BMW">
		<option value="Ford">
		<option value="Volvo">
	</datalist>
```

`autocomplete`

辅助输入所用的自动完成功能，是一个节省输入时间，同时也非常方便的功能。可以指定 `on`、`off`、`""`三个值。不指定时用浏览器的默认值。

```html
<input type="text" name="greeting" autocomplete="on" list="greetings">
```

### 表单元素种类

`url`、`email`、`date`、`time`、`datetime`、`datetime-local`、`month`、`week`、`number`、`range`、`search`、`tel`、`color`

```html
<input type="url" value="http://laibh.top">
<input type="email" value="544289495@qq.com">
<input type="date" value="2019-05-22">
<input type="time" value="11:27">
<input type="datetime">
<input type="datetime-local">
<input type="month" value="2019-05">
<input type="week" value="2019-W21">
<input type="number" value="25" min="10" max="100" step="2">
<input type="range" value="25" min="10" max="100" step="2">
<input type="search">
<input type="tel">
<input type="color">
```

`output`

定义了不同类型的输出，比如计算结果或者脚本的输出。output 元素必须从属某个表单，必须将它书写在表单内部，或者对它添加 form 属性。

```html
<form id="testform1">
    <input type="range" min="0" max="100" name="range1" step="5">
    <output onforminput="value=range1.value">50</output>
</form>
<!--或者浏览器兼容更好的下面这种方式-->
<form id="testform1" oninput="x.value=range1.value">
    <input type="range" min="0" max="100" name="range1" step="5">
    <output name="x" for="range1">50</output>
</form>
```

| 属性                                                        | 值           | 描述                                   |
| :---------------------------------------------------------- | :----------- | :------------------------------------- |
| [for](http://www.w3school.com.cn/tags/att_output_for.asp)   | *element_id* | 定义输出域相关的一个或多个元素。       |
| [form](http://www.w3school.com.cn/tags/att_output_form.asp) | *form_id*    | 定义输入字段所属的一个或多个表单。     |
| [name](http://www.w3school.com.cn/tags/att_output_name.asp) | *name*       | 定义对象的唯一名称。（表单提交时使用） |

### 表单验证

**自动验证**

`require`

可以应用在大部分输入元素（除了隐藏元素，图片按钮上）。在提交时，如果元素中内容为空白，则不允许提交，同时在浏览器中显示信息提示文字。

`pattern`

要求输入内容格式的，对 `input`使用 `pattern`属性，设为某个正则表达式

```html
<input pattern="[0-9][A-Z]{3}" name="part" placeholder="输入内容；一个数字与三个大写字母">
```

`min` 与 `max`

数值类型与日期类型元素专用属性。限制了 `input`元素输入的数值与日期范围。

`step`

控制 `input`元素中的值增加或者减少的步幅。

**显式验证**

HTML5 中，`form` 与 `input`(除了 `select` 与 `textarea`)都具有一个 `checkValidity`。使用这个方法，可以显示对表单内所有元素内容或者单个元素内容进行有效性验证。返回 boolen值

```html
<form id="textform4" onsubmit="return check()">
    <input type="url" value="http://laibh.top" id="url">
    <input type="submit">
</form>
<script>
    function check() {
        const url = document.getElementById('url');
        if (!url.value) {
            console.log('地址不能为空');
            return false;
        }
        if (!url.checkValidity()) {
            console.log('请输入正确的url地址');
            return false;
        }
        console.log(url.value)
    }

</script>
```

但其实一般提交按钮的时候，会自动检验格式

### 取消校验

有两种方法取消校验，第一种用 `form`的 `novalidate`属性

```html
<form id="textform4" onsubmit="return check()" novalidate>
    <input type="url" value="http://laibh.top" id="url">
    <input type="submit">
</form>
```

但是上述的方法里面的 `checkValidity()`仍会生效

第二种是利用 `input`  或者 `submit`元素的 `formnovalidate`。

```html
<form id="textform4" onsubmit="return check()">
    <input type="url" value="http://laibh.top" id="url" formnovalidate="formnovalidate">
    <input type="submit">
</form>
<!--或者-->
<form id="textform4" onsubmit="return check()">
    <input type="url" value="http://laibh.top" id="url">
    <input type="submit" formnovalidate="formnovalidate">
</form>
```

前者是单单让一个 `input`取消验证，后者是取消验证整个 `form`表单

### 自定义错误信息

HTML5 中可以利用 javascript 调用各个 input 元素的 `setCustomValidity`方法来自定义错误信息。需要注意的是一旦设置了 `setCustomValidity`，检验通过的条件变成了 `setCustomValidity('') && !valueMissing && !patternMismatch`

```html
<input type="text" id="code" required pattern="^\d{4}$" placeholder="请输入代码" oninput="check(this)">
<script>
    function check(i) {
        let { valueMissing,patternMismatch } = i.validity;
        console.log(valueMissing,patternMismatch)			
        if(valueMissing){
            i.setCustomValidity('该字段不能为空，请按要求填入代码')
        }else{
            if(patternMismatch){
                i.setCustomValidity('请输入4位数字的代码')
            }else{
                i.setCustomValidity('')
            }
        }
    }    
</script>
```

### 增强的页面元素

`figure`和 `figcaption`

`figure`是一种元素的组合，带有可选标题。figure 元素用来表示网页上一块独立内容，将其从网页上移除后不会对网页上的其他内容产生任何影响。`figure`元素所表示的内容可以是图片、统计图或者代码示例。

`figcaption`表示 `figure`元素的标题，从属于 `figure`。一个 `figure`最多只允许防止一个 `figcaption`元素，但是运行放置多个其他元素

```html
<figure>
  <figcaption>黄浦江上的的卢浦大桥</figcaption>
  <p>拍摄者：W3School 项目组，拍摄时间：2010 年 10 月</p>
  <img src="http://www.w3school.com.cn/i/shanghai_lupu_bridge.jpg" width="350" height="234" />
</figure>
```

`figure`所表示的内容通常是图片、统计图或者代码示例，也可以是音频插件、视频插件或者统计表格。

`details`

提供了一种替代 javascript 的将画面上的局部区域进行展开或者收缩的方法，目前只有 Chrome 和 Safari 6 支持 

```html
<details>
<summary>Copyright 2011.</summary>
<p>All pages and graphics on this web site are the property of W3School.</p>
</details>
```

`mark`

表示页面需要高亮或者突出显示的。只好是对网页全文检索某个关键词时显示的检索结果。

```html
<p>Do not forget to buy <mark>milk</mark> today.</p>
```

为了某种特殊目的把原文作者没有特别重点标示的内容给标示出来。

与 `em` 、`strong`元素的区别在于前者是作者自己标出来的重点要注意的，而`mark`跟作者本身没有太多关系，是在后来引用的时候添加上去的。

`progress`

表示一个任务的完成进度。

该元素有两个参数来表示当前任务完成情况。`value`表示完成了多少工作量，`max`表示总共多少工作量。

在属性设定的时候，这两个属性只能设定为有效的浮点数，`value`的值必须大于0，小于等于 `max`。

```html
<h2>progress 使用示例</h2>
<p>完成百分比：<progress id="progress" max="100" value="0"><span>0</span>%</progress></p>
<input type="button" onclick='add()' value="请点击">
<script>
    const progressBar = document.getElementById('progress');
    function add() {
        progressBar.getElementsByTagName('span')[0].textContent = '0'
        for (let i = 0; i <= 100; i++) {
            setTimeout(function () {
                progressBar.value = i;
                progressBar.getElementsByTagName('span')[0].textContent = i
            }, 1000 * i)
        }
    }
</script>
```

`meter`

表示规定范围内的数量值。例如磁盘使用量，对于某个候选者的投票人数占总投票人数的比例等。

meter 元素有六个属性：

| 属性    | 值       | 描述                                                         |
| :------ | :------- | :----------------------------------------------------------- |
| high    | *number* | 定义度量的值位于哪个点，被界定为高的值。                     |
| low     | *number* | 定义度量的值位于哪个点，被界定为低的值。                     |
| max     | *number* | 定义最大值。默认值是 1。                                     |
| min     | *number* | 定义最小值。默认值是 0。                                     |
| optimum | *number* | 定义什么样的度量值是最佳的值。如果该值高于 "high" 属性，则意味着值越高越好。如果该值低于 "low" 属性的值，则意味着值越低越好。 |
| value   | *number* | 定义度量的值。                                               |

```html
<meter value="5" min="0" max="10" high="8" low="2" optimum="5">3/10</meter><br>
<meter value="0.6">60%</meter>

<p><b>注释：</b>Internet Explorer 不支持 meter 标签。</p>
```

`menu`与 `command`

用于菜单工具条与弹出菜单。两个元素的浏览器支持不是很好，不做介绍。

`ol`

增加了 `start`与 `reversed`属性

```html
<ol reversed>
  <li>咖啡</li>
  <li>牛奶</li>
  <li>茶</li>
</ol>

<ol start="50" reversed>
  <li>咖啡</li>
  <li>牛奶</li>
  <li>茶</li>
</ol>
```

`dl`

重新定义后的 `dl`列表包含多个带名字的列表项。每一项包含一条或者多条带名字的 `dt`元素，用来表示术语，`dt`元素后面紧跟一个或者多个 `dd`元素，用来表示定义。在一个元素内，不允许带有相同的 `dt`元素，不允许有重复的术语。`dl`可以用来定义文章或者网页上的术语解释

```html
<dl>
<dt>Coffee</dt>
<dd>Black hot drink</dd>
<dt>Milk</dt>
<dd>White cold drink</dd>
</dl>
```

`cite`

标签定义作品（比如书籍、歌曲、电影、电视节目、绘画、雕塑等等）的标题。不能是人名

```html
<img src="/i/ct_fcsz.jpg" alt="富春山居图" />
<p>
<cite>《富春山居图》</cite>由黄公望始画于至正七年(1347)，于至正十年完成。
</p>
```

`small`

由原来的通用展示性元素变成更具体的、专门用来标识所谓的"小字印刷体"的元素。通常用于免责声明、注意事项、法律法规、与版权相关等的法律性声明文字中，同时不允许被应用在页面主内容中，只允许被当做辅助信息用 `inline`方式内嵌在页面上使用。同时 `small`元素也不意味着元素中内容字体会变小，如果需要将字体变小，需要配合  CSS 样式来用。

### 文件 API

HTML5 中提供了一个关于文件操作的文件 API.

#### FileList 对象与 file 对象

`FileList` 对象表示用户选择的文件列表。在 HTML4 中，`file` 控件只允许放置一个文件，到了 HTML5 中。通过添加 `multiple`属性，`file`控件允许一次放置多个文件。控件内的每一个用户选择的文件都是一个 `file`对象，而 `FileList`对象则为这些 `file`对象的列表，代表用户选择的所有文件。 `file`对象有两个属性，`name`属性表示文件名，不包括路径，`lastModifiedDate`属性表示文件的最后修改日期。

```html
<input type="file" id="file" multiple size="80">
<input type="button" onclick="showFileName()" value="文件上传">
<script>
    function showFileName(){
        let file;
        const files = document.getElementById('file').files;
        const len = files.length;
        for (let i = 0; i < len; i++) {
            file = files[i]
            console.log(file.name);
        }
    }
</script>
```

#### Blob对象

Blob 表示二进制原始数据，它提供一个 `slice`方法，可以通过该方法访问到字节内部数据块。事实上，上面的 `file`对象也继承了这个 Blob 对象。

Blob 对象有两个属性，`size`属性表示一个 Blob 对象的字节长度，`type` 属性表示 Blob 的 MIME 类型，如果是未知类型的话，返回一个空字符串。

```javascript
    function showFileName(){
        let file;
        const files = document.getElementById('file').files;
        const len = files.length;
        for (let i = 0; i < len; i++) {
            file = files[i]
           console.log(file.name + '===' + file.size + '===' + file.type);
        }
    }
```

通过对 `file.size`或者 `file.type`判断可以进行文件大小与文件类型的限制。另外 HTML5 已经对 `file`控件添加了 `accept`属性，企图让 `file`控件只能接受某种类型的文件。

```html
<input type="file" id="file" multiple size="80" accept="image/gif">
```

这样打开文件的时候就会显示 gif结尾的文件，当然如果你强行传其他类型的也是没有报错的。所以如果要做限制上传文件类型的话要结合 `file.type`来实现。

#### FileReader接口

主要用来把文件读入内存，并且读取文件中的数据。FileReader 接口有了一个异步 API,使用该 API可以在浏览器主线程中异步访问文件系统，读取文件中的数据。

检查是否可以使用：

```javascript
if(!typeof FileReader){
    // 浏览器为实现 FileReader 接口
}
```

**接口方法**

这个接口拥有4个方法，其中三个用来读取文件，另一个用来读取过程中断

| 方法名             | 参数            | 描述                 |
| ------------------ | --------------- | -------------------- |
| readAsBinaryString | file            | 将文件读取为二进制码 |
| readAsText         | file,[encoding] | 将文件读取为文本     |
| readAsDataURL      | file            | 将文件读取为 DataURL |
| abort              | （none）        | 中断读取操作         |

- readAsBinaryString,这个方法将文件读取为二进制字符串，通常我们把它传送到后端，后端可以通过这段字符串存储文件。
- readAsText,有两个参数，第二个参数是文本的编码方式，默认值为 `UTF-8`。将文件以文本方式读取，读取的结果是这个文本文件中的内容。
- readAsDateURL,该方法将文件读取为一串 Data URL字符串，该方法事实上是将小文件以一种特殊格式的URL 地址形式直接读入页面。这里的小文件通常是图像与 html 格式的文件。

**接口的事件**

除了上面打方法，FileReader 接口还包含了一套完整的事件模型，用于捕获读取文件时的状态。

| 事件        | 描述                                 |
| ----------- | ------------------------------------ |
| onabort     | 数据读取中断时触发                   |
| onerror     | 数据读取出错时触发                   |
| onloadstart | 数据取数开始时触发                   |
| onprogress  | 数据读取中                           |
| onload      | 数据读取成功完成时触发               |
| onloadend   | 数据读取完成时触发，无论成功或者失败 |

```html
<p>
<label for="file">请选择一个文件</label>
<input type="file" id="file" multiple>
<input type="button" onclick="readAsDataURL()" value="读取图像">
<input type="button" onclick="readAsBinaryString()" value="读取二进制数据">
<input type="button" onclick="readAsText()" value="读取文本数据">
</p>
<div name="result" id="result">

</div>
<script>
const result = document.getElementById('result');
const file = document.getElementById('file');
if (!typeof FileReader) {
  alert('浏览器不支持 FileReader 接口')
  file.setAttribute('disabled', 'disabled');
}
function readAsDataURL() {
  // 检查文件是否为图像
  const { files } = file;
  const len = files.length;
  for (let i = 0; i < len; i++) {
    if (!/image\/\w+/.test(files[i].type)) {
      alert('请确保文件都为图像类型');
      return false;
    }
    let reader = new FileReader();
    // 将文件以 Data URL 形式读入页面
    reader.readAsDataURL(files[i])
    reader.onload = function (e) {
      let img = document.createElement('img')
      img.src = this.result
      result.appendChild(img)
    }
  }
}

// 将文件以二进制的形式读入页面
function readAsBinaryString() {
  // 检查文件是否为图像
  const { files } = file;
  const len = files.length;
  for (let i = 0; i < len; i++) {
    if (!/image\/\w+/.test(files[i].type)) {
      alert('请确保文件都为图像类型');
      return false;
    }
    let reader = new FileReader();
    // 将文件以 二进制形式读入页面
    reader.readAsBinaryString(files[i])
    reader.onload = function (e) {
      let p = document.createElement('p')
      p.innerHTML += this.result
      result.appendChild(p)
    }
  }
}

// 将文件以文本形式读入页面
function readAsText() {
  // 检查文件是否为图像
  const { files } = file;
  const len = files.length;
  for (let i = 0; i < len; i++) {
    if (!/image\/\w+/.test(files[i].type)) {
      alert('请确保文件都为图像类型');
      return false;
    }
    let reader = new FileReader();
    // 将文件以 二进制形式读入页面
    reader.readAsText(files[i])
    reader.onload = function (e) {
      let p = document.createElement('p')
      p.innerHTML += this.result
      result.appendChild(p)
    }
  }
}
</script>
```

关于读取状态的先后顺序：

```javascript
function readAsDataURL() {
  // 检查文件是否为图像
  const { files } = file;
  const len = files.length;
  for (let i = 0; i < len; i++) {
    if (!/image\/\w+/.test(files[i].type)) {
      alert('请确保文件都为图像类型');
      return false;
    }
    let reader = new FileReader();
    // 将文件以 Data URL 形式读入页面
    reader.readAsDataURL(files[i])
    reader.onload = function (e) {
      let img = document.createElement('img')
      img.src = this.result
      result.appendChild(img)
      console.log('load')
    }
    reader.onprogress = function(e){console.log('progress');}
    reader.onabort = function(e){console.log('abort');}
    reader.onerror = function(e){console.log('error');}
    reader.onloadstart = function(e){console.log('loadstart');}
    reader.onloadend = function(e){console.log('loadend');}
  }
}
// loadstart
// progress
// load
// loadend
```

在 `onprogress`里面可以用 `progress`来显示文件读取的百分比。

#### 拖放 API

虽然在 HTML5 之前已经可以使用 `mousedown`、`mousemove`、`mouseup`来实现拖放操作，但是这只是在浏览器内容的拖放。在 HTML5 中，支持在浏览器与其他应用程序之间的数据互相拖动，同时也大大简化了拖放方面的代码。

##### 实现拖放的步骤

1. 将想要拖放的对象元素的 `draggable`属性设为 `true`，这样才能将该元素进行拖放。另外，img元素与 a 元素默认运行拖放。
2. 编写与拖放有关的代码。

##### 拖放的相关事件

| 事件      | 产生事件的元素           | 描述                             |
| --------- | ------------------------ | -------------------------------- |
| drastart  | 被拖放的元素             | 开始施放操作                     |
| drag      | 被拖放的元素             | 拖放过程中                       |
| dragenter | 拖放过程中鼠标经过的元素 | 被拖放的元素开始进入本元素的范围 |
| dragover  | 拖放过程中鼠标经过的元素 | 被拖放的元素正在本元素范围内移动 |
| dragleave | 拖放过程中鼠标经过的元素 | 被拖放的元素离开本元素的范围     |
| drop      | 拖放的目标元素           | 有其他元素被拖放到了本元素中     |
| dragend   | 拖放的对象元素           | 拖放操作结束                     |

```html
<body onload="init()">
  <h2>简单拖放示例</h2>
  <div id="dragme" draggable="true" style="width:200px;border:1px solid gray">
    请拖放
  </div>
  <div id="text" style="width:200px;height:200px;border:1px solid gray"></div>
  <script>
    function init() {
      const source = document.getElementById('dragme');
      const dest = document.getElementById('text');
      source.addEventListener('dragstart', function (ev) {
        const dt = ev.dataTransfer;
        dt.effectAllowed = 'all';
        dt.setData('text/plain', '你好')
      }, false);

      dest.addEventListener('dragend', function (ev) {
        // 不执行默认处理（拒绝被拖放）
        ev.preventDefault();
      });
      dest.addEventListener('drop', function (ev) {
        const dt = ev.dataTransfer;
        const text = dt.getData('text/plain');
        dest.textContent += text;
        ev.preventDefault();
        // 禁止事件传播
        ev.stopPropagation();
      }, false)
    }

    // 页面设置属性，不执行默认处理(拒绝被拖放)
    document.ondragover = function (e) { e.preventDefault() }
    document.ondrop = function (e) { e.preventDefault() }
  </script>
</body>
```

- 开始拖动（`dragstart`事件发生）时，将要拖动的数据存入 `DataTransfer`对象（`setData()方法`）。`DataTransfer`对象专门用来存放拖放时要携带的数据，它可以被设置为拖动事件对象的 `dataTransfer`属性。`setData`方法中的第一个参数为携带数据的数据种类的字符串，第二个参数为要修改的数据。第一个参数中表示数据种类的字符串里只能填入类似 `text/plain`或者`text/html`的表示 MIME 类型的文字，不能填入其他文字。
- 如果把`dt.setData("text/plain","你好")`改成 `dt.setData("text/plain",this.id)`。因为把被拖动元素的 id 当成了参数，所以携带的数据就是被拖动元素中的数据了，因为浏览器在使用 `getData()`方法读取数据时会自动读取该元素中的数据。
- 针对拖放的目标元素，必须在 `dragend`或者 `dragover`事件内调用事件对象的 `preventDefault()`方法。因为默认情况下，被拖放的目标元素不允许接受元素的，为了把元素拖放到其中，必须把默认处理给关闭掉。
- 目标元素接受到被拖放的元素后，执行 `getData`方法从 `DataTransfer`获得数据。`getData`方法的参数为 `setData`方法中指定的数据种类
- 要实现拖放过程，还必须在目标元素的 `drop`事件中关闭默认处理（拒绝被拖放），否则目标元素不能接受被拖放的元素
- 实现拖放过程，还必须设定整个页面为不执行默认处理（拒绝被拖放），否则拖放处理也不能被实现。因为页面是先于其他元素接受拖放的，如果页面上拒绝拖放，那么页面上其他元素就都不能接受拖放
- 要使元素可以被拖放，首先必须把该元素的 `draggable`属性设为 `true`，另外，为了让这个示例在所有支持拖放 API 的浏览器中都能正常运行，需要指定 `-webkit-user-drag:element`这种 Webkit 特有的 CSS 属性

现在支持拖放处理的 MIME 类型主要有一下几种：

- text/plain:文本文字
- text/html：HTML文字
- text/xml：xml 文字
- text/uri-list：URL 列表，每个 URL 为一行

##### DataTransfer 对象的属性与方法

| 属性/方法                                      | 描述                                                         |
| ---------------------------------------------- | ------------------------------------------------------------ |
| dropEffect属性                                 | 表示拖放操作的视觉效果，运行对其进行值的设定。该效果必须用 `effectAllowed`属性置顶的允许的效果范围内。允许指定的值为 none、copy、move、link |
| effectAllowed属性                              | 用来指定当元素被拖放时所运行的视觉效果，可以指定的值为none、copy、copyLink、copyMove、link、linkMove、move、all、unintialize |
| types属性                                      | 存入数据的种类，字符串的伪数组                               |
| void clearData(DOMString format)方法           | 清除 DataTransfer 对象中存放的数据，如果省略参数 format,则清除全部数据 |
| void setData(DOMString format、DOMString data) | 向 DataTransfer 对象内存入数据                               |
| DOMString getData(DOMString format)            | 从 DataTransfer 对象中读数据                                 |
| void setDragImage(Element image,long x,long y) | 用 img 元素来设置拖放图标（部分浏览器可以用 canvas 等其他元素来设置） |

##### 设定拖放时的视觉效果

`dropEffect`与 `effectAllowed`属性结合起来可以设定拖放时的视觉效果。`effectAllowed`属性表示当一个元素被拖动时所运行的视觉效果，一般在 `ondragstart`事件中设定，运行使用的值为 none、copy、copyLink、copyMove、link、move、all、unintialize。

`dropEffect`属性表示实际拖放时的视觉效果，一般在 `ondragover`事件中指定，运行设定的值为 none、copy、link、move。`dropEffect`属性所表示的实际视觉效果必须在 `effectAllowed`属性所表示的允许的视觉效果范围内。规则如下：

1. 如果 `effectAllowed`属性被设为 `none`，则不允许拖放元素。
2. 如果 `dropEffect`属性设定为 `none`，则不允许被拖放到目标元素中
3. `effectAllowed`属性设定为 `all`或者不设定，则 `dropEffect`属性允许被设定为任何值，并按照指定的视觉效果进行显示
4. 如果 `effectAllowed`属性设定为具体效果（不为 none或者 all），`dropEffect`属性也设定了具体视觉效果，则两个具体效果值必须完全相等，否则不允许被拖放元素拖放到目标元素中。

```javascript
source.addEventListener('dragstart',function(ev){
    const dt = ev.dataTransfer;
    dt.effectAllowed = 'copy';
    dt.setData("text/plain","你好")
},false);

dest.addEventListener('dragover',function(ev){
    const dt = ev.dataTransfer;
    dt.dropEffect = 'copy';
    ev.preventDefault();
},false);
```

##### 自定义拖放图标

除了上面所说的使用 `effectAllowed`属性与`dropEffect`属性外，HTML5 还允许自定义拖放图标——指的是在用鼠标拖动元素的过程中，位于鼠标指针下部的小图标。

`setDragImage`方法有三个惨呼，第一个参数 image 设定为拖放图标的图标元素，第二个参数 x 为拖放图标离鼠标指针x轴方向的位移量，第三个参数 y 为拖放图标距离鼠标指针的 y 轴方向的位移量。

```javascript
dragIcon.src = 'http://laibh.top/images/favicon-32x32-next.png?v=5.1.4';
source.addEventListener('dragstart', function (ev) {
const dt = ev.dataTransfer;
console.log(ev)
console.log(dt)
dt.effectAllowed = 'all';
dt.setDragImage(dragIcon, -10, -10)
dt.setData('text/plain', '你好')
}, false);
```

