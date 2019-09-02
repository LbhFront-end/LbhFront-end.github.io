---
title: 你不知道的JavaScript(上)——混合对象“类”
date: 2019-01-21 18:30:00
tags: 你不知道的JavaScript
categories: 
	- JavaScript
---

这个系列的作品是上一次当当网有活动买的，记得是上一年九月份开学季的时候了。后面一直有其他的事情，或者自身一些因素，迟迟没有开封这本书。今天立下一个 flag，希望可以在两个月内看完并记录这个系列的三本书，保持学习的激情，不断弥补自己的基础不够扎实的缺点。

[作者的github](https://github.com/getify/You-Dont-Know-JS)

书籍的购买链接，自己搜。

# 你不知道的JavaScript(上)——混合对象“类”

上一章讲了对象，下面介绍和类相关的对象编程，在研究类的具体机制之前，先介绍面向类的设计模式：实例化（instantitation）、继承（inheritance）和（相对）多态（polymorphism）

可以看到，这些概念实际上无法直接对应到 JavaScript 的对象机制，因为会介绍许多 JavaScript 开发者所使用的解决方法（比如混入，mixin）

## 类理论

类/继承描述了一种代码的组织结构形式——一种在软件中对真实世界中问题领域的建模方法。

面向对象编程强调的是数据和操作数据的行为本质上是互相关联的（当然，不同的数据有不同的行为），因为好的设计就是把数据以及和它相关的行为打包（或者说封装起来）。这在正式的计算机科学中有时被称为数据结构。

举例来说，用来表示一个单词或者是短语的一串字符通常被称为 字符串，字符就是数据，但是你关心的往往不是数据是什么，而是可以对数据做什么，所以可以应用在这种数据上的行为（计算长度、添加数据、搜索等等）都被设计成了 String 类的方法。

所有字符串都是 String 类的一个实例，也就是说它是一个包裹，包含字符数据和我们可以应用在数据上的函数。

我们还可以使用类对数据结构进行分类，可以把任意数据结构看做范围更广的定义的一种特例。“汽车”可以被当做是 “交通工具”的一种特例，后者是更广泛的类。我们可以在软件中定义一个 Vehicle 类 和 一个 Car 类来对这种关系进行建模。

Vehicle 的定义可能包含由推进器（比如引擎）、载人能力等等，这些都是 Vehicle 的行为，我们在 Vehicle 中定义的是（几乎）所有类型的交通工具（飞机、火车和汽车）都包含的东西。

在软件中，对不同交通工具重复定义 “载人功能”是没有意义的，相反，我们只要在 Vehicle 中定义一次。定义 Car 时，只要声明它继承（没有扩展）了 Vehicle 这个基本定义就可以了，Car 的定义就是对通用 Vehicle 定义的特殊化。

虽然 Vehicle 和 Car 会定义相同的方法，但是实例中的数据可能是不同的，比如每辆汽车独一无二的 VIN（汽车识别码）等等。

这就是类、继承和实例化。

类的另一个概念是多态，这概念是说父类的通用行为可以被子类用更特殊的行为重写。实际上，相对多态性运行我们从重写行为中引用基础行为。

类理论强烈建议父类和子类使用相同的方法名来表示特定的行为，从而让子类重写父类，我们之后会看到，在 JavaScript 代码中这样做会降低代码的可读性和健壮性。

### “类” 的设计模式

如果之前没有把类当做设计模式来看待，我们可能讨论比较多是面向对象设计模式。从这个角度来看，我们就是在（低级）面向对象类的基础上实现了所有（高级）的设计模式，似乎面向对象就是优秀代码的基础。

过程化编程，这种代码只包含过程（函数）调用，没有高层的抽象。

### JavaScript 中的“类” 

JavaScript 在很长的一段时间内，只有一些近似类的语法元素（new 和 instanceof），不过后面 ES6 中增加了一个元素，比如 class 关键字

这个是不是意味着 JavaScript 中实际上有类呢？简单来说：不是。

由于类是一种设计模式，所以你可以用一些方法近似实现类的功能。为了满足对于类设计模式的最普遍需求，JavaScript 提供了一些近似类的语法。虽然有近似类的语法，但是 JavaScript 的机制似乎一直在阻止你使用类设计模式。在近似类的表象之下，JavaScript 的机制其实和类完全不同。语法糖和（广泛使用的）JavaScript “类”库试图掩盖这个现实，但是你迟早会面对它：其他语法中的类和 JavaScript 中的 “类”并不一样。

总结一下，在软件设计中类是一种可选的模式，需要决定是否在 JavaScript 中使用它。

## 类的机制

在许多面向类的语言中，“标准库”会提供 Stack 类，它是一种 “栈”数据结构（支持压入、弹出等等）。Stack 类内部会有一些变量来存储数据，同时提供一些共有的可访问行为（“方法”），从而让你的代码可以和（隐藏的）数据进行交互（比如添加、删除数据）。

但是在这些语言中，实际上并不是直接操作 Stack （除非创建一个静态类成员引用）。Stack 类仅仅是一个抽象的表示，它描述了所有“栈”需要做的事，但是它本身并不是一个“栈”，你必须先实例化 Stack 类然后才能对它进行操作。

### 建造

“类”和“实例”的概念来源于房屋建造。

建筑师会规划出一个建筑的所有特性,宽高材料等等，但是它不在意建筑会被建造在哪里，也不关心数量。也不关心建筑内的内容，只关心用什么结构来容纳它们。

建筑工人会按照建造蓝图复制到现实世界中的建筑中。建筑和蓝图之间的关系是间接的，一个类就是一张蓝图，为了获得真正可以交互的对象，我们必须按照类来建造（实例化）一个东西，这个东西通常被称为实例，有需要的话，我们可以直接在实力上调用方法并访问其所有共有的数据属性。这个对象就是类中描述的所有特性的一份副本。

### 构造函数

类实例是由一个特殊的类方法构造的，这个方法名通常和类名相同，被称为构造函数，这个方法的任务就是初始化实例需要的所有消息（状态）。

举个例子，思考关于类的伪代码：

```pseudocode
class CoolGuy{
    spcialTrick = nothing
    
    CoolGuy(trick){
        spcialTrick = trick
    }
    showOff(){
        output('Here is my trick:',spcialTrick);
    }
}
```

我们可以调用类构造函数来生成一个 CoolGuy 实例：

```javascript
Joe = new CoolGuy('jumping rope');
Joe.showOff(); // Here is my trick:jumping rope
```

注意：GoolGuy 类中有一个 CoolGuy 构造函数，执行 new CoolGuy() 时实际上调用的就是它。构造函数会返回一个对象（也就是类的一个实例），之后我们在这个对象上调用 showOff() 方法，来输出指定 CoolGuy 的特长。

类构造函数属于类，而且通常和类同名，此外，构造函数大多数需要用 new 来调用，这样语言引擎才知道你想要构造一个新的类实例。

## 类的继承

在面向类的语言中，可以先定义一个类，然后定义一个继承前者的类。

后者通常被称为“子类”，前者通常被称为“父类”。这些术语=显然是类比父母和孩子，不过在意思上稍有扩展。

同理，定义好一个子类之后，相对于父类来说它就是一个独立并且完全不同的类，子类会包含父类行为的原始副本，但是也可以重写所有继承的行为甚至定义新行为。

非常重要的一点是，我们讨论的父类和子类并不是实例，父类和子类的比喻容易造成一些误解，实际上我们应当把父类和子类称为父类 DNA 和 子类 DNA ，我们根据这些 DNA 来创建一个人，然后才能和他进行沟通。

Vehicle 和 Car 类，思考下面关于类继承的伪代码：

```pseudocode
class Vehicle {
    engines = 1
    ignition(){
        output("Turning on my engine.")
    }
    drive(){
        ignition();
        output("Steering and moving forward!")
    }
}
class Car inherits Vehicle {
    wheels = 4
    drive(){
        inherited:drive()
        output("Rolling on all",wheels,"wheels")
    }
}
class SpeedBoat inherits Vehicle {
    engines = 2
    ignition(){
        output("Turning on my",engines, "engine.")
    }
    pilot(){
        inherited:drive()
        output("Speeding through the water with ease!")
    }
}
```

通过定义 Vehicle 类来假设一种发动机、一种点火方式、一种驾驶方法，但是不可以制造一个通用的“交通功能”，因为类只是一个抽象的概念。

定义了两类具体的交通工具，Car 和 SpeedBoat，它们都从 Vehicle 继承了通用的特性并根据自身类别修改了某些特性，汽车需要四个轮子，快艇需要两个发动机。因此，它必须启动两个发送机的点火装置。

### 多态

Car 重写了继承自父类的 drive() 方法，但是之后 Car 调用 inherited:drive() 方法，这表明了Car 可以继承来自父类的原始 drive()方法。快艇的 pilot() 方法同样引用了原始的 drive() 方法。

这个技术被称为多态或者虚拟多态，在上面的例子中，更恰当的说法是相对多态。

多态是一个非常广泛的话题，我们现在所说的“相对”多态只是一个方面：任何方法都可以引用继承层次中高层的方法（无论高层方法名和当前方法名是否相同）。之所以说“相对”是因为我们并不会定义想要访问的绝对继承层次（类），而是使用相对引用“查找上一层”。

在许多语言中可以使用 super 代替上面例子中的 inherited 它的含义是“超类”（superclass），表示当前的类的父类/祖先类。

多态的另一个方面是，在继承链的不同层次中一个方法名可以被多次定义，当调用方法时会自动选择合适的定义。在之前的代码中就有这样的两个例子：drive 被定义在 Vehicle 和 Car 中，ignition 被定义在 Vehicle 和 SpeedBoat 中。

> 在传统的面向类的语言中的 super 还有一个功能，就是从子类的构造函数中通过 super 可以直接调用父类的构造函数。通常来说这没有什么问题。因为对于真正的类来说，构造函数是属于类的，然后在 JavaScript 中恰好相反——实际上“类”是属于构造函数的（类似 Foo.prototype..这样的类型引用）。由于 JavaScript 中父类和子类的关系只存在于两者构造函数对应的 .prototype 对象中，因此它们的构造函数之间并不存在直接联系，从而无法简单地实现两者的相对引用（在 ES6 中可以通过 super 来”解决“这个问题）。

我们可以在 ignition() 中看到多态有趣的一点，在 pilot() 中通过相对多态引用了（继承）Vehicle中的 drive 方法直接通过名字（而不是相对引用）引用了 ignotion() 方法。

那么语言引擎会使用哪个 ignition(),Vehicle 的还是 SpeedBoat 的？实际上它会使用 SpeedBoat 中的。如果你直接实例化 Vehicle 类然后调用 它的 drive 那么语言引擎就会使用 Vehicle 中的 ignition 方法。

换言之，ignition 方法定义的多态性取决于你是在哪个类的实例中引用它。只有理解了这个细节才能理解 JavaScript 中类似（但是并不相同）的 [[Prototype]]机制。

在子类（而不是它们创建的实例对象）中也可以相对引用它继承的父类，这种相对引用通常被称为 super。

多态不表示子类和父类有关联，子类得到的只是父类的一份副本，类的继承其实就是复制。

### 多重继承

有些面向类 的语言允许继承多个类，多重继承意味着所有父类的定义都会被复制到子类中。从表面上看，对于类来说似乎是一个非常有用的功能，可以把许多功能组合在一起。然而，这个机制同时也会带来很多复杂的问题。如果两个父类中都定义了 drive 方法的话，子类引用的是哪个？这样多态继承的很多优点就不存在了。

除此之外，还有一种被称为钻石问题的变种。这问题中，子类 D 继承自两个父类（B 和 C） ，这两个父类都继承 A。如果 A 中 有  drive 方法并且 B 和 C 都重写了这个方法（多态），那当 D 引用应该选择哪个版本？

相比之下，JavaScript 要简单得多，它本身并不提供 “多重继承”功能，然后开发者会用其他办法来实现多重继承。

## 混入

在继承或者实例化时，JavaScript 的对象机制并不会自动执行复制行为，简单来说，JavaScript 中只有对象，并不存在可以被实例化的 “类”，一个对象并不会被复制到其他对象，它们会被关联起来。由于在其他语言中类表现出来的都是复制行为，因此 JavaScript 开发者也想出了一个方法来模拟类的复制行为，这个方法就是混入：显式混入和隐式混入

### 显式混入

首先我们回顾一下之前提到的 Vehicle 和 Car ，由于 JavaScript 不会自动实现 Vehicle 到 Car 的复制行为，所以我们需要手动实现复制功能。这个功能在许多库和框架汇总被称为 extend(...) ,但是为了方便理解我们称之为 mixin(...)

```javascript
// 非常简单的 mixin 例子
function mixin(sourceObj,targetObj){
	for(var key in sourceObj){
		// 只会在不存在的情况下复制
		if(!(key in targetObj)){
			targetObj[key] = sourceObj[key];
		}
	}
	return targetObj;
}

var Vehicle = {
	engines:1,
	ignition:function(){
		console.log("Turing on my engine");
	},
	drive:function(){
		this.ignition();
		console.log("Streeing and moving forward!");
	}
}

var Car = mixin(Vehicle,{
	wheels:4,
	drive:function(){
		Vehicle.drive.call(this);
		console.log("Rolling on all" +this.wheels + "wheels!");
	}
});
```

> 有一点需要注意的是，我们处理的已经不是类，因为在 JavaScript 中不存在类，Vehicle 和 Car 都是对象，供我们分别复制和粘贴

现在 Car 中就有了一份 Vehicle 属性和函数的副本，从技术角度来说，函数实际没有被复制，复制的只是函数引用。所以，Car 中的属性 ignition 只是从 Vehicle 中复制过来的对于 ignition() 函数的引用。相反，属性 engines 就是直接从 Vehicle 中复制了值1.

Car 已经有了 drive 属性（函数），所以这个属性引用并没有直接被 mixin 重写，从而保留了 Car 中定义的同名属性，实现了“子类”对 “父类”的属性的重写

#### **1.再说多态**

Vehicle.drive.call(this); 这就是显式多态，记得伪代码中对应的语句是 inherited：drive()，我们称之为相对多态。

JavaScript 并没有相对多态的机制。所以，由于 Car 和 Vehicle 中都有 drive() 函数，为了指明调用对象，我们必须使用绝对（而不是相对）引用。我们通过名称显式指定 Vehicle 对象并调用它的 drive 函数。

但是如果直接执行 Vehicle.drive()，函数调用中的 this 会被绑定到 Vehicle 对象而不是 Car 对象，这并不是我们想要的。因此，我们会使用 .call(this) 来确保 drive() 在 Car 对象的上下文中执行。

> 如果函数 Car.drive 的名称标识符并没有和 Vehicle.drive 重叠（或者说“屏蔽”）的话，我们就不用实现方法多态，因为调用 mixin(..) 有时会把函数 Vehicle.drive() 的引用复制到 Car 中，因此我们可以直接访问 this.drive().正是由于存在标识符重叠，所以必须使用更加复杂的显式多态方法。

在支持相对多态的面向类的语言中，Car 和 Vehicle 之间的联系只在类的定义的开头被创建，从而只需要在这一个地方维护两个类的联系。

但是在 JavaScript 中（由于屏蔽）使用显式伪多态会在所有需要使用（伪）多态引用的地方创建一个函数关联，这会极大地增加维护成本。此外，由于显式伪多态可以模拟多重继承，所以它会进一步增加代码的复杂度和维护程度。

使用伪多态通常会导致代码变得复杂，难以阅读并且难以维护，因此应当尽量避免使用显式伪多态，因为这样往往会得不偿失。

#### **2.混合复制**

回归之前的 mixin 函数

```javascript
// 非常简单的 mixin 例子
function mixin(sourceObj,targetObj){
	for(var key in sourceObj){
		// 只会在不存在的情况下复制
		if(!(key in targetObj)){
			targetObj[key] = sourceObj[key];
		}
	}
	return targetObj;
}
```

分析一下 mixin 的工作原理，它会遍历 sourceObj 的属性，然后在 targetObj 没有这个属性就会进行复制。由于我们是在目标对象初始化之后才进行复制，因此一定要小心不要覆盖目标对象的原有属性。

如果我们是先进行复制然后对 Car 进行特殊化的话，就可以跳过存在性检查。不过这种方法并不好用并且效率更低，不如第一种方法常用：

```javascript
// 另外一种混入函数，可能有重写风险
function mixin(sourceObj,targetObj){
	for(var key in sourceObj){
		targetObj[key] = sourceObj[key];
	}
	return targetObj;
}

var Vehicle = {
    // ...
}

// 首先创建一个空对象把 Vehicle 的内容复制进去
var Car = mixin(Vehicle,{});
// 然后把新内容复制到 Car 中
mixin({
    wheel:4,
    drive:function(){
        // ..
    }
},Car)
```

这两种方法都可以把不重叠的内容从 Vehicle 中显式复制到 Car 中。“混入”这个名字来源于这个过程的一种解释：Car 中混合了 Vehicle 的内容，就像你把巧克力混合到你最喜欢的饼干面团中一样。

复制操作完成后，Car 和 Vehicle 分离了，向 Car  中添加属性不会影响 Vehicle ，反之亦然。

> 这里跳过了一些小细节，实际上，在复制完成之后两者之间仍然有一些巧妙的方法可以影响到对方，例如引用同一个对象（比如一个数组）

由于两个对象引用的是同一个函数，因此这种复制（或者说混入）实际上并不能完全模拟面向类的语言中的复制。

JavaScript 中的函数无法（用标准、可靠的方法）真正地复制，你只是复制对共享函数对象的引用（函数就是对象）。如果你修改了贡献的函数对象（比如 ignition），比如添加了一个属性，那 Vehicle 和 Car 都会受到影响。

显式混入是 JavaScript 中一个很棒的机制，不过它的功能也没有看起来那么强大。虽然它可以把一个对象的属性复制到另一个对象中，但是这其实并不能带来太多好处，无非就是少几条定义语句，而且还会带来我们刚刚提及的函数对象引用问题。

如果你向目标对象中显式混入超过一个对象，就可以部分模仿多重继承行为，但是仍没有直接的方式来处理函数和属性的同名问题。有些开发者/库提出了“晚绑定”技术和其他的一些解决方法，但是从根本上来说，使用这些方法通常会（降低性能并且）得不偿失。

一定要注意，只在能够提高代码可读性的前提下使用显式混入，避免使用增加代码理解难度让对象关系更加复杂的模式。

如果使用混入时感觉越来越困难，那或许你应该停止使用它了。实际上，如果你必须使用一个复杂的库或者函数来实现这些细节，那就标志着你的方法是有问题的或者是不必要的。后面会试着提出一个更简单的方法，它能满足这些需求并且可以避免所有的问题。

#### **3.寄生继承**

显式混入的一种变体被称为“寄生继承”，它既是显式的又是隐式的，下面是它的工作原理：

```javascript
// 传统的 JavaScript 类 Vehicle
function Vehicle(){
	this.engines = 1;
}
Vehicle.prototype.ignition = function(){
	console.log("Turing on my engine");
}
Vehicle.prototype.drive = function(){
	this.ignition();
	console.log("Streeing and moving forward!");
}
// 寄生类 Car
function Car(){
	var car = new Vehicle();
	car.wheels = 4;
	var vehDrive = car.drive;
	car.drive = function(){
		vehDrive.call(this);
		console.log("Rolling on all" +this.wheels + "wheels!");
	}
	return car;
}
var myCar = new Car();
myCar.drive();
//Turing on my engine
// Streeing and moving forward!
// Rolling on all 4 "wheels!
```

上面的代码首先复制了一份 Vehicle （对象）父类的定义，然后混入子类（对象）的定义（如果需要的话保留到父类的特殊引用），然后用这个复合对象构建实例。

> 调用 new Car() 时会复制一个新的对象并绑定到 Car 的 this 上，但是因为我们没有使用这个对象而是返回了我们自己的 car 对象，所以最初被创建的这个对象会被丢弃，因为可以不使用 new 关键字调用 Car().这样做的结果是一样的，但是可以避免创建并丢弃多余的对象。

### 隐式混入

隐式混入和之前提到的显式伪多态很像，因此也具备同样的问题。

思考下面的代码：

```javascript
var Something = {
    cool:function(){
        this.greeting = "Hello World";
        this.count = this.count ? this.count + 1 : 1;
    }
}
Something.cool();
Something.greeting; // Hello World
Something.count; // 1
var Another = {
    cool:function(){
        // 隐式 把 Something 混入 Another
        Something.cool.call(this);
    }
}
Another.cool();
Another.greeting; // Hello World
Another.count; // 1 (count 不是共享状态)
```

通过在构造函数调用或者方法调用中使用 Something.cool.call(this)，我们实际上“借用”了函数Something.cool() 并在 Another 的上下文汇总调用了它（通过 this 绑定），最终的结果是 Something.cool() 中的赋值操作都会应用在 Another 对象上而不是 Something 对象上。

因为，我们 把 Something 的行为 "混入"到了 Another 中。

虽然这类技术利用了 this 的重新绑定功能，但是 Something.cool.call(this) 仍然无法变成相对（而且更灵活）引用，所以在使用时要非常小心，通常来说，尽量避免使用这样的结构，比保证代码的整洁和可维护性。

## 小结

类是一种设计模式，许多语言都提供了对于面向类设计软件的原生语法，JavaScript 也有类似的语法，但是和其他语言中的类完全不同。类意味着复制。

传统的类被实例化，它的行为是会被复制到实例中，类被继承时，行为也会被复制到子类中。

多态（在继承链的不同层次名称相同但是功能不同的函数）看起来似乎是从子类引用父类，但是本质上引用的其实是复制的结果。

JavaScript 并不会（像类那样）自动创建对象的副本。

混入模式（无论是显式还是隐式）可以用来模拟类的复制行为，但是通常会产生丑陋并且脆弱的语法，不如显式伪多态（OtherObj.methodName.call(this,...)），这会让代码更加困难并且难以维护。此外，显式混入实际上无法完全模拟类的复制行为，因为对象（函数）只能复制引用，无法复制被引用的对象或者是函数本身。忽视这一点会导致许多问题。

总结来说，在 JavaScript 中模拟类是得不偿失的，虽然能解决当前的问题，但是可能会埋下更多的隐患。