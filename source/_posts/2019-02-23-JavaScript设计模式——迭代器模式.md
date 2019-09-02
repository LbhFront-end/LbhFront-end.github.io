---
title: JavaScript设计模式——迭代器模式
date: 2019-02-23 11:30:00
tags: JavaScript设计模式
categories: 
	- JavaScript设计模式
---

学习曾探的 《JavaScript设计模式与开发实践》并做记录。

[书籍的购买链接](http://www.ituring.com.cn/book/1632/)

设计模式的定义是：在面向对象软件设计过程中针对特定问题的简洁而优雅的解决方案。

通俗一点，设计模式是在某种场合下对某个问题的一种解决方案。是给面向对象软件开发中的一些好的设计取个名字。

# JavaScript设计模式——迭代器模式

迭代器模式是指提供一种方法顺序访问一个聚合对象中的各个元素，而又不需要暴露该对象的内部表示。迭代器模式可以把迭代的过程从业务逻辑中分离出来，在使用迭代器模式之后，即使不关心对象的内部构造，也可以按顺序访问其中的每个元素。

## jQuery中的迭代器

迭代器模式无非是循环访问聚合对象中的各个元素。比如jQuery 中的 $.each 函数，其中回调函数中的参数 i 为当前索引，n 为当前元素，代码如下：

```javascript
$.each([1,2,3],function(i,n){
    console.log('当前小标为：'+i);
    console.log('当前值为：'+n);
});
```

## 实现自己的迭代器

我们来实现自己的一个 each 函数，each 函数接受2个参数，第一个参数为被循环的数组，第二个参数为循环中的每一步后被触发的回调函数。

```javascript
var each = function(ary,callback){
    for(var i=0,l=ary.length;i<l;i++){
        callback.call(ary[i],i,ary[i]); // 把下标和元素当做参数传给 callback 函数
    }
}

each([1,2,3],function(i,n){
    console.log(i,n);
});
```

### 内部迭代器和外部迭代器

迭代器可以分为内部迭代器和外部迭代器。

### 1.内部迭代器

上面的 each 函数属于内部迭代器，each 函数的内部已经定义好了迭代规则，它完全接手了整个迭代过程，外部只需要一次初始调用。

内部迭代器在调用的时候非常方便，外界根本不需要关心迭代器内部的实现，跟迭代器的交互也仅仅是一次初始调用，但这也是内部迭代器的缺点。由于内部迭代器的迭代规则已经被提前规定，上面的 each 函数就无法同时迭代两个数组了。

比如现在有一个需求，需要判断2个数组元素的值是否完全相等，如果不改写 each 函数本身的代码，我们能够入手的就只有 each 函数的回调函数了，代码如下：

```javascript
var each = function(ary,callback){
    for(var i=0,l=ary.length;i<l;i++){
        callback.call(ary[i],i,ary[i]); // 把下标和元素当做参数传给 callback 函数
    }
}

each([1,2,3],function(i,n){
    console.log(i,n);
});


var compare = function(ary1,ary2){
    if(ary1.length !== ary2.length){
        throw new Error('ary1 和 ary2 不相等');
    }
    each(ary1,function(i,n){
        if(n !== ary2[i]){
            throw new Error('ary1 和 ary2 不相等');
        }
    })
    console.log('ary1 和 ary2 相等');
}

compare([1,2,3],[1,2,3]);
```

在一些没有闭包的语言中，内部迭代器本身的实现也相当复杂，比如 C 语言中的内部迭代器是用函数的指针来实现的，循环处理需要的数据都要以参数的形式明确地从外部传递进去。

### 2.外部迭代器

外部迭代器必须显式地请求迭代下一个元素。

外部迭代器增加了一些调用的复杂度，但相对也增强了迭代器的灵活性，可以手工控制迭代器的过程或者是顺序。

```javascript
var Iterator = function(obj){
    var current = 0;
    var next = function(){
        current += 1;
    }
    var isDone = function(){
        return current >= obj.length;
    }
    var getCurrItem = function(){
        return obj[current];
    }
    return{
        next,
        isDone,
        getCurrItem
    }
}

// 改写 compare 函数
var compare = function(iterator1,iterator2){
    while(!iterator1.isDone() && !iterator2.isDone()){
        if(iterator1.getCurrItem() !== iterator2.getCurrItem()){
            throw new Error('iterator1 和 iterator2 不相等');
        }
        iterator1.next();
        iterator2.next();
    }
    console.log('iterator1 和 iterator2 相等');
}
compare(Iterator([1,2,3]),Iterator([1,2,4]));
```

外部迭代器虽然调用方式相对复杂，但它的适用面更广，也能满足更多变的需求，内部迭代器和外部迭代器在实际生产中没有优劣之分，使用哪个要根据需求场景而定。

## 迭代类数组对象和字面量对象

迭代器模式不仅可以迭代数组，还可以迭代一些类数组的对象。比如 arguments 、{“0”：a,"1"：b}等。通过上面的代码可以观察到，无论是内部迭代器还是外部迭代器，只要被迭代的聚合对象拥有 length 属性而且可以用下标访问，那它就可以被迭代。

在 JavaScript 中，for in语句可以用来迭代普通字面量对象的属性， jQuery 中提供了 $.each 函数来封装各种迭代行为。

```javascript
$.each = function(obj,callback){
    var value,
    i,
    length = obj.length,
    isArray = true;
    if(isArray){ // 迭代类数组
        for(;i<length;i++){
            value = callback.call(obj[i],i,obj[i]);
            if(value === false){
                break;
            }
        }
    }else{
        for(i in obj){
            value = callback.call(obj[i],i,obj[i]);
            if(value === false){
                break;
            }
        }
    }
    return obj;
}
```

## 倒序迭代器

迭代器模式提供了循环访问一个聚合对象中每个元素的方法，但它没有规定我们以顺序还是倒序甚至是 中序来循环遍历对象。

实现一个倒序遍历的迭代器：

```javascript
var reverseEach = function(ary,callback){
    for(var l = ary.length-1; l >= 0; l--){
        callback.call(ary[l],l,ary[l]);
    }
}
reverseEach([1,2,3],function(i,n){
    console.log(i,n);
});
```

## 中止迭代器

迭代器可以像普通 for 循环中的 break 一样，提供一种跳出循环的方法，在 jQuery 的 each 函数中有这样的一句话

```javascript
if(value === false){
   break;
}
```

这句话的意思是，约定如果回调函数的执行结果返回 false ，则提前终止循环。

我们可以把之前的 each 函数改写一下

```javascript

var each = function(ary,callback){
    for(var i=0,l=ary.length;i<l;i++){
        if(callback.call(ary[i],i,ary[i]) === false){
           break;
        }
    }
}
each([1,2,3,4,5],function(i,n){
    if(n > 3){
       return false;
    }
    console.log(n);
});
```

## 迭代器模式的应用举例

当作者重构某个项目中文件上传模式的代码的时候，他发现了下面这段代码，它的目的是根据不同的浏览器获取相应的上传组件对象：

```javascript
var getUploadObj = function(){
    try{
        return new ActiveXObject('TXFTNActiveX.FTNUpload'); // IE 上传控件
    }catch(e){ 
        if(supportFlash()){
            var str = '<object type="application/x-shockwave-flash"></object>'
            return $(str).appendTo($('body'));
        }else{
            var str = '<input type="file" name="file" />' // 表单上传
            return $(str).appendTo($('body'));
        }
    }
}
```

在不同浏览器环境下，选择的上传方式不同，因为使用浏览器的上传空间进行上传速度快，可以暂停和续传，所以我们会首先选择使用控件上传 ，如果浏览器没有安装上传控件，则使用 flash 上传，如果 连 Flash 也没有上传就使用浏览器原生的表单上传了。

上面的代码可读性差，严重违反了开闭原则。在开发和调试中我们需要来回切换不同的上传方式，后来还支持了 HTML5 上传，这时候唯一的方法就是继续往 getUploadObj 函数里面增加分支条件。

梳理一下问题，目前一共有 3 种可能上传的方式，我们不知道正在使用的浏览器支持哪几种。就好比我们有一个钥匙串，其中共有3把钥匙，我们想要打开一扇门但是不知道该使用哪把钥匙，于是从第一把钥匙开始，迭代钥匙串进行尝试，直到找到正确的钥匙为止。

同样，我们把每种获取 upload 对象的方法都封装在各自的函数里，然后使用一个迭代器，迭代获取这些 upload 对象，知道获取到一个可用的为止：

```javascript
var getActiveUploadObj = function(){
    try{
        return new ActiveXObject('TXFTNActiveX.FTNUpload');
    }catch(e){
        return false;
    }
}

var getFlashUploadObj = function(){
    if(supportFlash()){
        var str = '<object type="application/x-shockwave-flash"></object>'
        return $(str).appendTo($('body'));
    }
    return false;
}
var getFormUploadObj = function(){
    var str = '<object type="application/x-shockwave-flash"></object>'
    return $(str).appendTo($('body'));
}
```

在上面3个函数中都有同一个约定：如果该函数里面的 upload 对象是可用的，则让函数返回该对象，反之则返回 false,提示迭代器继续往后面进行迭代。

所以我们的迭代器只需要进行下面的几步工作。

- 提供一个可以被迭代的方法，使得上面三个函数依照优先级被循环迭代
- 如果正在跌打的函数返回一个对象，则表示找打了正确 的上传对象，反之如果该函数返回 false，则让迭代器继续工作。

迭代器代码如下：

```javascript
var iteratorUploadObj = function(){
    for(var i=0,fn;fn=arguments[i++];){
        var uploadObj = fn();
        if(uploadObj !== false){
            return uploadObj;
        }
    }
}
var uploadObj = iteratorUploadObj(getFlashUploadObj,getFormUploadObj,iteratorUploadObj);
```

重构代码之后，可以看到不同上传对象的方法被隔离在各自的函数里面互不干扰，try/catch 和 if 分支不再纠缠到一起，使得我们可以很方便地维护和扩展代码。后来，我们新增 Webkit 控件上传和 HTML5 上传，我们要做的仅仅是分别增加这两个函数，然后把这两个函数加到 iteratorUploadObj 函数的参数里面进去。

## 小结

迭代器是一种相对简单的模式，简单到我们都不认为它是一种设计模式，目前绝大多部分的语言都内置了迭代器。