---
title: JavaScript设计模式——策略模式
date: 2019-02-16 18:30:00
tags: JavaScript设计模式
categories: 
	- JavaScript设计模式




---

学习曾探的 《JavaScript设计模式与开发实践》并做记录。

[书籍的购买链接](http://www.ituring.com.cn/book/1632/)

设计模式的定义是：在面向对象软件设计过程中针对特定问题的简洁而优雅的解决方案。

通俗一点，设计模式是在某种场合下对某个问题的一种解决方案。是给面向对象软件开发中的一些好的设计取个名字。

# JavaScript设计模式——策略模式

俗话说，条条大路通罗马。在程序设计中，要实现一个功能有很多种方案可以选择。比如一个压缩文件的程序，既可以选择 zip 算法，还可以选择 gzip 算法。

这些算法灵活多样，而且可以随意互相转换。这种替换方案就是策略模式。

**策略模式的定义是：定义一系列的算法，把它们一个个封装起来，并且使它们可以互相替换**

## 使用策略模式计算奖金

策略模式有很广泛的应用，本节以年终奖的计算为例进行介绍。很多公司的年终奖是根据员工的工资基数和年底绩效情况来发放的。例如：绩效为 S 的人年终奖有 4倍工资，绩效为 A的人年终奖有 3倍工资，而绩效为 B 的人年终奖是 2 倍工资。假设财务部要求我们提供一段代码，来方便计算员工的年终奖。

### 1.初步想法和实现

```javascript
var calculateBonus = function(performanceLevel,salary){
  if(performanceLevel === 'S'){
    return salary * 4;
  }
  if(performanceLevel === 'A'){
    return salary * 3;
  }
  if(performanceLevel === 'B'){
    return salary * 2;
  }
}
calculateBonus('S',2000);
calculateBonus('B',2000);
calculateBonus('A',2000);
// 可以轻易地看出来，calculateBonus 函数比较庞大，包含了很多 if-else 语句，这些语句要覆盖所有的逻辑分支。缺乏弹性，如果增加了一种绩效等级，或者要把某个绩效等级的工资更改就必须深入calculateBonus 函数的内部实现。
// 算法的复用性差，如果在程序中要复用这些算法，只能复制粘贴。
```

### 2.使用组合函数重构代码

一般最容易想到的方法就是使用组合函数来重构代码，我们把各种算法封装到一个函数里面，函数有各自的命名，可以一目了然知道它对应哪种算法。它们也可以被复用在程序中的其他地方。

```javascript
var calculateBonus = function (performanceLevel, salary) {
  if (performanceLevel === 'S') {
    return performanceS(salary);
  }
  if (performanceLevel === 'A') {
    return performanceA(salary);
  }
  if (performanceLevel === 'B') {
    return performanceB(salary);
  }
}

var performanceS = function (salary) {
  return salary * 4;
}
var performanceA = function (salary) {
  return salary * 3;
}
var performanceB = function (salary) {
  return salary * 2;
}
calculateBonus('A',2000);
```

可以看出来，calculateBonus 函数有可能越来越庞大，而且在系统变化的时候缺乏弹性。

### 3.使用策略模式重构代码

策略模式指定是定义一系列的算法，把它们一个个封装起来。将不变的部分和变化的部分隔开是每个设计模式的主题，策略模式也不会例外，策略模式的目的就是将算法的使用与算法的实现分离开来。

上面的例子中，算法的使用方式是不变的，都是根据某个算法取得计算后的奖金数额。算法 的实现是各异和变化的，每种绩效对应着不同的计算规则。

一个基于策略模式的程序至少由两部分组成。第一部分是一组策略类，策略类封装了具体的算法，并负责具体的计算过程。第二部分是环境类 Context，Context 接受客户的请求，随后把请求委托给某一个策略类。说明 Context 中要维持对某个策略对象的引用。

现在用策略模式来重构上面的代码，第一个版本是模仿传统面向对象语言中实现。把每种绩效的计算规则都封装在对应的策略类里面：

```javascript
var performanceS = function(){}
performanceS.prototype.calculate = function(salary){
  return salary * 4;
}

var performanceA = function(){}
performanceA.prototype.calculate = function(salary){
  return salary * 3;
}

var performanceB = function(){}
performanceB.prototype.calculate = function(salary){
  return salary * 2;
}
```

定义奖金类 Bonus:

```javascript
var Bonus = function () {
  this.salary = null;
  this.strategy = null;
}
Bonus.prototype.setSalary = function(salary){
    this.salary = salary;
}
Bonus.prototype.setStrategy = function(salary){
    this.strategy = salary;
}
Bonus.prototype.getBouns = function(salary){
    return this.strategy.calculate(this.salary);
}
```

调用：

```javascript
// bonus 本身没有能力进行计算，而是把请求委托给力之前保存好的策略对象
var bonus = new Bonus();
bonus.setSalary(1000);
bonus.setStrategy(new PerformanceS()); // 设置策略对象

console.log(bonus.getBonus()); // 4000

bonus.setStrategy(new PerformanceA()); // 设置策略对象
console.log(bonus.getBonus()); // 3000
```

用策略模式重构了这段计算年终奖的代码后，可以看到通过策略模式重构之后，代码变得更加清晰了，各个类的职责更加鲜明了。但这段代码是基于传统面向对象语言的模仿。

## JavaScript 版本的策略模式

上面代码中，我们让 strategy 对象从各个策略类中创建而来，这是模拟一些传统面向对象语言的实现。实际上在 JavaScript ，函数也是对象，所以更简单和直接的做法是把 stategy 直接定义为函数：

```javascript
var staregies = {
  'S':function(salary){
    return salary * 4;
  },
  'A':function(salary){
    return salary * 3;
  },
  'B':function(salary){
    return salary * 2;
  }
}
```

而Context 也没有必要通过 Bonus 来表示，我们依然可以使用 calculateBonus 函数充当 Context 来接受用户的请求。

```javascript
var calculateBonus = function (level, salary) {
  return strategies[level](salary);
}
console.log(calculateBonus('S',2000));
console.log(calculateBonus('A',3000));
```

## 多态在策略模式中的体现

通过使用策略模式重构代码，消除了原来程序中大片的条件分支语句。所有跟计算奖金有关的逻辑都不放在 Context 中，而是分布在各个策略对象中。Context 并没有计算奖金的能力，而是把这个职责委托给了某个策略对象。每个策略对象负责的算法各自封装在对象内部。当我们对这些策略对象发出计算奖金的请求的时候，它们会返回各自不同的计算结果，这正是对象多态性的体现。替换 Context 中当前保存的策略对象，便能执行不同的算法来得到我们想要的结果。

## 使用策略模式实现缓动动画

HTML5 版本的街头霸王游戏，让游戏的主角跳跃或者移动，实际上只是让这个 div 按照一定的缓动算法进行运动而已。

如果我们明白了怎么样让一个小球运动起来，那么离编写一个完整的游戏就不遥远了，剩下的只是一些把逻辑组织起来的体力活。

### 实现动画效果的原理

用 JavaScript 实现动画效果的原理跟动画制作的原理是一样的，动画片是把一些差距不大的原画以较快的帧数播放，来达到视觉上的动画效果。在 JavaScript 中可以通过连续改变元素的某个 CSS 属性，如 top,bottom,background-position 来实现动画效果。

### 思路和一些准备工作

编写一个动画类和缓动算法，rag小球以各种各样的缓动效果在页面中运动。

在运动开始之前，需要提前记录一些有用的信息，至少包括以下信息：

- 动画开始时，小球所在的原始位置
- 小球移动的目标位置
- 动画开始的准确点时间
- 小球持续运动的时间

随后，我们会用 setInterval 创建一个定时器，定时器每隔19ms 循环一次。在定时器的每一帧里面，我们会把动画消耗的时间、小球的初始位置、小球目标位置和动画持续的总时间等信息传入缓动算法。该算法会通过几个参数，计算出小球当前应该在的位置。最后再更新该 div 对应的 CSS 属性，小球就可以顺利地运动起来了。

### 让小球运动起来

常见的缓动算法，这些算法最初来自 Flash ，但可以非常方便地移植到其他语言中。

这些算法都接受 4 个参数，这 4个参数的含义分别是动画已消耗的时间、小球的初始位置、小球目标位置、动画持续时间，返回的值则是动画元素应该处在的当前位置。代码如下：

```javascript
var tween = {
  linear: function (t, b, c, d) {
    return c * t / d + b;
  },
  easeIn: function (t, b, c, d) {
    return c * (t /= d) * t + b;
  },
  strongEaseIn: function (t, b, c, d) {
    return c * (t /= d) * t * t * t * t + b;
  },
  strongEaseOut: function (t, b, c, d) {
    return c * ((t = t / d - 1) * t * t * t * t + 1) + b;
  },
  sineaseIn: function (t, b, c, d) {
    return c * (t /= d) * t * t + b;
  },
  sineaseOut: function (t, b, c, d) {
    return c * ((t = t / d - 1) * t * t + 1) + b;
  }
}
```

接着在页面中放置一个div：

```html
<body>
    <div style="position:absolute;background:blue" id="div">
        我是 div
    </div>
</body>
```

定义 Animate 类，Animate 的构造函数接受一个参数，即将运动起来的 dom 节点。Animate 类的代码如下：

```javascript
var Animate = function (dom) {
  this.dom = dom; // 进行运动的 dom 节点
  this.startTime = 0; // 动画开始时间
  this.startPos = 0; // 动画开始时，dom节点的位置，即 dom 的初始位置
  this.endPos = 0; // 动画结束时，dom节点的位置，即 dom 的目标位置
  this.properyName = null; // dom 节点需要被改变的 css 属性名
  this.easing = null; // 缓动算法
  this.duration = null; // 动画持续时间
}
```

Animate.prototype.start 方法负责启动这个动画，在动画被启动的瞬间，要记录一些信息，供缓动算法在以后计算小球当前位置的时候使用。在记录完这些消息后，此方法还要负责启动定时器。

```javascript
Animate.prototype.start = function (propertyName, endPos, duration, easing) {
  this.startTime = +new Date; // 动画启动时间
  this.startPos = this.dom.getBoundingClientRect()[propertyName]; // dom 节点初始位置
  this.properyName = propertyName;
  this.endPos = endPos; // dom 节点目标位置
  this.duration = duration; // 动画持续时间
  this.easing = tween[easing]; // 缓动算法
  var self = this;
  var timeId = setInterval(function () { // 启动定时器，开始执行动画
    if (self.step() === false) { // 如果动画已结束，则清除定时器
        clearInterval(timeId);
    }
  });
}
```

start 方法接收四个参数：

- propertyName：要改变的 CSS 属性名
- endPos：小球运动的目标位置
- duration：动画持续时间
- easing：缓动算法

Animate.prototype.step 方法，代表小球运动的每一帧要做的事情，在此处，这个方法负责计算小球的当前为孩子和调用更新 CSS 属性值的方法 Animate.prototype.update.

```javascript
Animate.prototype.step = function () {
  var t = +new Date; // 取得开始时间
  if (t >= this.startTime + this.duration) { // 如果当前时间大于动画开始时间加上动画持续时间，说明动画已经结束了，此时要修正小球的位置。并返回 false 清除时间定时器。
    this.update(this.endPos); // 更新小球的 CSS 属性值
    return false;
  }
  // pos 为小球的位置
  var pos = this.easing(t - this.startTime, this.startPos, this.endPos - this.startPos, this.duration);
  this.update(pos); // 更新小球的 CSS 属性值
}	
```

最后是负责更新小球的 CSS 属性值的 Animate.prototype.update 方法：

```javascript
Animate.prototype.update = function(pos){
  this.dom.style[this.properyName] = pos + 'px';
}	
```

测试：

```javascript
var div = document.getElementById('div');
var animate = new Animate(div);
animate.start('left',500,700,'strongEaseOut');
```

可以看到，你的div 元素在页面上欢快地滑动。使用策略类把算法传入动画类里，来达到各种不同缓动效果，这些算法都是可以轻易地被替换成另一个算法。这是策略模式的经典运动之一。策略模式的实现并不复杂，关键是如何从策略模式的实现背后，找到封装、委托和多态性这些思想的价值。

## 更广义的算法

策略模式指定是一系列 的算法，并且把它们封装起来。

从定义上看，策略模式是用来封装算法的，但如果把策略模式仅仅用来封装算法，未免有一点大材小用。在实际开发中，通常会把算法的含义扩散开来，使策略模式也可以用来封装一系列的“业务规则”。只要这些业务规则指向的目标一致，并且可以被替换，我们就可以用策略模式来封装它们。

## 表单验证

在一个 Web  项目中，注册、登录、修改用户信息等功能的实现都离不开提交表单。

假设我们正在编写的一个注册的页面，在点击注册按钮之前，有如下几条校验逻辑。

- 用户名不能为空
- 密码长度不能少于6位
- 手机号码必须符合格式

### 表单校验的第一个版本

```html
<form ation="http://xxx.com/register" id="registerForm" method="post">
    请输入用户名：<input type="text" name="Username">
    请输入密码：<input type="text" name="password">
    请输入手机号码：<input type="text" name="phoneNumber">
    <button type="submit">提交</button>
</form>
<script>
	var registerForm = document.getElementById('registerForm');
	registerForm.onSubmit = function(){
		if(registerForm.userName.value === ''){
			alert('用户名不为空');
			return false;
		}		
		if(registerForm.password.length < 6){
			alert('密码长度不能少于6位');
			return false;
		}		
		if(!/(^1[3|5|8][0-9]{9}$)/.test(registerForm.phoneNumber.value)){		
			alert('手机号码格式不正确');
			return false;
		}
	}    
</script>
```

缺点显而易见：

- registerForm.onSubmit 函数庞大，包含了很多 if-else 语句
- 函数缺乏弹性，可复用性差

### 用策略模式重构表单验证

下面用策略模式来重构表单验证的代码，第一步把校验逻辑都封装成策略对象：

```javascript
var strategies = {
    isNonEmpty:function(value,errorMsg){
        if(value === ''){
            return errorMsg;
        }
    },
    minLength:function(value,length,errorMsg){
        if(value.length < length){
            return errorMsg;
        }
    },
    isMobile:function(value,errorMsg){
        if(!/(^1[3|5|8][0-9]{9}$)/.test(value)){
            return errorMsg;
        }
    }
}
```

接下来，我们猪呢比实现 Validator类。Validator 这里作为 Context ，负责接收用户的请求并委托给 strategy 对象。在给出 Validator 类的代码之前，有必要提前了解用户是如何向 Validator 类发送请求的，这有助于我们知道该如何去编写 Validator 类的代码：

```javascript
var validataFunc = function(){
    var validator = new Validator(); // 创建一个 validator 对象
    // 添加一些校验规则
    validator.add(registerForm.userName,'isNonEmpty','用户名不为空');
    validator.add(registerForm.password,'minLength:6','密码长度不能少于6位');
    validator.add(registerForm.phoneNumber,'isMobile','手机号码格式不正确');

    var errorMsg = validator.start(); // 获取校验结果
    return errorMsg;
}

var registerForm = document.getElementById('registerForm');
registerForm.onSubmit = function(){
    var errorMsg = validataFunc(); // 如果 errorMsg 有确切的返回值，说明未通过校验
    if(errorMsg){
        alert(errorMsg);
        return false; // 阻止表单提交
    }

}
```

从上面代码可以看出，通过创建一个 validator 对象，然后通过 validator.add 方法，往 validator 对象中添加了一些校验规则。validator.add 方法接受三个参数。

- registerForm.xxx 为参与校验的name 是 xxx 的input 输入框
- ‘minLength:6’ 是一个以冒号隔开的字符串。前面是 strategy 对象，后面代表校验过程中的参数。如果没有冒号则说明校验过程中不需要额外的参数信息
- 第三个参数是当校验不通过的时候返回的错误信息

当我们往 validator 对象添加了一系列的校验规则后，会调用 validator.start(); 方法来启动校验。如果 validator.start 返回一个确切的 errorMsg 字符串当做返回值，说明该次校验没有通过，此时需要返回 false 来阻止表单的提交。

最后是 Validator 类的实现：

```javascript
var Validator = function(){
    this.cache = []; // 保存校验规则
}
Validator.prototype.add = function(dom,rule,errorMsg){
    var ary = rule.split(':'); // 把 strategy 和 参数分开
    this.cache.push(function(){ //把校验的步骤用空函数包装起来，并且放入 cache 中
        var strategy = ary.shift(); // 用户挑选的 stragety
        ary.unshift(dom.value); // 把 input 的 value 添加到参数列表的头部
        ary.push(errorMsg); // 参数列表尾部推进去 errorMsg
        return strategies[strategy].apply(dom,ary);
    });
}
Validator.prototype.start = function(){
    for(var i=0,validataFunc;validataFunc=this.cache[i++];){
        var msg = validataFunc(); // 开始校验并取得校验后的返回信息
        if(msg){ // 如果有确切的返回信息，则说明校验没有通过
            return msg;
        }
    }
}
```

使用策略模式重构代码之后，我们仅仅通过 “配置” 的方式完成了一个表单的验证，这些校验规则可以复用在程序的任何地方，还能作为插件来使用，方便被移植到其他项目中。

在修改某个校验规则的时候，主要编写或者改写少量的代码。

### 给某个文本输入框添加多种校验规则

如果我们想校验一个文本是否为空，并且校验它输入的文本的长度不小于10呢？我们期望以这样的形式进行校验：

```javascript
Validator.add(registerForm,userName,[{
    stragety:'isNonEmpty',
    errorMsg:'用户名不能为空'
},{
    stragety:'minLength:6',
    errorMsg:'用户名长度不能小于10位'    
}]);
```

下面提供的代码可以用于一个文本框对应多种校验规则：

```javascript
// 改写 add 函数
Validator.prototype.add = function(dom,rules){
    var self = this;
    for(var i=0,rule;rule=rules[i++];){
        (function(rule){
            var strategyAry = rule.stragety.split(':');
            var errorMsg = rule.errorMsg;

            self.cache.push(function(){
                var strategy = strategyAry.shift();
                strategyAry.unshift(dom.value);
                strategyAry.push(errorMsg);
                return strategies[stragety].apply(dom,strategyAry);
            });
        })(rule)
    }
}
```

测试：

```javascript
var registerForm = document.getElementById('registerForm');
var validataFunc = function(){
    var validator = new Validator();
    validator.add(registerForm.userName,[{
        strategy: 'isNonEmpty', 
        errorMsg:'用户名不能为空'
    },{
        strategy: 'minLength:10', 
 		errorMsg: '用户名长度不能小于 10 位' 
    }]);
    validator.add(registerForm.password,[{
        strategy: 'isNonEmpty', 
        errorMsg:'密码不能为空'
    },{
        strategy: 'minLength:6', 
 		errorMsg: '密码长度不能小于 6 位' 
    }]);
    validator.add(registerForm.phoneNumber,[{
        strategy: 'isNonEmpty', 
        errorMsg:'手机号码不能为空'
    },{
        strategy: 'isMobile', 
 		errorMsg: '手机号码格式不正确' 
    }]); 
    
    var errorMsg = validator.start();
    return errorMsg;
}
registerForm.onsubmit = function(){
    var errorMsg = validataFunc();
    if(errorMsg){
        alert(errorMsg);
        return false; // 阻止表单提交
    }
}
```



## 策略模式的优缺点

策略模式是一种常用且有效的设计模式。从上面的三个例子中我们可以总结出来一些策略模式的优点：

- 策略模式利用组合、委托和多态等技术和思想，可以有效避免多重条件选择语句。
- 策略模式提供了对开放-封闭原则的完美支持，将算法包装在独立的 strategy 中，使得它们易于切换，易于理解，易于扩展。
- 策略模式中的算法也可以复用在系统的其他地方，从而避免许多重复的复制粘贴工作。
- 在策略模式中利用组合和委托来让 Context 拥有执行算法的能力，这也是继承的一种更轻便的替代方案。

首先，使用策略模式会在程序中增加许多策略类或策略对象，但实际上这比把它们负责的逻辑堆砌在 Context 中要好。

其次，要使用 策略模式，必须了解所有的 strategy ,必须了解各个 strategy 之间的不同点，这样才能选择一个合适的 strategy。比如我们要选择旅游出行的路线，必须先了解选择不同交通工具的方案的细节。此时 strategy 要向客户暴露它的所有实现，这是违反最少知识原则的。

## 一等函数对象与策略模式

在以类为中心的传统面向对象语言中，不同的算法或者行为被封装在各个策略类中，Context 将请求委托给这些策略对象，这些策略对象会根据请求返回不同执行的结果，这样便能体现出对象的多态性。

“在函数作为一等对象的语言中，策略模式是隐形的。strategy 就是值为函数的变量”。在 JavaScript 中，除了使用类来封装算法和行为之外，使用函数当然也是一种选择。这些算法可以被封装在函数中并且四处传递，也就是我们常说的 “高阶函数”。实际上在 JavaScript 这种把函数作为一等对象的语言里，策略模式已经融入到了语言本身当中，当我们调用高阶函数来封装不同的行为，并且把它传递到另一个函数里。当我们对这些函数发出 “调用”的消息时候，不同的函数会返回不同的执行结果。在 JavaScript 中，“函数对象的多态性” 来地更加简单。



## 小结

在 JavaScript 语言的策略模式中，策略类往往被函数所替代，这时策略模式就成为了一种 “隐形” 的模式，尽管这样，从头到尾地了解策略模式，不仅可以让我们对该模式有更加的透彻了解，也可以使得我们明白使用函数的好处。