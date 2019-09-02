---
title: javascript匿名函数
date: 2018-03-21 17:12:12
tags: js
categories: 
    - javaScript相关
---

<div class="jquery-head">
       <center><h1>JavaScript匿名函数</h1></center>
</div> 

匿名函数
=============

匿名函数：  

```javascript
(function(window,undefied){
    //jQuery code
})(window);
```

1. 这个一个自调用匿名函数。第一个括号创建了一个匿名函数，第二个括号立即执行
2. 通过定义一个这样的匿名函数，创建了一个“私有”的命名空间，该命名空间的变量和方法，不会破坏全局的命名空间。这点非常有用也是一个Js框架必须支持的功能。jQuery被应用在成千上万的js程序中，必须保证jQuery创建的变量不能与导入他的程序所使用的变量有所冲突。
3. 匿名函数从语法上叫函数直接量，js语法需要包围匿名函数的括号，事实上调用匿名函数的有两种写法：  

   ```javascript
    (function(){
        console.info(this);
        console.info(arguments);
    }(window);)
   
    (function(){
        console.info(this);
        console.info(arguments);	
    })(window);
   ```
