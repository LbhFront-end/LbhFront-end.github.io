---
title: JavaScript设计模式——模板方法模式
date: 2019-03-21 11:30:00
tags: JavaScript设计模式
categories: 
	- JavaScript设计模式



---

学习曾探的 《JavaScript设计模式与开发实践》并做记录。

[书籍的购买链接](http://www.ituring.com.cn/book/1632/)

设计模式的定义是：在面向对象软件设计过程中针对特定问题的简洁而优雅的解决方案。

通俗一点，设计模式是在某种场合下对某个问题的一种解决方案。是给面向对象软件开发中的一些好的设计取个名字。

# JavaScript设计模式——模板方法模式

在 JavaScript 开发中用到继承的场景其实不是很多，很多时候我们都喜欢用 mix-in 的方式给对象扩展属性。但这部代表继承在 JavaScript 中没有用武之地，虽然没有真正的类和继承机制，但是我们可以通过原型 prototype 来变相地继承。

本章讨论一种基于继承的设计模式——模板方法（Template Method）模式

## 定义和组成

模板方法模式只是一种需要使用继承就可以实现的非常简单的模式。

模板方法模式由两个部分结构组成，第一部分是抽象父类，第二部分是具体的实现子类。通常在抽象父类中封装了子类的算法框架，包括实现一些公共的方法以及封装子类中所有方法的执行顺序。子类通过继承这个抽象类，也继承了整个算法结构，并且可以选择重写父类的方法。

假如我们有一些平行的子类，各个子类之间有一些相同的行为，也有一些不同的行为。如果相同和不同的行为都混合在各个子类的实现中，说明这些相同的行为会在各个子类中重复出现。但实际上，相同的行为可以被搬移到另外一个单一的地方，模板方法模式就是为了解决这个问题而生的。在模板方法模式中，子类实现的相同部分被上移到了父类中，而将不同的部分留待子类类实现。也很好地体现了泛化的思想。

## Coffee or Tea

咖啡和茶是一个经典的例子。

### 先泡一杯咖啡

首先，我们来泡一杯咖啡，如果没有什么太多个性化的需求，一般的步骤如下：

1. 把水煮沸
2. 用沸水冲泡咖啡
3. 把咖啡倒进杯子
4. 加糖和牛奶

代码：

```javascript
var Coffee = function(){}
Coffee.prototype.boilWater = function(){
    console.log('把水煮沸');
}		
Coffee.prototype.brewCoffeeGriends  = function(){
    console.log('用沸水冲泡咖啡');
}		
Coffee.prototype.pourInCup  = function(){
    console.log('把咖啡倒进杯子');
}		
Coffee.prototype.addSugarAndMilk  = function(){
    console.log('加糖和牛奶');
}
Coffee.prototype.init = function(){
    this.boilWater()
    this.brewCoffeeGriends()
    this.pourInCup()
    this.addSugarAndMilk()
}
var coffee = new Coffee();
coffee.init();
```

### 泡一壶茶

开始准备我们的茶，步骤差不多：

1. 把水煮沸
2. 用沸水浸泡茶叶
3. 把茶水倒进杯子
4. 加柠檬

代码实现：

```javascript
var Tea = function(){}
Tea.prototype.boilWater = function(){
    console.log('把水煮沸');
}		
Tea.prototype.steepTeaBag   = function(){
    console.log('用沸水浸泡茶叶');
}		
Tea.prototype.pourInCup  = function(){
    console.log('把茶水倒进杯子');
}		
Tea.prototype.addLemon  = function(){
    console.log('加柠檬');
}
Tea.prototype.init = function(){
    this.boilWater()
    this.steepTeaBag()
    this.pourInCup()
    this.addLemon()
}		
var tea = new Tea();
tea.init();	
```

### 分离出共同点

我们可以发现其实两个步骤是大同小异的。

主要体现为：

- 原料不同。一个是咖啡，一个是茶，但我们都可以统称之为饮料
- 泡的方式不同。咖啡是冲泡，而茶叶是浸泡，但是都可以抽象为“泡”
- 加入的调料不同。一个是糖和牛奶，一个是柠檬，但是都可以抽象为 “调料”

不管是茶还是咖啡都可以整理为下面四步：

1. 把水煮沸
2. 用沸水浸泡饮料
3. 把饮料倒进杯子
4. 加调料(condiments)

现在可以创建一个抽象父类来表示泡(brew)一杯饮料(Beverage)的整个过程：

```javascript
var Beverage = function(){}
Beverage.prototype.boilWater = function(){
    console.log('把水煮沸');
}	
// 空方法，应该由子类重写
Beverage.prototype.brew  = function(){}		
Beverage.prototype.pourInCup  = function(){}		
Beverage.prototype.addCondiments  = function(){}
Beverage.prototype.init = function(){
    this.boilWater()
    this.brew()
    this.pourInCup()
    this.addCondiments()
}	
```

### 创建 Coffee 子类和 Tea 子类

继承饮料类：

```javascript
var Coffee = function(){}
Coffee.prototype = new Beverage();
// 重写抽象父类中的一些方法，把只有把水煮沸这个行为可以使用父类中的 boilWater 方法，其他都需要在 Coffee 子类中重写
Coffee.prototype.brew  = function(){
    console.log('用沸水冲泡咖啡');
}		
Coffee.prototype.pourInCup  = function(){
    console.log('把咖啡倒进杯子');
}		
Coffee.prototype.addCondiments  = function(){
    console.log('加糖和牛奶');
}
var coffee = new Coffee();
coffee.init();	
```

当调用 coffee 对象的 init 方法时候，由于 coffee 对象和 Coffee 构造器的原型 prototype 上没有这个方法，所以该请求会顺着原型链，被委托给 Coffee 的父类 Beverage 原型上的 init 方法。而这个方法已经规定好了泡饮料的顺序，所以我们成功地泡出了一杯咖啡。

Tea 类：

```javascript
var Tea = function(){}
Tea.prototype = new Beverage();
Tea.prototype.brew  = function(){
    console.log('用沸水浸泡茶叶');
}		
Tea.prototype.pourInCup  = function(){
    console.log('把茶水倒进杯子');
}		
Tea.prototype.addCondiments  = function(){
    console.log('加柠檬');
}
var tea = new Tea();
tea.init();	
```

`Beverage.prototype.init` 就是模板方法，该方法中封装了子类的算法框架，他作为一个算法的模板，指导子类以何种顺序去执行哪些方法。在这个方法中，算法内的每一步骤都清楚地展示在我们面前。

## 抽象类

在 Java 中，类分成两种，一种是具体类，另一种就是抽象类。具体类可以被实例化，抽象类不能被实例化。

由于抽象类不能被实例化，如果有人编写了一个抽象类，那么这个抽象类一定是用来被某些具体类继承的。

抽象类和接口一样可以用于向上转型，在静态类型语言中，编译器对类型检查总是一个绕不开的话题与困扰。虽然类型检查可以提供程序的安全性，但繁琐而严格的类型检查也时常会让程序员觉得麻烦。把对象的真正类型隐藏在抽象类或者接口之后，这些对象才可以被互相替换使用。这可以让我们 Java 程序尽量避免依赖倒置原则。

除了用于向上转型，抽象类也可以表示一种契约。继承了这个抽象类的所有子类都将拥有跟抽象类一致的接口方法，抽象类的主要作用就是为它的子类定义这些公共接口。如果我们在子类中删除了这些方法中的某一个，那么将不能通过编译器的检查，这在某些场景下是非常有用的。例如，Coffee 子类中没有实现对应的 brew 方法，那么我们百分之百得不到一杯咖啡。既然父类规定了子类的方法和执行这些方法的顺序，子类就应该拥有这些方法，并且提供正确的实现。

### 抽象方法和具体方法

抽象方法被声明在抽象类中，抽象方法并没有具体的实现过程，都是一些 “哑” 方法。比如 Beverage 类中的 brew 方法、pourInCup 方法和 addCondiments 方法，都被声明为抽象方法。当子类继承了这个抽象类时，必须重写父类的抽象方法。

除了抽象方法之外，如果每个子类中都有一些同样的具体实现方法，那这些方法也可以选择放在抽象类中，这可以节省代码以达到复用的效果，这些方法叫做具体方法。当代码需要改变的时候，我们需要改动抽象类里的具体方法就可以了。

### 用 Java 实现 Coffee or Tea 

```java
public abstract class Beverage{ // 抽象类饮料
    final void init(){ // 模板方法
        boilWater();
        brew();
        pourInCup();
        addCondiments();
    }
    // 具体方法
    void boilWater(){
        System.out.println('把水煮沸');
    }
    // 抽象类方法
    abstract void brew();
    abstract void pourInCup();
    abstract void addCondiments();
}

public class Coffee extends Beverage{ // Coffee 类
    @Override
    void brew(){ // 子类中重写 brew 方法
        System.out.println('用沸水冲泡咖啡');
    }
    @Override
    void pourInCup(){ // 子类中重写 pourInCup 方法
        System.out.println('把咖啡倒进杯子');
    }
    @Override
    void addCondiments(){ // 子类中重写 addCondiments 方法
        System.out.println('加糖和牛奶');
    }    
}

public class Tea extends Beverage{ // Coffee 类
    @Override
    void brew(){ // 子类中重写 brew 方法
        System.out.println('用沸水浸泡茶叶');
    }
    @Override
    void pourInCup(){ // 子类中重写 pourInCup 方法
        System.out.println('把茶倒进杯子');
    }
    @Override
    void addCondiments(){ // 子类中重写 addCondiments 方法
        System.out.println('加柠檬');
    }    
}

public class Test{
    public static void prepareRecipe(Beverage beverage){
        beverage.init();
    }
    public static void main(String args[]){
        Beverage coffee = new Coffee(); // 创建 coffee 对象
        prepareRecipe(coffee); // 开始泡咖啡
         // 把水煮沸
         // 用沸水冲泡咖啡
         // 把咖啡倒进杯子
         // 加糖和牛奶        
        Beverage tea = new Tea(); // 创建 tea 对象
        prepareRecipe(tea); // 开始泡茶
         // 把水煮沸
         // 用沸水浸泡茶叶
         // 把茶倒进杯子
         // 加柠檬         
    }
}
```

### JavaScript 没有抽象类的缺点和解决方案

JavaScript 并没有从语法层面提供对抽象类的支持。抽象类的第一个作用是隐藏对象的具体类型，由于 JavaScript 是一门 “类型模糊” 的语言，所以隐藏对象的类型在 JavaScript 中并不重要。

另一方面，当我们在 JavaScript 中使用原型继承模拟传统的类式继承时，并没有编译器帮助我们进行任何形式的检查，我们也没有办法保证子类会重写父类中的 “抽象方法”。

我们知道，`Beverage.prototype.init` 作为模仿方法，已经规定了子类的算法框架，代码如下：

```javascript
Beverage.prototype.init = function(){ 
 this.boilWater(); 
 this.brew(); 
 this.pourInCup(); 
 this.addCondiments(); 
}; 
```

如果我们的 Coffee 类或者 Tea 类忘记实现这 4 个方法中的一个呢？拿 brew 方法举例，如果我们忘记编写 Coffee.prototype.brew 方法，那么当请求 coffee 对象的 brew 时，请求会顺着原型链找到 Beverage“父类”对应的 Beverage.prototype.brew 方法，而 Beverage.prototype.brew 方到目前为止是一个空方法，这显然是不能符合我们需要的。

在 Java 中编译器会保证子类会重写父类中的抽象方法，但在 JavaScript 中却没有进行这些检查工作。我们在编写代码的时候得不到任何形式的警告，完全寄托于程序员的记忆力和自觉性是很危险的，特别是当我们使用模板方法模式这种完全依赖继承而实现的设计模式时。

下面提供两种变通的解决方案。

- 第 1 种方案是用鸭子类型来模拟接口检查，以便确保子类中确实重写了父类的方法。但模拟接口检查会带来不必要的复杂性，而且要求程序员主动进行这些接口检查，这就要求我们在业务代码中添加一些跟业务逻辑无关的代码。
- 第 2 种方案是让 Beverage.prototype.brew 等方法直接抛出一个异常，如果因为粗心忘记编
  写 Coffee.prototype.brew 方法，那么至少我们会在程序运行时得到一个错误。

```javascript
Beverage.prototype.brew = function(){ 
 throw new Error( '子类必须重写 brew 方法' ); 
}; 
Beverage.prototype.pourInCup = function(){ 
 throw new Error( '子类必须重写 pourInCup 方法' ); 
}; 
Beverage.prototype.addCondiments = function(){ 
 throw new Error( '子类必须重写 addCondiments 方法' ); 
};
```

第 2 种解决方案的优点是实现简单，付出的额外代价很少；缺点是我们得到错误信息的时间点太靠后

我们一共有 3 次机会得到这个错误信息，第 1 次是在编写代码的时候，通过编译器的检查来得到错误信息；第 2 次是在创建对象的时候用鸭子类型来进行“接口检查”；而目前我们不得不利用最后一次机会，在程序运行过程中才知道哪里发生了错误。

## 使用场景

从大的方面来讲，模板方法模式常被架构师用于构建项目的框架，架构师订好了框架的骨架，程序员继承架构的结构后，负责往里面填空，比如 Java 程序员大多使用过的 HttpServlet 技术来开发项目。

一个基于 HttpServlet 的程序包含 7 个 生命周期，这 7 个周期分别对应一个 do 方法。

- doGet()
- doHead()
- doPut()
- doDelete()
- doOption()
- doTrace()

HttpServlet 类还提供了一个 service 方法，它就是这里的模板方法，service 规定了这些 do 方法的执行顺序，而这些 do 方法的具体实现则需要 HttpServlet 的子类来提供。

在 Web 开发中也能找打很多模板方法模式适用的场景，比如我们在构建一系列的 UI 组件，这些组件的构建过程一般如下所示：

1. 初始化一个 div 容器
2. 通过 ajax 请求拉取相应的数据
3. 把数据渲染到 div 容器里面，完成组件的构造
4. 通知用户组件渲染完毕

我们可以看到，任何组件的构建都遵循上面的四部，其中第一步和最后一步是相同的，第二步不同的地方是请求 ajax 的远程地址，第三步不同地方是渲染数据的方式。

## 钩子方法

通过模板方法模式，我们在父类中封装了子类的算法框架。这些算法框架在正常状态下是适用于多数子类的，但如果有一些特别“个性”的子类呢？比如我们在 饮料类 Beverage 中封装了饮料冲泡顺序：

1. 把水煮沸
2. 用沸水冲泡饮料
3. 把饮料倒入杯中
4. 加调料

在我们的饮料店中，根据这四个步骤制作出来的咖啡和茶，一直顺利地提供给绝大多数客人享用。但有一些客人喝咖啡是不加调料的。既然 Beverage 作为父类，已经规定好了冲泡饮料的四个步骤，那么有什么方法可以让子类不受这个约束？

钩子方法（hook） 可以用来解决这个问题，放置钩子是隔离变化的一种常见的手段，我们在父类中容易变化的地方放置钩子，钩子可以有一个默认的实现，究竟要不要 "挂钩"，这由子类自行决定。钩子方法的返回结果决定了模板方法后面部分的执行步骤，也就是程序接下来的走向，这样一来，程序就拥有了变化的可能。

在这个例子里面，我们把挂钩的名为定位 customWantsCondiments，接下来将挂钩放入 Beverage 类，看看我们如何得到一杯不需要 糖和牛奶的咖啡，代码如下：

```javascript
var Beverage = function(){}
Beverage.prototype.boilWater = function(){
    console.log('把水煮沸');
}		
Beverage.prototype.brew  = function(){
    throw new Error('子类必须重写 brew 方法');
}		
Beverage.prototype.pourInCup  = function(){
    throw new Error('子类必须重写 pourInCup 方法');
}		
Beverage.prototype.addCondiments  = function(){
    throw new Error('子类必须重写 addCondiments 方法');
}
Beverage.prototype.customerWantsCondiments   = function(){
    return false; // 默认需要调料
}	
Beverage.prototype.init = function(){
    this.boilWater()
    this.brew()
    this.pourInCup()
    if(this.customerWantsCondiments()){
        this.addCondiments()
    }		
}	

var CoffeeWithHook = function(){}
CoffeeWithHook.prototype = new Beverage();
CoffeeWithHook.prototype.brew = function(){
    console.log('用沸水冲泡咖啡');
}
CoffeeWithHook.prototype.pourInCup = function(){
    console.log('把咖啡倒进杯子');
}
CoffeeWithHook.prototype.addCondiments = function(){
    console.log('加糖和牛奶');
}
CoffeeWithHook.prototype.customerWantsCondiments = function(){
    return window.confirm('请问需要调料吗？');
}	

var coffeeWithHook = new CoffeeWithHook();
coffeeWithHook.init();
```



## 好莱坞原则

好莱坞无疑是演员的天堂，但好莱坞也有很多找不到工作的新人演员，许多新人演员在好莱坞把简历递给演艺公司之后就只有回家等待电话。有时候该演员等得不耐烦了，给演艺公司打电话询问情况，演艺公司往往这样回答：“不要来找我，我会给你打电话。”

在设计中，这样的规则就是好莱坞原则。在这一原则的指导下，我们运行底层组件将自己挂钩到高层组件中，而高层组件决定什么时候、以何种方式去使用这些底层组件，高层组件对待底层组件的方式，跟演艺公司对待新人演员一样，都是 “别调用我们，我们会调用你”。

模板方法模式是好莱坞原则的一个典型使用场景，它与好莱坞原则的联系非常明显，当我们用模板方法模式编写一个程序的时候，这意味着子类放弃了对自己的控制权，而是改成父类通知子类，哪些方法应该在什么时候被调用，作为子类，只负责提供一些设计上的细节。

除此之外，好莱坞原则还常常应用于其他模式和场景，例如发布-订阅模式和回调函数。

- 发布-订阅模式

  发布者会把消息推送给订阅者，这取代了原先不断去 fetch 消息的形式。例如假设我们乘坐出租车去一个不了解的地方，除了每过 5 秒钟问司机 "是否达到了目的地"之外，还可以在车上美美地睡一觉，然后跟司机说好，等目的地到了就叫醒你。这也相当于好莱坞原则中的 “别调用我们，我们会调用你”

- 回调函数

  在 ajax 异步请求中，由于不知道请求返回的具体时间，而通过轮询去判断是否返回数据，这项显然是不理智的行为。所以我们通常会把接下来的操作放在回调函数中，传入发起 ajax 异步请求的函数。当数据返回之后，这个回调函数才被执行，这也是好莱坞原则的一种体现。把需要执行的操作封装在回调函数里面，然后把主动权交给另一个函数。至于回调函数什么时候被调用，则是另外一个函数控制的。

## 真的需要 “继承” 吗

模板方法模式是基于继承的一种设计模式，父类封装了子类的算法框架和方法的执行顺序，子类继承父类之后，父类通知子类执行这些方法，好莱坞原则很好地诠释了这种设计技巧，即高层组件调用底层组件。

上面的例子中，我们编写了一个 Coffee or Tea 的例子。模板方法模式是为数不多的基于继承的设计模式，但 JavaScript 语言实际上没有提供真正的类式继承模式，继承是通过对象与对象之间的委托来实现的。也就是说，虽然我们在形式上借鉴了提供类式继承的语言，但本章学习到的模板方法模式并不十分正宗。

在好莱坞原则的指导下，下面的这段代码可以达到和继承一样的效果。

```javascript
var Beverage = function(param){
    var boilWater = function(){
        console.log('把水煮沸');
    };
    var brew = param.brew || function(){
        throw new Error('必须传递 brew 方法');
    };
    var pourInCup = param.pourInCup || function(){
        throw new Error('必须传递 pourInCup 方法');
    };
    var addCondiments = param.addCondiments || function(){
        throw new Error('必须传递 addCondiments 方法');
    };		
    var F = function(){};
    F.prototype.init = function(){
        boilWater();
        brew();
        pourInCup();
        addCondiments();
    }
    return F;
}

var Coffee = Beverage({
    brew:function(){
        console.log('用沸水冲泡咖啡');
    },
    pourInCup:function(){
        console.log('把咖啡倒进杯子');
    },
    addCondiments:function(){
        console.log('加糖和牛奶');
    }		
});

var Tea = Beverage({ 
    brew: function(){ 
        console.log( '用沸水浸泡茶叶' ); 
    }, 
    pourInCup: function(){ 
        console.log( '把茶倒进杯子' ); 
    }, 
    addCondiments: function(){ 
        console.log( '加柠檬' ); 
    } 
}); 

var coffee = new Coffee();
coffee.init();

var tea = new Tea();
tea.init();	
```

在这段代码中，我们把 brew、pourInCup、addCondiments 这些方法依次传入 Beverage 函数，Beverage 函数被调用之后返回构造器 F。F 类中包含了“模板方法”F.prototype.init。跟继承得到的效果一样，该“模板方法”里依然封装了饮料子类的算法框架

## 小结

模板方法模式是一种典型的通过封装变化提高系统扩展性的设计模式。在传统的面向对象语言中，一个运用模板方法模式的程序中，子类的方法种类和执行顺序都是不变的，所以我们把这部分逻辑抽象到父类的模板方法里面。而子类的方法具体怎么实现则是可变的，于是我们把这部分变化的逻辑封装到子类中。通过增加新的子类，我们便能对系统增加新的功能，并不需要改动抽象已经其他子类，这也是符合开发-封闭的原则。

但在 JavaScript 中，我们很多时候并不需要这样去实现一个模板方法模式，高阶函数是更好的选择。