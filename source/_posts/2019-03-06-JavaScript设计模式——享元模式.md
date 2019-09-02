---
title: JavaScript设计模式——享元模式
date: 2019-03-06 11:30:00
tags: JavaScript设计模式
categories: 
	- JavaScript设计模式



---

学习曾探的 《JavaScript设计模式与开发实践》并做记录。

[书籍的购买链接](http://www.ituring.com.cn/book/1632/)

设计模式的定义是：在面向对象软件设计过程中针对特定问题的简洁而优雅的解决方案。

通俗一点，设计模式是在某种场合下对某个问题的一种解决方案。是给面向对象软件开发中的一些好的设计取个名字。

# JavaScript设计模式——享元模式

享元（flyweight）模式是一种用于性能优化的模式，‘fly’ 在这里是苍蝇的意思，意为蝇量级。享元模式的核心是运用共享技术来有效支持大量细粒度的对象。

如果系统中因为创建了大量类似的对象而导致内存占用过高，享元模式就非常有用了。在 JavaScript 中，浏览器特别是移动端的浏览器分配的内存并不算多，如何节省内存就成了 一件非常有意义的事情。

## 初识享元模式

假设有个内衣工厂，目前的产品有 50 种男式内衣和 50 种女士内衣，为了推销产品，工厂决定生产一些塑料模特来穿上他们的内衣拍成广告照片。 正常情况下需要 50 个男模特和 50 个女模特，然后让他们每人分别穿上一件内衣来拍照。不使用享元模式的情况下，在程序里也许会这样写

```javascript
var Model = function(sex,underwear){
    this.sex = sex;
    this.underwear = underwear;
}
Model.prototype.takePhoto = function(){
    console.log('sex='+this.sex + 'underwear='+this.underwear);
}
for(var i=1;i<=50;i++){
    var maleModel = new Model('male','underwear'+i);
    maleModel.takePhoto();
}
for(var i=1;i<=50;i++){
    var femaleModel = new Model('female','underwear'+i);
    femaleModel.takePhoto();
}
```

要得到一张照片，每次都需要传入 sex 和 underwear 参数，如上所述，现在一共有 50 种男内衣和 50 种女内衣，所以一共会产生 100 个对象。如果将来生产了 10000 种内衣，那这个程序可能会因为存在如此多的对象已经提前崩溃.

虽然有 100 种内衣，但很显然并不需要 50 个男模特和 50 个女模特。其实男模特和女模特各自有一个就足够了，他们可以分别穿上不同的内衣来拍照

```javascript
// 把 underwear 参数从构造函数中移除，构造函数只接收 sex 参数：
var Model = function(sex,underwear){
    this.sex = sex;
}
Model.prototype.takePhoto = function(){
    console.log('sex='+this.sex + 'underwear='+this.underwear);
}
// 分别创建一个男模特对象和一个女模特对象
var maleModel = new Model('male');
var femaleModel = new Model('female');
// 给男模特依次穿上所有的男装，并进行拍照：
for(var i=1;i<=50;i++){
    maleModel.underwater = 'underwater' + i;
    maleModel.takePhoto();
}
// 给女模特依次穿上所有的男装，并进行拍照：
for(var i=1;i<=50;i++){
    femaleModel.underwater = 'underwater' + i;
    femaleModel.takePhoto();
}
```



## 内部状态与外部状态

享元模式要求将对象的属性划分为内部状态和外部状态（状态在这里通常是指属性）。享元模式的目标是尽量减少共享对象的数量，如何划分它们，下面有几条经验作为指引：

- 内部状态存储于对象内部
- 内部状态可以被一些对象共享
- 内部状态独立于具体的场景，通常不会改变
- 外部状态取决于具体的场景，并且根据场景的变化而变化，外部状态不能被共享

我们可以把所有内部状态相同的对象都指定为同一个共享的对象。而外部状态可以从对象上剥离出来，并存储在外部。

剥离了外部状态的对象成为共享对象，外部状态在必要的时候被传入共享对象来组装成一个完整的对象。虽然组装外部状态成为了一个完整对象的过程需要一定的时间，但却可以大大减少系统中的对象数量。相比之下，这点时间或许是微不足道的。因此，享元模式是一种用时间换空间的优化模式。

在上面的例子中，性别是内部状态，内衣是外部状态，通过区分这两种状态，大大减少了系统中的对象数量。通常来说，内部状态有多少种组合，系统中便最多存在多少个对象，因为性别通常只有男女两种，所有该内衣厂商最多需要2个对象。

使用享元模式的关键是如何区分内部状态和外部状态。可以被对象共享的属性通常被划分为内部状态，如同不管什么样式的衣服，都可以按照性别的不同，穿在同一个男模特或者女模特身上。模特的性别就可以作为内部存储在共享对象内部。而外部状态取决于具体的场景，并根据场景二变化，就像例子中每件衣服都是不同的，它们不能被一些对象共享，因此只能被划分为外部状态。

## 享元模式的通用结构

上面例子中还不是一个完整的享元模式，还存在以下几个问题。

- 我们通过构造函数显式 new 出了男女两个 model 对象，在其他系统中，也许这并不是一开始就需要所有的共享对象。
- 给 model 对象手动设置了 underwater 外部状态，在更复杂的系统中，这并不是最好的方式，因为外部状态可能会相当复杂，它们与共享对象的联系会变得更加困难。

我们通过一个对象工厂来解决第一个问题，只有当某种共享对象被真正需要的时候，它从才工厂中被创建出来，对于第二个问题，可以用一个管理器来记录对象相关的外部状态，使得这些外部状态通过某个钩子和共享对象联系起来。

## 文件上传的例子

在微云上传模板的开发中，作者曾经借助享元模式提升了程序的性能。

### 对象爆炸

微云的文件上创功能虽然可以选择依照队列，一个一个排队上传，但也支持同时选择 2000 个文件。每一个文件都对应着一个 JavaScript 上传对象的创建，在第一版开发中，的确往程序里面同时 new 了 2000个 upload 对象，结果可想而知。Chrome 中还勉强能够支撑，IE 下直接进入假死状态。

微云支持好几种上传方式，比如浏览器插件、Flash 和表单上传等，为了简化例子，作者假设只有插件和 Flash 这两种。不论是插件上传，还是 Flash 上传，原理都是一样的，当用户选择了文件之后，插件和 Flash 都会通知调用 Window 下的一个全局 JavaScript 函数，它的名字是startUpload，用户选择的文件列表被组合成一个数组 files 塞进该函数的参数列表里，代码如下：

```javascript
var id = 0;
window.startUpload = function(uploadType,files){ // uploadType 区分是控件还是 flash
    for(var i=0,file;file=files[i++];){
        var uploadObj = new Upload(uploadType,file.fileName,file.fileSize);
        uploadObj.init(id++); // 给 upload 对象设置一个唯一的 id
    }
}

var Upload = function(uploadType,fileName,fileSize){
    this.uploadType = uploadType;
    this.fileName = fileName;
    this.fileSize = fileSize;
    this.dom = null;
}
Upload.prototype.init = function(id){
    var that = this;
    this.id = id;
    this.dom = document.createElement('div');
    this.dom.innerHTML = '<span>文件名称:'+ this.fileName +', 文件大小: '+this.fileSize +'</span>' + '<button class="delFile">删除</button>'; 
    this.dom.querySelector('.delFile').onclick = function(){
        that.delFile();
    };
    document.body.appendChild(this.dom);
}

Upload.prototype.delFile = function(){
    if(this.fileSize < 3000){
        return this.dom.parentNode.removeChild(this.dom);
    }
    if(window.confirm('确定删除该文件吗？'+this.fileName)){
        return this.dom.parentNode.removeChild(this.dom);
    }
}
```

接下来分别创建 3 个插件上传对象和三个 Flash 上传对象：

```javascript
startUpload('plugin',[
    {fileName: '1.txt', fileSize: 1000 },
    {fileName: '2.html', fileSize: 3000 },
    {fileName: '3.txt', fileSize: 5000 },
    ]);		
startUpload('flash',[
    {fileName: '4.txt', fileSize: 1000 },
    {fileName: '5.html', fileSize: 3000 },
    {fileName: '6.txt', fileSize: 5000 },
    ]);
```

### 享元模式重构文件上传

上一节的代码是第一版文件上传，在这段代码中有多少个需要上传的文件，就一共创建了多少个 upload 对象，下面用享元模式重构它。

首先，我们需要确认插件类型 uploadType 是内部状态，那为什么单单 uploadType 是内部状态，前面说过，划分内部状态和外部状态的关键是主要下面几点：

- 内部状态储存于对象内部。
- 内部状态可以被一些对象共享。
- 内部状态独立于具体的场景，通常不会改变。
- 外部状态取决于具体的场景并根据场景而变化外部状态不能被共享。

在文件上传的例子中，upload 对象必须依赖 uploadType 属性才能工作，这是因为插件上传、Flash 上传、表单上传的实际工作原理有很大的区别，它们各自调用的接口也是不一样的，必须在对象创建之初就明确它是什么类型的插件，才可以在程序的运行过程中，让它们分别调用各自的 start、pause、cancel、del 等方法。

实际上在微云的真实代码中，虽然插件和 Flash 上传对象最终创建自一个大的工厂类，但它们实际上根据 uploadType 值的不同，分别是来自于两个不同类的对象。

一旦明确了 uploadType，无论我们使用什么方式上传，这个上传对象都是可以被任何文件共用的。而 fileName 和 fileSize 是根据场景而变化的，每个文件的 fileName 和 fileSize 都不一样，fileName 和 fileSize 没有办法被共享，它们只能被划分为外部状态。

### 剥离外部状态

明确了 uploadType 作为内部状态之后，我们再把其他外部状态从构造函数中抽离出来，Upload 构造函数中只保留 uploadType 参数：

```javascript
var Upload = function(uploadType){
    this.uploadType = uploadType;
}
// init 函数也不再需要了，因为 upload 对象初始化的工作被放在了 uploadManager.add 函数中，下面定义的是 删除函数
Upload.prototype.delFile = function(id){
    // 开始删除文件之前，需要读取文件的实际大小，而文件的实际大小被存储在外部管理器 uploadManager 中，这里需要 setExternalState 方法给共享对象设置正确的 fileSize。这里表示把当前 id 对应的对象的外部状态都组装到共享对象中
    uploadManager.setExternalState(id,this);
    if(this.fileSize < 3000){
        return this.dom.parentNode.removeChild(this.dom);
    }
    if(window.confirm('确定删除该文件吗？'+this.fileName)){
        return this.dom.parentNode.removeChild(this.dom);
    }				
}
```

### 工厂进行对象实例化

接下里定义一个工厂来创建 upload 对象，如果某种内部状态对应的共享对象已经被创建过了，那么直接返回这个对象，否则创建一个新的对象：

```javascript
var UploadFactory = (function(){
    var createdFlyWeightObjs = {};
    return{
        create:function(uploadType){
            if(createdFlyWeightObjs[uploadType]){
                return createdFlyWeightObjs[uploadType]
            }
            return createdFlyWeightObjs[uploadType] = new Upload(uploadType);
        }
    }
})()
```

### 管理器封装外部状态

完善前面提到的 uploadManager 对象，它负责向 UploadFactory 提交创建对象的请求，并用一个 uploadDatabase 对象保存所有 upload 对象的外部状态，以便在程序运行的过程中给 upload 共享对象设置外部状态：

```javascript
var uploadManager = (function(){
    var uploadDatabase = {};
    return{
        add:function(id,uploadType,fileName,fileSize){
            var flyWeightObj = UploadFactory.create(uploadType);
            var dom = document.createElement('div');
            dom.innerHTML = '<span>文件名称:'+ fileName +', 文件大小: '+fileSize +'</span>' + '<button class="delFile">删除</button>'; 
            dom.querySelector('.delFile').onclick = function(){
                flyWeightObj.delFile(id);
            };
            document.body.appendChild(dom);
            uploadDatabase[id] = {
                fileName:fileName,
                fileSize:fileSize,
                dom:dom
            };
            return flyWeightObj;
        },
        setExternalState:function(id,flyWeightObj){
            var uploadData = uploadDatabase[id];
            for(var i in uploadData){
                flyWeightObj[i] = uploadData[i]
            }
        }
    }
})();
```

然后是开始触发上传动作的 startUpload函数：

```javascript
var id = 0;
window.startUpload = function(uploadType,files){
    for(var i=0,file;file=files[i++];){
        var uploadObj = uploadManager.add(++id,uploadType,file.fileName,file.fileSize);
    }
}
```

最后是测试时间，运行下面的代码后，可以发现运行结果跟享元模式是一样的：

```javascript
startUpload('plugin',[
    {fileName: '1.txt', fileSize: 1000 },
    {fileName: '2.html', fileSize: 3000 },
    {fileName: '3.txt', fileSize: 5000 },
    ]);		
startUpload('flash',[
    {fileName: '4.txt', fileSize: 1000 },
    {fileName: '5.html', fileSize: 3000 },
    {fileName: '6.txt', fileSize: 5000 },
    ]);
```

享元模式重构之前的代码一共创建了 6 个 upload 对象，而通过享元模式重构之后，对象的数量减少为 2 个，就算现在同时上传 2000 个文件，需要创建的 upload 对象数量依旧是 2。

## 享元模式 的适用性

享元模式是一种很好的性能优化方案，但它也带来了一些复杂性的问题，从前面两组代码的比较可以看出，使用了享元模式之后，我们需要分别维护多一个 factory 对象和 一个 manager 对象，在大部分不必要使用享元模式的环境下，这些开销是可以避免的。

享元模式带来的好处很大程度上取决于如何使用以及何时使用，一般来说，一下情况发生的时候可以使用享元模式。

- 一个程序中使用了大量的相似对象
- 由于使用了大量对象，造成很大的内存开销
- 对象的大多数状态都可以变为外部状态
- 剥离出对象的外部状态之后，可以用相对比较少的共享对象取代大量对象

## 再谈内部状态和外部状态

享元模式的关键是把内部状态和外部状态分离开来，有多少中内部状态的组合，系统便有最多多少个共享对象，而外部状态存储在共享对象的外部，在必要时被传入共享对象来组装成一个完整的对象。现在考虑两种极端的情况，即对象没有外部状态和没有内部状态的时候。

### 没有内部状态的享元

在文件上传的例子中，我们分别进行过插件调用和 Flash 调用，就 `startUpload('plugin',[])` 和 `startUpload(flash,[])`，导致程序中创建了内部状态不同的两个共享对象。很多网盘都提供了极速上传（控件）和普通上传（Flash）两种模式，如果极速上传不好使的话，用户可以随时切换到普通模式，所以这里确实是需要同时存在两个不同的 upload 共享对象，而不是在一开始就自我判断浏览器可以支持什么插件，就用什么插件上传。

但不是每个网站都要做得这么复杂，很多小的网站只支持单一的上传方式，如果不考虑极速上传和普通上传之间的切换，这意味着之前的代码中作为内部状态的 uploadType 属性是可以删除的。

在继续使用享元模式的前提下，构造函数 upload 就变成了无参数的形式：

```javascript
var upload = function(){}
```

其他属性如 fileName，fileSize，dom 依然可以作为外部状态保存在共享对象的外部。在 uploadType 作为内部状态的时候，它可能为空间，也可能为 Flash，所以当时最多可以组合成两个共享对象。而现在没有了内部状态，这意味着只需要唯一的一个共享对象。我们对代码进行改写：

```javascript
var UploadFactory = (function(){
    var uploadObj;
    return{
        create:function(){
            if(uploadObj){
                return uploadObj
            }
            return uploadObj = new Upload();
        }
    }
})()
```

管理器部分的代码不需要改动，还是负责剥离和组装外部状态，可以看到，当对象没有内部状态的时候，生产共享对象的工厂实际上变成了一个单例工厂，虽然这时候的共享对象没有内部状态的区分，但还是有剥离外部状态的过程，我们依然倾向于称之为享元模式。

### 没有外部状态的享元

网上许多资料中，经常把 Java 或者 C#的字符串看成享元，这种说法是否正确呢？我们看看下面这段 Java 代码，来分析一下：

```java
public class Test { 
 public static void main( String args[] ){ 
 String a1 = new String( "a" ).intern(); 
 String a2 = new String( "a" ).intern(); 
 System.out.println( a1 == a2 ); // true 
 } 
} 
```

在这段 Java 代码里，分别 new 了两个字符串对象 a1 和 a2。intern 是一种对象池技术， new String("a").intern()的含义如下

- 如果值为 a 的字符串对象已经存在于对象池中，则返回这个对象的引用
- 反之，将字符串 a 的对象添加进对象池，并返回这个对象的引用

所以 a1 == a2 的结果是 true，但这并不是使用了享元模式的结果，享元模式的关键是区别内部状态和外部状态。享元模式的过程是剥离外部状态，并把外部状态保存在其他地方，在合适的时刻再把外部状态组装进共享对象。这里并没有剥离外部状态的过程，a1 和 a2 指向的完全就是同一个对象，所以如果没有外部状态的分离，即使这里使用了共享的技术，但并不是一个纯粹的享元模式。

## 对象池

对象池维护一个装载空闲对象的池子，如果需要对象的时候，不是直接 new，而是转从对象池里获取。如果对象池里没有空闲对象，则创建一个新的对象，当获取出的对象完成它的职责之后， 再进入池子等待被下次获取。

对象池的原理很好理解，比如我们组人手一本《JavaScript 权威指南》，从节约的角度来讲，这并不是很划算，因为大部分时间这些书都被闲置在各自的书架上，所以我们一开始就只买一本，或者一起建立一个小型图书馆（对象池），需要看书的时候就从图书馆里借，看完了之后再把书还回图书馆。如果同时有三个人要看这本书，而现在图书馆里只有两本，那我们再马上去书店买一本放入图书馆。

对象池的技术的应用非常广泛，HTTP 连接池和数据库连接池都是其代表应用，在 Web 前端开发中，对象池使用最多的场景大概是跟 DOM 有关的操作，很多空间和时间都消耗在了 DOM 节点上，如何避免频繁地创建和删除 DOM 节点就成了一个有意义的话题。

### 对象池的实现

假设我们在开发一个地图应用，地图上经常会出现一些标志地名的小气泡，我们叫它 toolTip。

在搜索我家附近地图的时候，页面里出现了 2 个小气泡。当我再搜索附近的兰州拉面馆时，页面中出现了 6 个小气泡。按照对象池的思想，在第二次搜索开始之前，并不会把第一次创建的2 个小气泡删除掉，而是把它们放进对象池。这样在第二次的搜索结果页面里，我们只需要再创建 4 个小气泡而不是 6 个。

先定义个获取小气泡的工厂，作为对象池的数组称为私有属性被包含在工厂闭包里，这个工厂有两个暴露对外的方法，create 表示获取一个 div 节点，recover 表示回收一个 div 节点：

```javascript
var toolTipFactory = (function(){
    var toolTipPool = []; // toolTip 对象池
    return{
        create:function(){
            if(toolTipPool.length === 0){
                var div = document.createElement('div');
                document.body.appendChild(div);
                return div;
            }else{
                return toolTipPool.shift(); // 如果对象池不为空，则从对象池取出一个 dom
            }
        },
        recover:function(tooltipDom){
            return toolTipPool.push(tooltipDom); // 对象池回收 dom
        }

    }
})();
```

拨回进行第一次搜索的时刻，目前需要创建 2 个小气泡节点，为了方便回收，用一个数组 ary 来记录它们：

```javascript
var ary = [];
for(var i=0,str;str=['A','B'][i++];){
    var toolTip = toolTipFactory.create();
    toolTip.innerHTML = 'src';
    ary.push(toolTip);
}
```

假设地图需要开始重新绘制，在此之前把两个节点回收进去对象池：

```javascript
for(var i=0,toolTip;toolTip = ary[i++]){
    toolTipFactory.recover(toolTip);
}
```

在创建6个小气泡

```javascript
var ary = [];
for(var i=0,str;str=[ 'A', 'B', 'C', 'D', 'E', 'F'][i++];){
    var toolTip = toolTipFactory.create();
    toolTip.innerHTML = 'src';
    ary.push(toolTip);
}
```

对象池跟享元模式的思想有点相似，虽然 innerHTML 的值 A、B、C、D 等也可以看成节点的外部状态，但在这里我们并没有主动分离内部状态和外部状态的过程.

### 通用对象池实现

我们还可以在对象池工厂里，把创建对象的具体过程封装起来，实现一个通用的对象池：

```javascript
var objectPoolFactory = function(createObjFn){
    var objectPool = [];
    return {
        create:function(){
            var obj = objectPool.length === 0 ? createObjFn.apply(this,arguments) : objectPool.shift();
        },
        recover:function(obj){
            objectPool.push(obj);
        }
    }
}
```

现在可以利用 objectPoolFactory 来创建一个装载一些 iframe 的对象池：

```javascript
var objectPoolFactory = function(createObjFn){
    var objectPool = [];
    return {
        create:function(){
            var obj = objectPool.length === 0 ? createObjFn.apply(this,arguments) : objectPool.shift();
            return obj;
        },
        recover:function(obj){
            objectPool.push(obj);
        }
    }
}

var iframeFactory = objectPoolFactory(function(){
    var iframe = document.createElement('iframe');
    document.body.appendChild(iframe);

    iframe.onload = function(){
        iframe.onload = null; // 防止 iframe 重复加载的 bug
        iframeFactory.recover(iframe); // iframe 加载完成之后回收节点
    }
    return iframe;
});

var iframe1 = iframeFactory.create(); 
iframe1.src = 'http://baidu.com'; 
var iframe2 = iframeFactory.create(); 
iframe2.src = 'http://QQ.com'; 
setTimeout(function(){ 
    var iframe3 = iframeFactory.create(); 
    iframe3.src = 'http://163.com'; 
}, 3000 );
```

对象池是另外一种性能优化方案，它跟享元模式有一些相似之处，但没有分离内部状态和外部状态这个过程。本章用享元模式完成了一个文件上传的程序，其实也可以用对象池+事件委托来代替实现

## 小结

享元模式是为解决性能问题而生的模式，这根大部分模式的诞生原因都不一样，在一个存在大量相似对象的系统中，享受模式可以很好地解决大量对象带来的性能问题。