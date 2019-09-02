---
title: flex布局重温
date: 2018-05-10 12:00:00
tags: flex
categories: 
	- 界面布局
---

<div class="jquery-head">
       <center><h1>flex布局重温</h1></center>
</div> 

flex布局重温
=============
flex布局重温  
一、Flex是什么  
Flex布局是Flexible Box 的缩写。意为“弹性布局”，为盒装模型提供最大的灵活性，任何一个容器都可以指定为Flex布局。  

```css
.box{
    display:flex;
}
```
行内元素也可以使用Flex布局  

```css
.box{
    display:inline-box;
}
```
Webkit内核的浏览器，必须加上 `-webkit` 前缀

```css
.box{
    display:-webkit-flex;/*Safair*/
    display:flex;
}
```
注意设为Flex布局以后，子元素的 `float`、`clear`、`vertical-align` 属性将失效

二、基本概念  
采用Flex布局的元素，称为Flex容器（flex container）,简称“容器”。它的所有子元素自动成为容器成员，称为Flex项目（flex item）,简称“项目”。
容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置与边框的交叉点叫做 `main start` ，结束位置叫做 `main end`；交叉轴的开始位置叫做 `cross start`,结束位置叫做 `cross end`。
项目默认沿主轴排列。单个项目占据的主轴空间 `main size`，占据的交叉轴空间叫做 `cross size`

三、容器的属性  
▶ flex-direction  
▶ flex-wrap  
▶ flex-flow  
▶ justify-content  
▶ align-items  
▶ align-content  

3.1 flex-direction属性  
`flex-direction` 属性决定主轴的方向（即项目的排列方向）  

```css
.box{
    flex-direction:row | row-reverse | column | column-reverse;
}
```

>▪ row: 主轴为水平方向，起点在左端  
▪ row-reverse: 主轴为水平方向，起点在右端  
▪ column : 主轴为垂直方向，起点在上沿  
▪ column-reverse : 主轴为垂直方向，起点在下沿  
▪ row: 主轴为水平方向，起点在左端  

3.2 flex-wrap属性  
默认情况下，所有的项目都排在一条线（又称“轴线”）上。`flex-wrap` 属性定义，如果一条轴线排不下，如何换行。  

```css
.box{
    flex-wrap:nowrap | wrap | wrap-reverse
}
```

>▪ nowrap:不换行  
▪ wrap:换行，第一行在上方  
▪ wrap-reverse:换行，第一行在下方  

3.3 flex-flow  
`flex-flow` 属性是 `flex-direction` 属性和 `flex-wrap` 属性的简写形式，默认值为 `row nowrap`  

```css
.box{
    flex-flow: <flex-direction> || <flex-wrap>
}
```

3.4 justify-content属性  
`justify-content` 属性定义了项目在主轴上的对齐方式  

```css
box{
    justify-content:flex-start | flex-end | center | space-between | space-around;
}
```

>▪ flex-start:左对齐  
▪ flex-end:右对齐  
▪ center:居中  
▪ space-between:两端对齐，项目之间的间隔都相等  
▪ space-around:每个项目两侧的间隔相等，所以，项目之间的间隔比项目与边框的间隔大一倍

3.5 align-items属性  
`align-items`属性定义项目在交叉轴上如何对齐  

```css
.box{
    align-item:flex-start | flex-end | center | baseline | stretch
}
```
>▪ flex-start:交叉轴的起点对齐  
▪ flex-end:交叉轴的终点对齐  
▪ center:交叉轴的中点对齐  
▪ baseline:项目的第一行文字的基线对齐  
▪ stretch:如果项目未设置高度或设为auto，将占满整个容器的高度

3.6 align-content属性  
`align-content` 属性定义了多根轴线的对齐方式，如果项目只有一条轴线，该属性不起作用。  

```css
.box{
    align-content:flex-start | flex-end | center | space-between |space-around | stretch;
}
```

>▪ flex-start:交叉轴的起点对齐  
▪ flex-end:交叉轴的终点对齐  
▪ center:交叉轴的中点对齐  
▪ space-between:与交叉轴两端对齐，轴线之间的间隔平均分布  
▪ space-around:每根轴线两侧的间隔都相等，所以轴线之间的间隔比轴线与边框的间隔大一倍  
▪ stretch:轴线占满整个交叉轴

四、项目的属性  
>▪ order:属性定义项目的排序顺序。数值越小，排列越靠前，默认为0  
▪ flex-grow:属性定义了项目的放大比例，默认为0，即如果存在剩余空间，也不放大，如果所有属性都为1,则平分所有剩余空间。若有一个为2，其他都为1，则前者占据的剩余空间将比其他项多一倍  
▪ flex-shrink:属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小  
▪ flex-basis:属性定义了在分配多余空间之间，项目占据的主轴空间（main size），浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。  
▪ flex:flex属性是`flex-grow` , `flex-shrink` 和 `flex-basis` 的简写，默认值为0 1 auto。后两个属性可选。  
▪ align-self: `align-self` 属性允许单个项目有与其他项目不一样的对齐方式，可覆盖 `align-items` 属性。默认值为 `auto` ，表示继承父元素的 `align-items` 属性，如果没有父元素，则等同于 `stretch`。