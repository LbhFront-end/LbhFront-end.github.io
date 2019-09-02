---
title: JavaScript设计模式——状态模式
date: 2019-03-15 11:30:00
tags: JavaScript设计模式
categories: 
	- JavaScript设计模式




---

学习曾探的 《JavaScript设计模式与开发实践》并做记录。

[书籍的购买链接](http://www.ituring.com.cn/book/1632/)

设计模式的定义是：在面向对象软件设计过程中针对特定问题的简洁而优雅的解决方案。

通俗一点，设计模式是在某种场合下对某个问题的一种解决方案。是给面向对象软件开发中的一些好的设计取个名字。

# JavaScript设计模式——状态模式

态模式是一种非同寻常的优秀模式，它也许是解决某些需求场景的最好方法。虽然状态模式并不是一种简单到一目了然的模式（它往往还会带来代码量的增加），但你一旦明白了状态模式的精髓，以后一定会感谢它带给你的无与伦比的好处。

状态模式的关键是区分事物内部的状态，事物内部状态的改变往往会带来事物的行为改变。

## 初识

场景：有一个电灯，电灯上面只有一个开关。当电灯开着的时候，此时按下开关，电灯会切换到关闭状态；再按一次开关，电灯又将被打开。同一个开关按钮，在不同的状态下，表现出来的行为是不一样的。

首先定义一个 Light 类，可以预见，电灯对象 light 将从 Light类创建而出， light 对象将拥有两个属性，我们用 state 来记录电灯当前的状态，用 button 表示具体的开关按钮。下面来编写这个电灯程序的例子。

### 例子：电灯程序

```javascript
const Light = function(){
    this.state = 'off'; // 给电灯设置初始状态 off 
    this.button = null; // 电灯开关按钮
}
// 该方法负责在页面中创建一个真实的 button 节点，这个 button 就是电灯的开关按钮
Light.prototype.init = function(){
    const button = document.createElement('button'),
    self = this;
    button.innerHTML = '开关';
    this.button = document.body.appendChild(button);
    this.button.onclick = function(){
        self.buttonWasPressed();
    }
}
Light.prototype.buttonWasPressed = function(){
    if(this.state === 'off'){
        console.log('开灯');
        this.state = 'on';
    }else if(this.state === 'on'){
        console.log('关灯');
        this.state = 'off';
    }
}

const light = new Light();
light.init();
```

新型电灯：

```javascript
Light.prototype.buttonWasPressed = function(){ 
    if ( this.state === 'off' ){ 
        console.log( '弱光' ); 
        this.state = 'weakLight'; 
    }else if ( this.state === 'weakLight' ){ 
        console.log( '强光' ); 
        this.state = 'strongLight'; 
    }else if ( this.state === 'strongLight' ){ 
        console.log( '关灯' ); 
        this.state = 'off'; 
    } 
}; 
```

- 很明显 buttonWasPressed 方法是违反开放封闭原则的，每次新增或者修改 light 的状态，都需要改动 buttonWasPressed 方法中的代码，这使得 buttonWasPressed 成为了一个非常不稳定的方法。
- 所有跟状态有关的行为，都被封装在 buttonWasPressed 方法里，如果以后这个电灯又增加了强强光、超强光和终极强光，那我们将无法预计这个方法将膨胀到什么地步。
- 状态的切换非常不明显，仅仅表现为对 state 变量赋值，比如 this.state = 'weakLight'。在实际开发中，这样的操作很容易被程序员不小心漏掉。我们也没有办法一目了然地明白电灯一共有多少种状态，除非耐心地读完 buttonWasPressed 方法里的所有代码。
- 状态之间的切换关系，不过是往 buttonWasPressed 方法里堆砌 if、else 语句，增加或者修改一个状态可能需要改变若干个操作，这使 buttonWasPressed 更加难以阅读和维护。

### 状态模式改进

我们通常谈到封装，一般都会优先封装对象的行为，而不是对象的状态。但在状态模式中刚好相反，状态模式的关键是把事物的每种状态都封装成单独的类，跟此状态有关的行为都被封装在这个类的内部，所以 button 被按下的时候，只需要在上下文中，把这个请求委托给当前的状态对象即可，该状态对象会负责渲染它自身的行为。

同时我们还可以把状态的切换规则事先分布在状态类中， 这样就有效地消除了原本存在的大量条件分支语句。

定义 3 个状态类，分别是 offLightState、WeakLightState、strongLightState。这 3 个类都有一个原型方法 buttonWasPressed，代表在各自状态下，按钮被按下时将发生的行为，代码如下：

```javascript
const Light = function(){
    this.offLightState = new OffLightState(this);
    this.strongLightState = new StrongLightState(this);
    this.weakLightState = new WeakLightState(this);
    this.button = null;
}
const OffLightState = function(light){
    this.light = light;
}
OffLightState.prototype.buttonWasPressed = function(){
    console.log('弱光');
    this.light.setState(this.light.weakLightState);
}
const WeakLightState = function(light){
    this.light = light;
}
WeakLightState.prototype.buttonWasPressed = function(){
    console.log('强光');
    this.light.setState(this.light.strongLightState);
}
const StrongLightState = function(light){
    this.light = light;
}
StrongLightState.prototype.buttonWasPressed = function(){
    console.log('关灯');
    this.light.setState(this.light.offLightState);
}
Light.prototype.setState = function(newState){
    this.currentState = newState;
}
Light.prototype.init = function(){
    const button = document.createElement('button'),
    self = this;
    this.button = document.body.appendChild(button);
    this.button.innerHTML = '开关';
    this.currentState = this.offLightState;			
    this.button.onclick = function(){
        // 将请求委托给当前持有的状态对象去执行
        self.currentState.buttonWasPressed();
    }
}
const light = new Light();
light.init();
```

使用状态模式的好处很明显，它可以使得每一种状态和它对应的行为之间的关系局部化，这些行为被分散和封装在各自对应的状态类中，便于阅读和管理代码。

另外，状态之间的切换都被分布在状态类的内部，这使得我们无需编写过多的 if-else条件语句来控制状态之间的转换。

当我们需要为 light 对象增加一种新的状态时，只需要增加一个新的状态类，再稍稍改变一些现有的代码就可以了，假设现在 light 对象多了一种超强光的状态，那就先增加 SuperStrongLightState类：

```javascript
const Light = function(){
    this.offLightState = new OffLightState(this);
    this.strongLightState = new StrongLightState(this);
    this.weakLightState = new WeakLightState(this);
    this.superStrongLightState = new SuperStrongLightState(this);
    this.button = null;
}
const OffLightState = function(light){
    this.light = light;
}
OffLightState.prototype.buttonWasPressed = function(){
    console.log('弱光');
    this.light.setState(this.light.weakLightState);
}
const WeakLightState = function(light){
    this.light = light;
}
WeakLightState.prototype.buttonWasPressed = function(){
    console.log('强光');
    this.light.setState(this.light.strongLightState);
}
const StrongLightState = function(light){
    this.light = light;
}
StrongLightState.prototype.buttonWasPressed = function(){
    console.log('超强光');
    this.light.setState(this.light.superStrongLightState);
}

const SuperStrongLightState = function(light){
    this.light = light;
}
SuperStrongLightState.prototype.buttonWasPressed = function(){
    console.log('关灯');
    this.light.setState(this.light.offLightState);
}

Light.prototype.setState = function(newState){
    this.currentState = newState;
}
Light.prototype.init = function(){
    const button = document.createElement('button'),
    self = this;
    this.button = document.body.appendChild(button);
    this.button.innerHTML = '开关';
    this.currentState = this.offLightState;			
    this.button.onclick = function(){
        self.currentState.buttonWasPressed();
    }
}
const light = new Light();
light.init();
```

## 定义

**允许一个对象在其内部状态改变的时改变它的行为，对象看起来似乎修改了它的类**

前半句是将状态封装成独立的类，并将请求委托给当前的状态对象，当对象的内部状态改变的时候会带来不同的行为变化。电灯的例子足以说明这一点，在 off 和 on 这里两种不同的状态下，我们点击同一个按钮，得到的行为反馈是截然不同的。

后半句是从客户的角度看，我们使用的对象，在不同的状态下具有截然不同的行为，这个对象看起来是从不同类中实例化而来的，实际上这是使用了委托的效果。

## 通用结构

上面例子中的 Light 类在这里也被叫做上下文（Context）。随后在 Light 的构造函数中，我们要创建每一个状态类的实例对象，Context 将持有这些状态对象的引用，以便把请求委托给状态对象。用户的请求，即点击 button 的动作也是实现在 Context 中。代码如下：

```javascript
const Light = function(){
    this.offLightState = new OffLightState(this);
    this.strongLightState = new StrongLightState(this);
    this.weakLightState = new WeakLightState(this);
    this.superStrongLightState = new SuperStrongLightState(this);
    this.button = null;    
}

Light.prototype.init = function(){
    const button = document.createElement('button'),
    self = this;
    this.button = document.body.appendChild(button);
    this.button.innerHTML = '开关';
    this.currentState = this.offLightState;			
    this.button.onclick = function(){
        self.currentState.buttonWasPressed();
    }
}
```

接下来可能是个苦力活，我们要编写各种状态类，light 对象被传入状态类的构造函数，状态对象也需要持有 light 对象的引用，以便调用 light 中的方法或者直接操作 light 对象

```javascript
const OffLightState = function(light){
    this.light = light;
}
OffLightState.prototype.buttonWasPressed = function(){
    console.log('弱光');
    this.light.setState(this.light.weakLightState);
}
```



## 缺少抽象类的变通方式

我们看到，在状态类中将定义一些共同的行为方法，Context 最终会将请求委托给状态对象的这些方法，在这个例子里，这个方法就是 buttonWasPressed。无论增加了多少种状态类，它们都必须实现 buttonWasPressed 方法

在 Java 中，所有的状态类必须继承自一个 State 抽象父类，当然如果没有共同的功能值得放入抽象父类中，也可以选择实现 State 接口。这样做的原因一方面是我们曾多次提过的向上转型，另一方面是保证所有的状态子类都实现了 buttonWasPressed 方法。遗憾的是，JavaScript 既不支持抽象类，也没有接口的概念。所以在使用状态模式的时候要格外小心，如果我们编写一个状态子类时，忘记了给这个状态子类实现 buttonWasPressed 方法，则会在状态切换的时候抛出异常。为 Context 总是把请求委托给状态对象的 buttonWasPressed 方法。

建议的解决方案跟《模板方法模式》中一致，让抽象父类的抽象方法直接抛出一个异常，这个异常至少会在程序运行期间就被发现：

```javascript
const State = function(){};
State.prototype.buttonWasPressed = function(){
    throw new Error('父类的 buttonWasPressed 方法必须被重写');
}
const SuperStrongLightState = function(light){
    this.light = light;
}
SuperStrongLightState.prototype = new State();
SuperStrongLightState.prototype.buttonWasPressed = function(){
    console.log("关灯");
    this.light.setState(this.light.offLightState);
}
```

## 实例——文件上传

不论是文件上传，还是音乐、视频播放器，都可以找到一些明显的状态区分。文件上传程序中有扫描、正在上传、暂停、上传成功、上传失败这几种状态.音乐播放器可以分为加载中、正在播放、暂停、播放完毕这几种状态。点击同一个按钮，在上传中和暂停状态下的行为表现是不一样的，同时它们的样式 class 也不同。

### 更复杂的切换条件

相对于电灯的例子，文件上传不同的地方在于，现在我们将面临更加复杂的条件切换关系。在电灯的例子中，电灯的状态总是从关到开再到关，或者从关到弱光、弱光到强光、强光再到关。看起来总是循规蹈矩的 A→B→C→A，所以即使不使用状态模式来编写电灯的程序，而是使用原始的 if、else 来控制状态切换，我们也不至于在逻辑编写中迷失自己，因为状态的切换总是遵循一些简单的规律，代码如下：

```javascript
if ( this.state === 'off' ){ 
     console.log( '开弱光' ); 
     this.button.innerHTML = '下一次按我是强光'; 
     this.state = 'weakLight'; 
}else if ( this.state === 'weakLight' ){ 
     console.log( '开强光' ); 
     this.button.innerHTML = '下一次按我是关灯'; 
     this.state = 'strongLight'; 
}else if ( this.state === 'strongLight' ){ 
     console.log( '关灯' ); 
     this.button.innerHTML = '下一次按我是弱光'; 
     this.state = 'off'; 
} 
```

而文件上传的状态切换相比要复杂得多，控制文件上传的流程需要两个节点按钮，第一个用于暂停和继续上传，第二个用于删除文件

现在看看文件在不同的状态下，点击这两个按钮将分别发生什么行为。

- 文件在扫描状态中，是不能进行任何操作的，既不能暂停也不能删除文件，只能等待扫描完成。扫描完成之后，根据文件的 md5 值判断，若确认该文件已经存在于服务器，则直接跳到上传完成状态。如果该文件的大小超过允许上传的最大值，或者该文件已经损坏，则跳往上传失败状态。剩下的情况下才进入上传中状态。
- 上传过程中可以点击暂停按钮来暂停上传，暂停后点击同一个按钮会继续上传。
- 扫描和上传过程中，点击删除按钮无效，只有在暂停、上传完成、上传失败之后，才能删除文件。

### 一些准备工作

微云提供了一些浏览器插件来帮助完成文件上传。插件类型根据浏览器的不同，有可能是ActiveObject，也有可能是 WebkitPlugin。

上传是一个异步的过程，所以控件会不停地调用 JavaScript 提供的一个全局函数window.external.upload，来通知 JavaScript 目前的上传进度，控件会把当前的文件状态作为参数state 塞进 window.external.upload。在这里无法提供一个完整的上传插件，我们将简单地用setTimeout 来模拟文件的上传进度，window.external.upload 函数在此例中也只负责打印一些 log：

```javascript
window.external.upload = function(state){
    console.log(state); // 可能为 sign、uploading、done、error 
}
// 用于上传的插件对象
const plugin = (function(){
    const plugin = document.createElement('embed');
    plugin.style.display = 'none';
    plugin.type = 'application/txftn-webkit';
    plugin.sign = function(){
        console.log("开始文件扫描");
    }
    plugin.pause = function(){ 
        console.log( '暂停文件上传' ); 
    }; 
    plugin.uploading = function(){ 
        console.log( '开始文件上传' ); 
    }; 
    plugin.del = function(){ 
        console.log( '删除文件上传' ); 
    } 
    plugin.done = function(){ 
        console.log( '文件上传完成' ); 
    }    
})();	
```

### 开始编写代码

接下来开始完成其他代码的编写，先定义 Upload 类，控制上传过程的对象将从 Upload 类中创建而来。

Upload.prototype.init 方法会进行一些初始化工作，包括创建页面中的一些节点。在这些节点里，起主要作用的是两个用于控制上传流程的按钮，第一个按钮用于暂停和继续上传，第二个用于删除文件。

是 Upload.prototype.changeState 方法，它负责切换状态之后的具体行为，包括改变按钮的 innerHTML，以及调用插件开始一些“真正”的操作。



```javascript
window.external.upload = function (state) {
  // 可能为 sign、uploading、done、error 
  console.log(state); 
}
// 用于上传的插件对象
const plugin = (function () {
  const plugin = document.createElement('embed');
  plugin.style.display = 'none';
  plugin.type = 'application/txftn-webkit';
  plugin.sign = function () {
    console.log("开始文件扫描");
  }
  plugin.pause = function () {
    console.log('暂停文件上传');
  };
  plugin.uploading = function () {
    console.log('开始文件上传');
  };
  plugin.del = function () {
    console.log('删除文件上传');
  }
  plugin.done = function () {
    console.log('文件上传完成');
  }
  document.body.appendChild( plugin ); 
  return plugin; 
})();

const Upload = function (fileName) {
  this.plugin = plugin;
  this.fileName = fileName;
  this.button1 = null;
  this.button2 = null;
  this.state = 'sign'; // 设置初始状态为 waiting
}

Upload.prototype.init = function () {
  const self = this;
  this.dom = document.createElement('div');
  this.dom.innerHTML = '<span>文件名称:'+ this.fileName +'</span>\ <button data-action="button1">扫描中</button>\ <button data-action="button2">删除</button>'; 
  document.body.appendChild(this.dom);
  this.button1 = this.dom.querySelector( '[data-action="button1"]' ); 
  this.button2 = this.dom.querySelector( '[data-action="button2"]' ); 
  this.bindEvent(); 		  
}

Upload.prototype.bindEvent = function () {
  const self = this;
  console.log(this);
  this.button1.onclick = function () {
    if (self.state === 'sign') { // 扫描状态下，任何操作无效
      console.log('扫描中，点击无效...');
    } else if (self.state === 'uploading') { // 上传中，点击切换到暂停
      self.changeState('pause');
    } else if (self.state === 'pause') { // 暂停中，点击切换到上传中
      self.changeState('uploading');
    } else if (self.state === 'done') {
      console.log('文件已完成上传, 点击无效');
    } else if (self.state === 'error') {
      console.log('文件上传失败, 点击无效');
    }
  };
  this.button2.onclick = function () {
    if (self.state === 'done' || self.state === 'error'
      || self.state === 'pause') {
      // 上传完成、上传失败和暂停状态下可以删除
      self.changeState('del');
    } else if (self.state === 'sign') {
      console.log('文件正在扫描中，不能删除');
    } else if (self.state === 'uploading') {
      console.log('文件正在上传中，不能删除');
    }
  };
}
Upload.prototype.changeState = function( state ){ 
    switch(state) {
        case 'sign': 
        this.plugin.sign(); 
        this.button1.innerHTML = '扫描中，任何操作无效'; 
        break; 
        case 'uploading': 
        this.plugin.uploading(); 
        this.button1.innerHTML = '正在上传，点击暂停'; 
        break; 
        case 'pause': 
        this.plugin.pause(); 
        this.button1.innerHTML = '已暂停，点击继续上传'; 
        break; 
        case 'done': 
        this.plugin.done(); 
        this.button1.innerHTML = '上传完成'; 
        break; 
        case 'error': 
        this.button1.innerHTML = '上传失败'; 
        break; 
        case 'del': 
        this.plugin.del(); 
        this.dom.parentNode.removeChild( this.dom ); 
        console.log( '删除完成' ); 
        break; 
    } 
    this.state = state; 
}; 
// 测试工作
const uploadObj = new Upload( 'JavaScript 设计模式与开发实践' ); 
uploadObj.init(); 
window.external.upload = function( state ){ // 插件调用 JavaScript 的方法
    uploadObj.changeState( state ); 
}; 
window.external.upload( 'sign' ); // 文件开始扫描
setTimeout(function(){ 
 window.external.upload( 'uploading' ); // 1 秒后开始上传
}, 1000 ); 
setTimeout(function(){ 
 window.external.upload( 'done' ); // 5 秒后上传完成
}, 5000 );		
```

至此就完成了一个简单的文件上传程序的编写。当然这仍然是一个反例，这里的缺点跟电灯例子中的第一段代码一样，程序中充斥着 if、else 条件分支，状态和行为都被耦合在一个巨大的方法里，我们很难修改和扩展这个状态机。文件状态之间的联系如此复杂，这个问题显得更加严重了。

### 运用状态模式重构文件上传

状态模式在文件上传的程序中，是最优雅的解决办法之一。

第一步仍然是提供 window.external.upload 函数，在页面中模拟创建上传插件，这部分代码没有改变：

```javascript
window.external.upload = function (state) {
  // 可能为 sign、uploading、done、error 
  console.log(state); 
}
// 用于上传的插件对象
const plugin = (function () {
  const plugin = document.createElement('embed');
  plugin.style.display = 'none';
  plugin.type = 'application/txftn-webkit';
  plugin.sign = function () {
    console.log("开始文件扫描");
  }
  plugin.pause = function () {
    console.log('暂停文件上传');
  };
  plugin.uploading = function () {
    console.log('开始文件上传');
  };
  plugin.del = function () {
    console.log('删除文件上传');
  }
  plugin.done = function () {
    console.log('文件上传完成');
  }
  document.body.appendChild( plugin ); 
  return plugin; 
})();
```

第二步，改造 Upload 构造函数，在构造函数中为每种状态子类都创建一个实例对象：

```javascript
const Upload = function(fileName){
    this.plugin = plugin;
    this.fileName = fileName;
    this.button1 = null;
    this.button2 = null;
    this.signState = new SignState(this);
    this.uploadingState = new UploadingState(this);
    this.pauseState = new PauseState(this);
    this.doneState = new DoneState(this);
    this.errorState = new ErrorState(this);
    this.currState = this.signState;
}
```

第三步，Upload.prototype.init 方法无需改变，仍然负责往页面中创建跟上传有关的 DOM 节点，并开始绑定按钮的事件：

```javascript

Upload.prototype.init = function(){
    const that = this;
    this.dom = document.createElement( 'div' ); 
    this.dom.innerHTML = '<span>文件名称:'+ this.fileName +'</span>\ <button data-action="button1">扫描中</button>\ <button data-action="button2">删除</button>'; 
    document.body.appendChild(this.dom);
    this.button1 = this.dom.querySelector( '[data-action="button1"]' ); 
    this.button2 = this.dom.querySelector( '[data-action="button2"]' ); 
    this.bindEvent(); 	
}
```

第四步，负责具体的按钮事件实现，在点击了按钮之后，Context 并不做任何具体的操作，而是把请求委托给当前的状态类来执行：

````javascript
Upload.prototype.bindEvent = function(){
    const self = this;
    this.button1.onclick = function(){
        self.currState.clickHandler1();
    }
    this.button2.onclick = function(){
        self.currState.clickHandler2();
    }			
}
// 把状态对应的逻辑行为放在 Upload 类中
Upload.prototype.sign = function(){
    this.plugin.sign();
    this.currState = this.signState;
}

Upload.prototype.uploading = function(){
    this.button1.innerHTML = '正在上传，点击暂停';
    this.plugin.uploading();
    this.currState = this.uploadingState;
}		

Upload.prototype.done = function(){ 
    this.button1.innerHTML = '上传完成'; 
    this.plugin.done(); 
    this.currState = this.doneState; 
}; 		

Upload.prototype.pause = function(){
    this.button1.innerHTML = '已暂停，点击继续上传';
    this.plugin.pause();
    this.currState = this.pauseState;			
}

Upload.prototype.del = function(){
    this.plugin.del();
    this.dom.parentNode.removeChild(this.dom);			
}		
````

第五步，工作略显乏味，我们要编写各个状态类的实现。值得注意的是，我们使用了StateFactory，从而避免因为 JavaScript 中没有抽象类所带来的问题。

```javascript


const StateFactory = (function(){
    const State = function(){};
    State.prototype.clickHandler1 = function(){
        throw new Error('子类必须重写父类的 clickHandler1 方法');
    }
    State.prototype.clickHandler2 = function(){
        throw new Error('子类必须重写父类的 clickHandler2 方法');
    }	
    return function(param){
        const F = function(uploadObj){
            this.uploadObj = uploadObj;
        }
        F.prototype = new State();
        for(let i in param){
            F.prototype[i] = param[i];
        }
        return F;
    }		
})();	

const SignState = StateFactory({
    clickHandler1:function(){
        console.log('扫描中，点击无效...');
    },
    clickHandler2:function(){
        console.log('文件正在上传中，不能删除');
    }
});
const UploadingState = StateFactory({
    clickHandler1:function(){
        this.uploadObj.pause();
    },
    clickHandler2:function(){
        console.log('文件正在上传中，不能删除');
    }
});
const PauseState = StateFactory({
    clickHandler1:function(){
        this.uploadObj.uploading();
    },
    clickHandler2:function(){
        this.uploadObj.del();
    }
});
const DoneState = StateFactory({
    clickHandler1:function(){
        console.log('文件已完成上传，点击无效');
    },
    clickHandler2:function(){
        this.uploadObj.del();
    }
});	
const ErrorState = StateFactory({
    clickHandler1:function(){
        console.log('文件上传失败');
    },
    clickHandler2:function(){
        this.uploadObj.del();
    }
});	
```

测试：

```javascript
const uploadObj = new Upload('JavaScript 设计模式与开发实践');
uploadObj.init();

window.external.upload = function(state){
    uploadObj[state]();
}

window.external.upload('sign');

setTimeout(function(){
    window.external.upload('uploading');
},1000);

setTimeout(function(){
    window.external.upload('done');
},5000);
```



## 优缺点

- 状态模式定义了状态与行为之间的关系，并将它们封装在一个类里面，通过增加新的状态类，很容易增加新的状态和转换。
- 避免 Context 无限膨胀，状态切换的逻辑被分布在状态类中，也去掉了 Context 中原本过多的条件分支。
- 用对象代替字符串来记录当前状态，使得状态的切换更加一目了然。
- Context 中的请求动作和状态中封装的行为可以非常容易地独立变化而互不影响。

状态模式的缺点是会在系统中定义许多状态类，编写20个状态类是一项枯燥乏味的工作，而且系统中会因此而增加不少的对象，另外，由于逻辑分散在状类中，虽然避免了不受欢迎的条件分支语句，但也造成了逻辑分散的问题，我们无法在一个地方就看出整个状态机的逻辑。

## 性能优化点

上面的；两个例子中，我们并没有太多地从性能方法考虑问题，实际上，这里有一些较大的优化方案：

- 有两种选择来管理 state 对象的创建和销毁。第一种是仅当 state 对象被需要时才创建并随后销毁，另一种是可以一开始就创建所有的状态对象，并且始终不销毁它们。如果 state 对象过于庞大，可以用第一种方式来节省内存，这样就可以避免一些不会用到的对象并及时回收它们。但如果状态的改变很频繁，最好一开始就把这些 state 对象都创建出来，也没有必要销毁它们，因为可能很快就再次用到它们。
- 上面的例子中，我们为每个 Context 对象都创建了一组 state 对象，实际上这些 state 对象之间是可以共享的，各个 Context 对象可以共享一个state 对象，也就是享元模式的应用。

## 和策略模式的关系

状态模式和策略模式像一对双胞胎，它们都封装了一系列的算法或者行为，它们的类图看起来几乎一模一样，但在意图上有很大不同，因此它们是两种迥然不同的模式。

相同点是它们都有一个上下文、一些策略类或者状态类，上下文把请求委托给这些类来执行。

它们之间的区别是策略模式中的各个策略类之间是平等又平行的，它们之间没有任何联系，所以客户必须熟知这些策略类的作用，以便客户可以主动切换算法，而在状态模式中，状态和状态对应的行为早已经封装好了，状态之间的切换也早被规定好了，改变行为这件事情发生在状态模式的内部。对客户来说，并不需要了解这些细节，这正是状态模式的作用所在。

## JavaScript 版本的状态机

状态模式是状态机的实现之一，但在 JavaScript 这种无类的语言中，没有规定让状态对象一定要从类中创建而来。另外一点，JavaScript 可以非常方便地使用委托技术，并不需要实现让一个对象持有另一个对象。下面的状态机选择了通过 Function.prototype.call 方法直接把请求委托给某个字面量对象来执行。

改写电灯的例子，来展示这种更加轻巧的做法：

```javascript
const Light = function(){
    this.currState = FSM.off;
    this.button = null;
}
Light.prototype.init = function(){
    const button = document.createElement('button'),
    self = this;
    button.innerHTML = '已关灯'；
    this.button = document.body.appendChild(button);
    this.button.onclick = function(){
        self.currState.buttonWasPressed.call(self);
    }
}
const FSM = {
    off:{
        buttonWasPressed:function(){
            console.log('关灯');
            this.button.innerHTML = '下一次按我是开灯';
            this.currState = FSM.on;
        }
    },
    on:{
        buttonWasPressed:function(){
            console.log('开灯');
            this.button.innerHTML = '下一次按我是关灯';
            this.currState = FSM.on;
        }
    }
}
const light = new Light();
light.init();
```

利用下面的 delegate 函数来完成这个状态的编写，这是面向对象设计和闭包互换的一个例子，前者把变量保存为对象的属性，而后者把变量封闭在闭包形成的环境中：

```javascript
const delegate = function(client,delegation){
    return{
        buttonWasPressed:function(){
            return delegation.buttonWasPressed.apply(client,arguments);
        }
    }
}

const FSM = { 
    off: { 
        buttonWasPressed: function(){ 
            console.log( '关灯' ); 
            this.button.innerHTML = '下一次按我是开灯'; 
            this.currState = this.onState; 
        } 
    }, 
    on: { 
        buttonWasPressed: function(){ 
            console.log( '开灯' ); 
            this.button.innerHTML = '下一次按我是关灯'; 
            this.currState = this.offState; 
        } 
    } 
};
const Light = function(){
    this.offState = delegate(this,FSM.off);
    this.onState = delegate(this,FSM.on);
    this.currState =this.offState;
    this.button =null;
} 
Light.prototype.init = funuction(){
    const button = document.createElement('button'),
    self = this;
    button.innerHTML = '已关灯'；
    this.button = document.body.appendChild(button);
    this.button.onclick = function(){
        self.currState.buttonWasPressed.call(self);
    }			
}
const light = new Light();
light.init();	
```

## 实际项目中的其他状态机

在实际开发中，很多场景都可以用状态机来模拟。比如一个下拉菜单在 hover 动作下有显示、悬浮、隐藏等状态，一次TCP 请求有建立连接、监听、关闭等状态，一个格斗游戏中任务有攻击、防御、跳跃、跌倒等状态。

状态机在游戏开发中也有着广泛的用途，特别是游戏 AI 的逻辑编写，HTML5 版街头霸王游戏里，游戏主角 Ryu 有走动、攻击、防御、跌倒、跳跃等多种状态。这些状态之间既互相联系又互相约束。比如 Ryu 在走动的过程中如果被攻击，就会由走动状态切换为跌倒状态。在跌倒状态下，Ryu 既不能攻击也不能防御。同样，Ryu 也不能在跳跃的过程中切换到防御状态，但是可以进行攻击。这种场景就很适合用状态机来描述。代码如下：

```javascript
const FSM = { 
    walk: { 
        attack: function(){ 
            console.log( '攻击' ); 
        }, 
        defense: function(){ 
            console.log( '防御' ); 
        }, 
        jump: function(){ 
            console.log( '跳跃' ); 
        } 
    }, 
    attack: { 
        walk: function(){ 
            console.log( '攻击的时候不能行走' ); 
        }, 
        defense: function(){ 
            console.log( '攻击的时候不能防御' ); 
        }, 
        jump: function(){ 
            console.log( '攻击的时候不能跳跃' ); 
        } 
    } 
} 
```



## 小结

讲解了状态模式在实际开发中的应用。状态模式也许是被大家低估的模式之一。实际上，通过状态模式重构代码之后，很多杂乱无章的代码会变得更加清晰。