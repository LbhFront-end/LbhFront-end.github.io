---
title: 水平垂直居中对齐的几种方案
date: 2018-05-11 14:01:44
tags: css
categories: 
	- 界面布局
---

水平垂直居中
=============
```html
<div class="box1"></div>
<div class="box2"></div>
<div class="box3"></div>
<div class="box4">
	<div></div>
</div>
<div class="box5">
	<div class="content">
		<div class="inner">
		</div>
	</div>
</div>   
```

实现div水平垂直居中的几个方案  
一、margin:auto实现绝对定位元素的居中，兼容性：ie7之前的版本不适用

```css
.box1{
    width: 200px;
    height: 200px;
    background: green;
    position: absolute;
    left:0;
    right: 0;
    bottom:0;
    top: 0;
    margin: auto;
}
```

二、margin负间距  

```css
.box2{
    width:200px;
    height: 200px;
    background:red;
    position: absolute;
    left:50%;
    top:50%;
    margin-left: -100px;
    margin-top: -100px;
}
```

三、transforms 变形 兼容性：ie8不支持  

```css
.box3{
    width:200px;  
    height: 200px;
    background:blue;
    position: absolute;
    left:50%; /*定位父级的50%*/
    top:50%;
    transform:translate(-50%,-50%);/*自己的50%*/
}
```

四、css不定宽水平垂直居中（高要设置）  

```css
.box4{		
    display: flex;
    justify-content: center;
    align-items: center;
}
.box4>div{
    background:#ff9;
    width: 200px;
    height: 200px;
}
```

五、table-cell布局 
table-cell相当与表格的td，td为行内元素，无法设置宽和高，所以嵌套一层，嵌套一层必须设置display: inline-block;td的背景覆盖了橘黄色，不推荐使用

```css
.box5{
    background-color: #FF8C00;/*橘黄色*/
    width: 300px;
    height: 300px;
    display: table;
}
.content{
    background-color: #F00;/*红色*/
    display: table-cell;
    vertical-align: middle;/*使子元素垂直居中*/
    text-align: center;/*使子元素水平居中*/
}
.inner{
    background-color: #000;/*黑色*/
    display: inline-block;
    width: 20%;
    height: 20%;
}
```

