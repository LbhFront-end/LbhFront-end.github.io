---
title: 面试官问我HTML5是什么（下）
date: 2019-05-30  09:30:54
tags: HTML5
categories: 
	- 前端面试


---



## CSS3

发展历史

```shell
CSS1 -> CSS2 -> CSS2.1 ->CSS3
```

**模块与模块化结构**

CSS3采用了分工协作的模块化结构

其中最重要的 CSS3 模块包括：

- 选择器
- 框模型
- 背景和边框
- 文本效果
- 2D/3D 转换
- 动画
- 多列布局
- 用户界面

### CSS3 新增的选择器

**属性选择器**

| 选择器          | 含义                                 | 实例              |
| --------------- | ------------------------------------ | ----------------- |
| *E[att^="val"]* | 属性 `att`的值以 `val`开头的元素     | `div[id^='haha']` |
| *E[att$="val"]* | 属性 `att`的值以 `val`结尾的元素     |  `div[id^='haha']`                 |
| *E[att*="val"] | 属性 `att`的值包含 `val`字符串的元素 | `div[id^='haha']` |



**结构性伪类选择器**

| 选择器                  | 含义                                                         | 实例                                                   |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------ |
| *E:root*                | 匹配文档的根元素，对于 HTML 文档就是 HTML元素                |                                                        |
| *E:not(s)*              | 匹配不符合当前选择器的任何元素                               | `div:not([class="demo"])`:除了`class`为`demo`的div以外 |
| *E:empty*               | 匹配一个不包含任何子元素的元素（文本节点也被看成子元素，空格也是一个元素） |                                                        |
| *E:target*              | 匹配被 `location.hash`选中的元素（即锚点元素），选择器可以用来选取当前活动的目标元素 |                                                        |
| *E:first-child*         | 匹配`E`父元素的第一个子元素,如果当前位置元素不是前面所修饰的元素，那么无效 | `li:first-child`:匹配页面中所有第一个`li`              |
| *E:last-child*          | 匹配`E`父元素的最后一个子元素，等同于 `E:nth-last-child(1)`,如果当前位置元素不是前面所修饰的元素，那么无效 |                                                        |
| *E:nth-child(n)*        | 匹配`E`父元素的第`n`个元素，第一个编号为1,如果当前位置元素不是前面所修饰的元素，那么无效 |                                                        |
| *E:nth--last-child(n)*  | 匹配`E`父元素的倒数第n个子元素，第一个编号为1,如果当前位置元素不是前面所修饰的元素，那么无效 |                                                        |
| *E:nth-of-type(n)*      | 与`E:nth-child()`类似，但是仅匹配使用同种标签的元素          | `p:nth-of-type(2)`:匹配父元素的第2个子元素p            |
| *E:first-of-type(n)*    | 匹配父元素使用同种标签的第一个子元素，等同于 `E:nth-of-type(1)` |                                                        |
| *E:last-of-type(n)*     | 匹配父元素使用同种标签的最后一个子元素，等同于 `E:nth-last-of-type(1)` |                                                        |
| *E:nth-last-of-type(n)* | 与 `E:nth-last-child(n)`类似，但是仅匹配使用相同标签的元素   |                                                        |
| *E:only-child*          | 匹配父元素下仅有的一个子元素，等同于 `E:first-child:last-child`或者 `E:nth-child(1):nth-last-child(1)` |                                                        |
| *E：only-of-type*       | 匹配父元素下使用同种标签的唯一一个子元素，等同于 `E:first-of-type:last-of-type`或者 `E:nth-of-type(1):nth-last-of-type(1)` |                                                        |
| 备注：                  | `n`里面可以是 odd(奇数)、even(偶数)                          | 循环使用样式：`li:nth-child(2n+1)`                     |

**UI状态伪类选择器**

| 选择器         | 含义                               | 实例                                                     |
| -------------- | ---------------------------------- | -------------------------------------------------------- |
| *E:enabled*    | 匹配表单中激活的元素               |                                                          |
| *E:disabled*   | 匹配表单中禁用的元素               |                                                          |
| *E:checked*    | 匹配表单中被选中的单选框或者复选框 |                                                          |
| *E::selection* | 匹配用户用鼠标当前选中的元素       | `user-select: none;`取消鼠标选中的默认样式               |
| *E:read-only*  | 匹配选中只读的元素                 | `<input type="text" readonly="readonly" value="hahha"/>` |
| *E:read-write* | 匹配选中非只读的元素               | `<input type="text" value="hahha"/>`                     |

**级元素通用选择器**

| 选择器 | 含义                                 | 实例                   |
| ------ | ------------------------------------ | ---------------------- |
| `E~F`  | 匹配任何在 `E`元素之后的同级元素 `F` | p~ul{background:#ff9;} |



1. E：hover
2. E：active
3. E：focus
4. E：enabled
5. E：disabled
6. E：read-only
7. E：read-write
8. E：checked
9. E：default
10. E：indeterminate
11. E：selection

### 属性选择器

1. 简单属性选择

```css
/*把包含某个attribute的所有边元素变成红色*/
*[attribute]{color:red}
/*只对有attribute属性的div元素应用样式*/
div[attribute]{color:red}
/*根据多个属性，同时拥有attribute1和attribute2的元div应用样式*/
div[attribute1][attribute2]{color:red}
/*对有alt属性的图像*/
img[alt]{border:5px solid red}
```

2. 根据具体属性选择

```css
/*某个具体的a标签*/
a[href="http://laibh.top"]{color:red};
/*加多一个限制*/
a[href="http://laibh.top"][title="赖同学"]{color:red};
/*不要忽略空格*/
p[class="red small"]{color:red};
```

3. 根据部分属性值选择

```css
/*选择 class属性中包含 important元素的 */
p[class~="important"]{color:red}
```

| 类型         | 描述                                       |
| :----------- | :----------------------------------------- |
| [abc^="def"] | 选择 abc 属性值以 "def" 开头的所有元素     |
| [abc$="def"] | 选择 abc 属性值以 "def" 结尾的所有元素     |
| [abc*="def"] | 选择 abc 属性值中包含子串 "def" 的所有元素 |

4. 特定属性选择类型

```css
/*这个规则会选择 lang 属性等于 en 或以 en- 开头的所有元素*/
*[lang|="en"]{color:red}
```

**css属性选择器表**

| 选择器                  | 描述                                                         |
| :---------------------- | :----------------------------------------------------------- |
| [*attribute*]           | 用于选取带有指定属性的元素。                                 |
| [*attribute*=*value*]   | 用于选取带有指定属性和值的元素。                             |
| [*attribute*~=*value*]  | 用于选取属性值中包含指定词汇的元素。                         |
| [*attribute*\|=*value*] | 用于选取带有以指定值开头的属性值的元素，该值必须是**整个单词**。 |
| [*attribute*^=*value*]  | 匹配属性值以指定值开头的每个元素。（CSS3新增）               |
| [*attribute*$=*value*]  | 匹配属性值以指定值结尾的每个元素。（CSS3新增）               |
| [*attribute\**=*value*] | 匹配属性值中包含指定值的每个元素。（CSS3新增）               |

### 伪类与伪元素

#### 伪类

CSS2定义：用于向某些选择器添加特殊的效果。

CSS3定义：

1. 伪类的存在是为了通过选择器找到那些不存于DOM树中的信息以及不能被常规 CSS选择器获取到的信息（:target）
2. 伪类由**一个**冒号`:`开头，冒号后面是伪类的名称和包含在圆括号中的可选参数
3. 任何常规选择器都可以在任何位置使用伪类。伪类语法不区分大小写。一些伪类的作用会互斥，另外一些伪类可以同时被同一个元素使用。并且，为了满足用户在操作  DOM时产生的 DOM 结构变化，伪类也可以是动态的

```css
selector : pseudo-class{ property:value }
selector.class : pseudo-class{ property:value }
```

**锚伪类**（不存在于 DOM树的信息）

```css
a:link{}
a:visited{}
a:hover{}
a;active{}
```

```html
<style>
.tab_content {
  height: 800px;
  background: red;
  margin-bottom: 100px;
}
#tab1:target, #tab2:target, #tab3:target {
    background:blue;
}    
</style>
<div id="tab1" class="tab_content">
<!--tabed content--></div>
<div id="tab2" class="tab_content">
<!--tabed content--></div>
<div id="tab3" class="tab_content">
<!--tabed content--></div>

<!--:target通过CSS实现了常规CSS无法实现的逻辑-->
```



**伪类表**

| 属性         | 描述                                     | CSS  |
| :----------- | :--------------------------------------- | :--- |
| :active      | 向被激活的元素添加样式。                 | 1    |
| :focus       | 向拥有键盘输入焦点的元素添加样式。       | 2    |
| :hover       | 当鼠标悬浮在元素上方时，向元素添加样式。 | 1    |
| :link        | 向未被访问的链接添加样式。               | 1    |
| :visited     | 向已被访问的链接添加样式。               | 1    |
| :first-child | 向元素的第一个子元素添加样式。           | 2    |
| :lang        | 向带有指定 lang 属性的元素添加样式。     | 2    |

#### 伪元素

CSS2定义：用于向某些选择器设置特殊效果

CSS3定义：

1. 伪元素在DOM树中创建了一些抽象元素，这些抽象元素是不存在文档语言里面的（html源码）。如：document接口不提供访问元素内容的第一个字或者第一行的机制，而伪元素可以使得开发者提取到这些信息。并且，一些伪元素可以使开发者获取到不存在与源文档中的内容（`:before`/`:after`）
2. 伪元素由**两个**冒号`:：`开头，然后是伪元素的名称
3. 使用`::`是为了区分伪类和伪元素（CSS2中没有区别）。考虑兼容性的话，CSS2中的已存伪元素可以使用 `:`，但是 CSS3的就要使用 `::`
4. 一个选择器只能使用一个伪元素，并且伪元素必须处于选择器语句的最后

```css
selector:pseudo-element{property:value}
selector.class:pseudo-element{property:value}
```

`:first-line`

用于向文本的首行设置特殊样式(只能用于块级元素)

下面属性可以应用这个伪元素

- font
- color
- background
- word-spacing
- letter-spacing
- text-decoration
- vertical-align
- text-transform
- line-height
- clear

`:first-letter`

用于向文本的首字母设置特殊样式

下面的属性可以应用这个伪元素

- font
- color
- background
- margin
- padding
- border
- text-decoration
- vertical-align(当 float为none)
- text-transform
- line-height
- float
- clear

`:before`

在元素内容前面插入新内容

`:after`

在元素内容之后插入新内容

**伪元素表**

| 属性          | 描述                             | CSS  |
| :------------ | :------------------------------- | :--- |
| :first-letter | 向文本的第一个字母添加特殊样式。 | 1    |
| :first-line   | 向文本的首行添加特殊样式。       | 1    |
| :before       | 在元素之前添加内容。             | 2    |
| :after        | 在元素之后添加内容。             | 2    |

#### 伪类与伪元素特性与区别

1. 伪类本质上是为了弥补常规 CSS 选择器的不足，以便获取更多的信息（不存在 DOM树的信息，不能被常规CSS选择器获取的信息）
2. 伪元素本质上是创建了一个有内容的虚拟容器,这个容器不包含任何DOM元素，但是可以包含内容
3. CSS3 中的伪类和伪元素的语法不同（`:`与`::`）
4. 可以同时使用多个伪类，而只能同时使用一个伪元素

### 完整表格

| 选择器                 | 示例                   | 示例说明                                                  | CSS  |
| :--------------------- | :--------------------- | :-------------------------------------------------------- | :--- |
| .*class*               | .intro                 | 选择所有class="intro"的元素                               | 1    |
| #*id*                  | #firstname             | 选择所有id="firstname"的元素                              | 1    |
| *                      | *                      | 选择所有元素                                              | 2    |
| *element*              | p                      | 选择所有<p>元素                                           | 1    |
| *element,element*      | div,p                  | 选择所有<div>元素和<p>元素                                | 1    |
| *element* *element*    | div p                  | 选择<div>元素内的所有<p>元素                              | 1    |
| *element*>*element*    | div>p                  | 选择所有父级是 <div> 元素的 <p> 元素                      | 2    |
| *element*+*element*    | div+p                  | 选择所有紧接着<div>元素之后的<p>元素                      | 2    |
| *attribute*            | [target]               | 选择所有带有target属性元素                                | 2    |
| *attribute*=*value*    | [target=-blank]        | 选择所有使用target="-blank"的元素                         | 2    |
| *attribute*~=*value*   | [title~=flower]        | 选择标题属性包含单词"flower"的所有元素                    | 2    |
| *attribute*            | =*language*[lang\|=en] | 选择 lang 属性以 en 为开头的所有元素                      | 2    |
| :link                  | a:link                 | 选择所有未访问链接                                        | 1    |
| :visited               | a:visited              | 选择所有访问过的链接                                      | 1    |
| :active                | a:active               | 选择活动链接                                              | 1    |
| :hover                 | a:hover                | 选择鼠标在链接上面时                                      | 1    |
| :focus                 | input:focus            | 选择具有焦点的输入元素                                    | 2    |
| :first-letter          | p:first-letter         | 选择每一个<P>元素的第一个字母                             | 1    |
| :first-line            | p:first-line           | 选择每一个<P>元素的第一行                                 | 1    |
| :first-child           | p:first-child          | 指定只有当<p>元素是其父级的第一个子级的样式。             | 2    |
| :before                | p:before               | 在每个<p>元素之前插入内容                                 | 2    |
| :after                 | p:after                | 在每个<p>元素之后插入内容                                 | 2    |
| :lang(*language*)      | p:lang(it)             | 选择一个lang属性的起始值="it"的所有<p>元素                | 2    |
| *element1*~*element2*  | p~ul                   | 选择p元素之后的每一个ul元素                               | 3    |
| *attribute*^=*value*   | a[src^="https"]        | 选择每一个src属性的值以"https"开头的元素                  | 3    |
| *attribute*$=*value*   | a[src$=".pdf"]         | 选择每一个src属性的值以".pdf"结尾的元素                   | 3    |
| *attribute*=*value*    | a[src*="runoob"]       | 选择每一个src属性的值包含子字符串"runoob"的元素           | 3    |
| :first-of-type         | p:first-of-type        | 选择每个p元素是其父级的第一个p元素                        | 3    |
| :last-of-type          | p:last-of-type         | 选择每个p元素是其父级的最后一个p元素                      | 3    |
| :only-of-type          | p:only-of-type         | 选择每个p元素是其父级的唯一p元素                          | 3    |
| :only-child            | p:only-child           | 选择每个p元素是其父级的唯一子元素                         | 3    |
| :nth-child(*n*)        | p:nth-child(2)         | 选择每个p元素是其父级的第二个子元素                       | 3    |
| :nth-last-child(*n*)   | p:nth-last-child(2)    | 选择每个p元素的是其父级的第二个子元素，从最后一个子项计数 | 3    |
| :nth-of-type(*n*)      | p:nth-of-type(2)       | 选择每个p元素是其父级的第二个p元素                        | 3    |
| :nth-last-of-type(*n*) | p:nth-last-of-type(2)  | 选择每个p元素的是其父级的第二个p元素，从最后一个子项计数  | 3    |
| :last-child            | p:last-child           | 选择每个p元素是其父级的最后一个子级。                     | 3    |
| :root                  | :root                  | 选择文档的根元素                                          | 3    |
| :empty                 | p:empty                | 选择每个没有任何子级的p元素（包括文本节点）               | 3    |
| :target                | #news:target           | 选择当前活动的#news元素（包含该锚名称的点击的URL）        | 3    |
| :enabled               | input:enabled          | 选择每一个已启用的输入元素                                | 3    |
| :disabled              | input:disabled         | 选择每一个禁用的输入元素                                  | 3    |
| :checked               | input:checked          | 选择每个选中的输入元素                                    | 3    |
| :not(*selector*)       | :not(p)                | 选择每个并非p元素的元素                                   | 3    |
| ::selection            | ::selection            | 匹配元素中被用户选中或处于高亮状态的部分                  | 3    |
| :out-of-range          | :out-of-range          | 匹配值在指定区间之外的input元素                           | 3    |
| :in-range              | :in-range              | 匹配值在指定区间之内的input元素                           | 3    |
| :read-write            | :read-write            | 用于匹配可读及可写的元素                                  | 3    |
| :read-only             | :read-only             | 用于匹配设置 "readonly"（只读） 属性的元素                | 3    |
| :optional              | :optional              | 用于匹配可选的输入元素                                    | 3    |
| :required              | :required              | 用于匹配设置了 "required" 属性的元素                      | 3    |
| :valid                 | :valid                 | 用于匹配输入值为合法的元素                                | 3    |
| :invalid               | :invalid               | 用于匹配输入值为非法的元素                                | 3    |

### 在页面插入内容

`:before`或者 `:after`

```css
/* 插入文字 */
/* 在h2前 */
h2:before{
    content:'哈哈哈'
}
/* 在h2后 */
h2:after{
    content:'内容'
}
/* 指定个别元素不进行插入 */
h2.sample:before{
    content:none
}

/* 插入图像 */
/* 在h2前 */
h2:bofore{
    content:url(mark.png);
}
/* 在h2后 */
h2:after{
    content:url(mark.png);
}
/* 将元素属性作为content的值来显示 */
img:after{
    context:attr(alt);
    display:block;
    text-align:center;
    margin-top:5px;
}
```

**`content`插入项目编号**

```html
<h1>大标题</h1>
<p>实例文字</p>
<h1>大标题</h1>
<p>实例文字</p>
```

```css
/*多个标题加上连续编号*/
h1:before{
	content:counter(mycounter)
}
h1{
	counter-increment:mycounter;
}
/*追加文字*/
h1:before{
	content:'第'counter(mycounter)'章'
}
h1{
	counter-increment:mycounter;
}
/* 指定样式 */
h1:before{
	content:'第'counter(mycounter)'章';
	color:blue;
	font-size:42px;
}
h1{
	counter-increment:mycounter;
}
/* 指定编号种类 list-style-type的值*/
/* content(计数器名，编号种类)*/
h1:before{
	content:'第'counter(mycounter,lower-roman)'章';
	color:blue;
	font-size:42px;
}
/* 编号嵌套 */
p:bofore{
    content:counter(mycounter2,lower-roman);
}
p{
    counter-increment:mycounter2;
}
```



list-style-type 的值

| 值                   | 描述                                                        |
| :------------------- | :---------------------------------------------------------- |
| none                 | 无标记。                                                    |
| disc                 | 默认。标记是实心圆。                                        |
| circle               | 标记是空心圆。                                              |
| square               | 标记是实心方块。                                            |
| decimal              | 标记是数字。                                                |
| decimal-leading-zero | 0开头的数字标记。(01, 02, 03, 等。)                         |
| lower-roman          | 小写罗马数字(i, ii, iii, iv, v, 等。)                       |
| upper-roman          | 大写罗马数字(I, II, III, IV, V, 等。)                       |
| lower-alpha          | 小写英文字母The marker is lower-alpha (a, b, c, d, e, 等。) |
| upper-alpha          | 大写英文字母The marker is upper-alpha (A, B, C, D, E, 等。) |
| lower-greek          | 小写希腊字母(alpha, beta, gamma, 等。)                      |
| lower-latin          | 小写拉丁字母(a, b, c, d, e, 等。)                           |
| upper-latin          | 大写拉丁字母(A, B, C, D, E, 等。)                           |
| hebrew               | 传统的希伯来编号方式                                        |
| armenian             | 传统的亚美尼亚编号方式                                      |
| georgian             | 传统的乔治亚编号方式(an, ban, gan, 等。)                    |
| cjk-ideographic      | 简单的表意数字                                              |
| hiragana             | 标记是：a, i, u, e, o, ka, ki, 等。（日文片假名）           |
| katakana             | 标记是：A, I, U, E, O, KA, KI, 等。（日文片假名）           |
| hiragana-iroha       | 标记是：i, ro, ha, ni, ho, he, to, 等。（日文片假名）       |
| katakana-iroha       | 标记是：I, RO, HA, NI, HO, HE, TO, 等。（日文片假名）       |

## 参考链接

1. [CSS3伪类和伪元素的特性和区别](https://www.cnblogs.com/ihardcoder/p/5294927.html)
2. [HTML 5与CSS 3权威指南](https://read.douban.com/ebook/15160963/)
3. [W3cScholl](https://www.w3cschool.cn/html/)
4. [CSS3新增的选择器和属性](https://www.cnblogs.com/dreamingbaobei/p/5062998.html)
5. [神奇的css3（1）新增属性、选择器](https://www.cnblogs.com/wangzhenling/p/8824093.html)