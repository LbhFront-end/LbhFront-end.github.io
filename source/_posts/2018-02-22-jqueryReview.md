---
title: Jquery复习（视频心得）
date: 2018-02-22 17:12:32
tags: jQuery
categories: 
	- jQuery相关
---

<div class="jquery-head">
       <center><h1>Jquery的一些基础回顾复习</h1></center>
</div> 

jquery函数
=============

`$(document).ready(fn) / $(fn)`

 1. `window.onload` 与 `$(document).ready(fn)的区别`<br/>
    1.1 `window.onload` 需要等待页面完全加载完毕才能触发，即所有的Dom元素创建完毕、图片、Css等加载完毕后才可以触发。<br/>                       `$(document).ready()` 只要Dom元素加载完成就可以触发，这样可以提供响应速度<br/>
    1.2 `$(document).ready()`可以注册多次事件处理程序，并且最终都会执行</br>而 `window.onload` 每次注册新的事件处理程序时都会将前面的覆盖掉

 2. 使用jQuery实现 `window.onload` 的效果：`$(window).load(fn)`



`$.map(array,callback(element,index))`

对于数组 `array` 中的每个元素，调用 `callback()` 函数，最终返回一个新的数组，原数组不变

Example： 

```javascript
var arrInt = [1, 2, 3, 4, 5, 6, 7];   
var arrIntNEW = $.map(arrInt, function(element, index){
    if (index > 3){
        return element * 2;      
    } else {
        return element;
    }
});
console.log(arrIntNEW);
```



`$.each(array,fn)`

遍历数组，通过 `return false` 来退出循环，不能使用 `break` 来提出循环

 Example：

```javascript
var arrInt = [1, 2, 3, 4, 5, 6, 7];   
    $.each(arrInt, function(key, value){
        if(key == 3){
            return false;  //跳出循环
        }
    //当使用each遍历，普通的数组的时候，索引就是键，值就是值
    console.log(key + ' , ' + value);
});
```



`$.trim(字符串)`

去掉两端空格，启动调试，可以F11进去源码查看原理

Example：

```javascript
var msg = '   hello   ';   
console.log(msg);
console.log($.trim(msg));  //去掉两边空格
```



jquery对象与Dom对象
=============

Example：

```javascript
window.onload = function(){
    document.getElementById('btn').onclick = function(){
    //获取层对象
    //从页面上的元素对象才叫做dom对象，数值，日期对象，这些都不是dom对象
    var dvObj = document.getElementById('div1');
    dvObj.innerText = 'Hello!';
    //当直接使用dom对象的时候存在浏览器兼容性问题
    //为了解决浏览器的兼容性问题，所以这时可以把dom对象转为jQuery对象，然后再操作
    //把dom对象转换为jQuery对象后，就可以调用对应的所有的jQuery对象的成员了               
    //  Dom -> jQuery
    var $dvObj = $(dvOjb); 
    $dvObj.text('您好！);

    //  jQuery -> Dom (两种方法)
    1. var domDiv = $dvObj[0];
    2. var domDiv = $dvObj.get(0);
    domDiv.innerText('我现在是dom对象书写出来的文字');

    //jQuery对象：
    //可以把dom对象装换为jQuery对象
    //也可以把jQuery对象转为dom对象
    }
}
```


jquery选择器
=============
1. Id选择器 $("#id")
2. 标签选择器 $("input");
3. 类选择器 $(".class");
4. 标签+类选择器 $("p.text");
5. 组合选择器 $("选择器1","选择器2","选择器3"，"......");
6. 层次选择器
    后代元素选择器：使用空格隔开 \$("选择器1 选择器2")，表示选取选择器1下所有的选择器2
    子元素选择器：使用 `>` 隔开 \$("选择器1 > 选择器2")
    相邻元素选择器：使用 `+` 或者 `~` 
        `+`：$(".a + div") == $(".a").next 如果相邻的不是div 则不会继续向后找
        `~`：$(".a ~ div") == $(".a").nextAll() 获取类名为a之后的所有兄弟div元素
    \$("div").prev("span"); 获取div上一个兄弟元素，并且该元素必须是span
    \$("div").prevAll("span"); 获取div前面的所有span兄弟元素
    \$("div").siblings("span"); 获取div的所有span兄弟元素



特点：链式方程/隐式迭代

Example1:
                  
```javascript
$(function(){
    $("#bthSetText").click(function(){
        $("p").text("我被改变了").css("background","#ff9").mouseover(function(){
            $(this).css("background","#ff9").siblings().css("background","");
        });
    });
});
```

Example2:

```html

<ul id="target">
<li>公牛</li>
<li>小牛</li>
<li>牛奶</li>
<li>母牛</li>
</ul>

<scirpt>
$(function(){
    $("#target li").mouseover(function() {
        //鼠标悬浮事件
        $(this).css("background","#ff9").siblings('li').css("background","");
    }).click(function(){
        //单击事件
        //有些方法会破坏链式编程中返回的对象，比如：		next(),nextAll(),prev(),prevAll(),siblings()
        //无参数的：text()、val()、html()
        //当链式编程的链被破坏时可以用end()修复
    $(this).prevAll().css("background","green").end().nextAll().css("background","blue");
});
});
</scirpt>

```

jQuery1.6以后有以下区别，1.6之前都是用`attr`
>`attr()` 与  `prop()`
>对于HTML元素本身就带有的 **固有** 属性，在处理时，使用 `prop` 方法。
>对于HTML元素我们自己 **自定义** 的DOM属性，在处理时，使用 `attr` 方法。


过滤选择器（`:`）
===================
`:first` 选取 **第一个** 元素 `$("div:first")`  
`:last` 选取 **最后一个** 元素 `$("div:last")`  
`:not(选择器)`选取 **不满足选择器** 条件的元素 `$("input:not(.myClass)")`  
`:even` \ `:odd` 选取索引是 **偶数** 、**奇数** 的元素 `$("input:even")`   
`:eq(索引序号)` \ `：gt(索引序号)` \ `:lt(索引序号)` 选取索引 **等于** 、**大于** 、**小于** 索引号的元素 `$("input:lt(5)")` | `$("input:lt(3):gt(0)")`  
`:animated` 选取正在 **执行动画** 的 `div` 元素  
`$(".header")` 选取所有 **h1...h6** 元素  

Example1:     

```html
<input type="button" name="" id="btn" value="点我">
<table id="t" border="1">
    <tr>
        <td>姓名</td>
        <td>成绩</td>
    </tr>		
    <tr>
        <td>a</td>
        <td>100</td>
    </tr>		
    <tr>
        <td>b</td>
        <td>99</td>
    </tr>		
    <tr>
        <td>c</td>
        <td>98</td>
    </tr>		
    <tr>
        <td>d</td>
        <td>97</td>
    </tr>			
    <tr>
        <td>e</td>
        <td>98</td>
    </tr>		
    <tr>
        <td>f</td>
        <td>97</td>
    </tr>		
    <tr>
        <td>平均分</td>
        <td>97</td>
    </tr>
</table>
<script>
    $(function(){
        $("#btn").click(function(){
            $("#t tr:first").css("font-size","70px");
            $("#t tr:gt(0):lt(3)").css("font-size","28px");
            $("#t tr:last").css("color","red");
            $("#t tr:odd:not(:last)").css("background","red");
        });
    });
</script>
```

Example2: 

```html
<table id="b1" border="1" cellpadding="2" cellspacing="2">
    <tr>
        <td>Aa</td>
        <td>Aa</td>
        <td>Aa</td>
        <td>Aa</td>
        <td>Aa</td>
    </tr>		
    <tr>
        <td>Aa</td>
        <td>Aa</td>
        <td>Aa</td>
        <td>Aa</td>
        <td>Aa</td>
    </tr>		
    <tr>
        <td>Aa</td>
        <td>Aa</td>
        <td>Aa</td>
        <td>Aa</td>
        <td>Aa</td>
    </tr>		
    <tr>
        <td>Aa</td>
        <td>Aa</td>
        <td>Aa</td>
        <td>Aa</td>
        <td>Aa</td>
    </tr>		
    <tr>
        <td>Aa</td>
        <td>Aa</td>
        <td>Aa</td>
        <td>Aa</td>
        <td>Aa</td>
    </tr>
</table>
<script>
    $(function(){
        $("#b1 tr").click(function() {
            $("td").css("background","");
            $("td:even",$(this)).css("background","red"); //$(this) == $("#b1 tr")
            $("td:odd",$(this)).css("background","blue");
        });
    });
</script>
```

**$("td:even",$(this))** 表示在上文鼠标所点击的 `tr` 里面来选择 `td` 

属性过滤选择器
---------------
`$("div[id]")` 选取有 **id** 属性的 `<div>`  
`$("div[title=test]")` 选取 **title** 属性为test的 `<div>`  
`$("div[title!=test]")` 选取 **title** 属性不为test的 `<div>`  
Example1:     

```html
<input type="text" name="a1">
<input type="text" name="a2">
<input type="text" name="a3">
<input type="text" name="a4">
<input type="text" name="a5">
<input type="text" name="a6">
<input type="text" name="a7">
<input type="text" name="a8">
<script>
    //1.选取所有input属性，具有name属性的
    $("input[name]").css();
    //2.选取所有body下面具有name属性的元素
    $("body *[name]").css();
    //3.选取页面上具有name属性，同时name属性的开头为a的元素
    $("body *[name^=a]").css();
    //4.选取name属性为空的
    $("body *[name=]").css();
    //5.选取以name属性中a结尾的
    $("body *[name$=a]").css();
    //6.包含a的name的属性的元素
    $("body *[name*=a]").css();
    //7.name属性中不等于某个属性的
    $("body *[name!='spec']").css();
    //8.同时具有多个属性的
    $("body *[name*=a] [id] [value]").css();
</script>

```


表单选择器
---------------------
`$(":input")` 选取所有`<input>`、`<textarea>`、`<select>` 和 `<button>`  
`$("input")` 不一样，只获得 `<input>`  
`$(":text")` == `$("input[type=text]")` 选取所有的单行文本框  
`$(":password");` 选取所有密码框  
上述同理的还有 `:radio`、`:submit`、`:image`、`:reset`、`:button`、`:file`、`:hidden` （替代了`$("input[type=***]")`）



表单对象属性过滤选择器
---------------------
`$("#form :enabled")` 选取id为form的表单内所有 **启用** 的元素  
`$("#form :disabled")` 选取id为form的表单内所有 **禁用** 的元素  
`$("#input:checked")` 选取所有 **选中** 的元素(Radio/Checkbox),中间不可有空格  
`$("select :selected")` 选取所有选中的 **选项** 的元素(下拉列表)