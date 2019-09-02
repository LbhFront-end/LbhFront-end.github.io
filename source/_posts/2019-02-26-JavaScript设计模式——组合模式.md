---
title: JavaScript设计模式——组合模式
date: 2019-02-26 11:30:00
tags: JavaScript设计模式
categories: 
	- JavaScript设计模式


---

学习曾探的 《JavaScript设计模式与开发实践》并做记录。

[书籍的购买链接](http://www.ituring.com.cn/book/1632/)

设计模式的定义是：在面向对象软件设计过程中针对特定问题的简洁而优雅的解决方案。

通俗一点，设计模式是在某种场合下对某个问题的一种解决方案。是给面向对象软件开发中的一些好的设计取个名字。

# JavaScript设计模式——组合模式

组合模式就是用小的子对象来构建更大的对象，而这些小的对象本身也许是由更小的“孙对象”构成的。

## 回顾宏命令

宏命令对象包含了一组具体的子命令对象，不管是宏命令对象还是子命令对象，都有一个 execute 方法负责执行命令。回顾一下之前安装在万能遥控器上的宏命令代码：

```javascript
var closeDoorCommand = {
    execute:function(){
        console.log('关门');
    }
}
var openPcCommand = {
    execute:function(){
        console.log('开电脑');
    }
}

var openQQCommand = {
    execute:function(){
        console.log('登录QQ');
    }
}

var MacroCommand = function(){
    return{
        commandList:[],
        add:function(command){
            this.commandList.push(command);
        },
        execute:function(){
            for(var i=0,command;command = this.commandList[i++];){
                command.execute();
            }
        }
    }
}
var macroCommand = new MacroCommand();
macroCommand.add(closeDoorCommand);
macroCommand.add(openPcCommand);
macroCommand.add(openQQCommand);
macroCommand.execute();
```

上面的代码可以很容易发现啊，宏命令中包含了一组子命令，它们组成了一个树形结构，这里是一颗结构非常简单的树。

其中，macroCommand 被称为组合对象，closeDoorCommand、openPcCommand、openQQCommand 都是叶对象。在 macroCommand  的 execute 方法里，并不真正地执行操作，而是遍历它所包含的叶对象，把真正的 execute 请委托给这些叶对象。

macroCommand  表现得像一个命令，但它实际上只是一组真正命令的“代理”。并非真正的代理，虽然结构上很相似，但 macroCommand   只负责传递请求给也对象，它的目的不在于控制对业对象的访问。

## 组合模式的用途

组合模式将对象合成树形结构，以表示 “部分——整体”的层次结构。除了用来表示树形结构之外，组合模式的另一个好处是通过对象的多态性表现，使得用户对单个对象和组合对象的使用具有一致性，下面分别说明：

- 表示树形结构。通过回顾上面的例子，我们很容易找到组合模式的一个优点：提供一种遍历树形结构的方案，通过调用组合对象的 execute 方法，程序会递归调用组合对象下面的也对象的 execute 方法，所以我们万能遥控器只需要一次操作，便能一次完成关门、打开电脑、登录 QQ 这几件事情。组合模式可以非常方便地描述对象部分-整体层次结构。
- 利用对象多态性统一对待组合对象和单个对象。利用对象的多态性表现，可以使客户端忽略组合对象和单个对象的不同。在组合模式中，客户将统一地使用组合结构中的所有对象，而不需要关系它究竟是组合对象还是单个对象。

在实际开发中会给客户带来相当大的便利性，当我们往万能遥控器里面添加一个命令的时候，并不关心这个命令是宏命令还是普通子命令。这点对于我们不重要，我们只需要确定它是一个命令，并且这个命令拥有可执行的 execute 方法，那么这个命令就可以被添加进万能遥控器。

当宏命令和通过子命令接收到执行 execute 方法的请求的时候，宏命令和普通子命令都会做它们各自认为正确的事情。这些差异是隐藏在客户背后的，在客户看来，这种透明性可以让我们非常自由地扩展这个万能遥控器。

## 请求在树中传递的过程

在组合模式中，请求在树中传递的过程总是遵循一种逻辑。

以宏命令为例子，请求从树的顶端的对象往下传递，如果当前处理请求的对象是叶对象（普通子命令），叶对象自身会对请求做出对应的处理。如果当前处理请的对象是组合对象（宏命令），组合对象则会遍历它属下的子节点，将请求继续传递给这些子节点。

总而言之，如果子节点是也对象，叶对象自身会处理这个请求，而如果子节点还是组合对象，请求就会继续传递下去。叶对象下面不会再有其他子节点，一个叶对象就是树的这条树枝的尽头，组合对象下面可能还有有子节点。

请求从上到下沿着树进行传递，直到树的尽头，作为客户，只需要关心树最顶层的组合对象，客户只需要请求这个组合对象，请求便会沿着树往下传递，依次达到所有的叶对象。

在刚刚的例子中，由于宏命令和子命令组成的树太过简单，我们还不清楚地看到组合模式带来的好处，如果只是简单地遍历一组节点，迭代器便能解决所有的问题。下面创造一个更强大的宏命令，这个宏命令中又包含一些宏命令和普通子命令，看起来是一颗相对复杂的树。

## 更强大的宏命令

目前的万能遥控器，包含了关门、开电脑、登录QQ 这 3 个命令。现在我们需要一个 “超级万能遥控器”，可以控制家里的所有电器，这个遥控器拥有以下的功能：

- 打开空调
- 打开电视和音响
- 关门、开电脑、登录QQ

```html
<body>
	<button id="button">按我</button>
	<script>
		var MacroCommand = function(){
			return{
				commandList:[],
				add:function(command){
					this.commandList.push(command);
				},
				execute:function(){
					for(var i=0,command;command = this.commandList[i++];){
						command.execute();
					}
				}
			}
		}
		var openAcCommand = {
			execute:function(){
				console.log('打开空调');
			}
		}	
		// 家里的电视和音响是连接在一起的，所以可以用一个宏命令来组合打开电视和打开音响的命令
		var openTvCommand  = {
			execute:function(){
				console.log('打开电视');
			}
		}		
		var openSoundCommand = {
			execute:function(){
				console.log('打开音响');
			}
		}
		var macroCommand1 = new MacroCommand();
		macroCommand1.add(openTvCommand);
		macroCommand1.add(openSoundCommand);

		// 关门、打开电脑和打登录 QQ 的命令
		var closeDoorCommand  = {
			execute:function(){
				console.log('关门');
			}
		}		
		var openPcCommand   = {
			execute:function(){
				console.log('开电脑');
			}
		}		
		var openQQCommand  = {
			execute:function(){
				console.log('打开QQ');
			}
		}		
		var macroCommand2 = new MacroCommand();
		macroCommand2.add(closeDoorCommand);
		macroCommand2.add(openPcCommand);
		macroCommand2.add(openQQCommand);		

		// 把所有命令组合成为一个超级命令
		var macroCommand = new MacroCommand();
		macroCommand.add(openAcCommand);
		macroCommand.add(macroCommand1);
		macroCommand.add(macroCommand2);

		// 最后给遥控器绑定“超级命令”
		var setCommand = (function(command){
			document.getElementById('button').onclick = function(){
				command.execute();
			}
		})(macroCommand);
	</script>
</body>
```

当按下遥控器的时候，所有命令都被依次执行。

基本对象可以被组合成为复杂的组合对象，组合对象又可以被组合，这样不断递归下去，这棵树的结构可以支持任意多的复杂度。在树最终被构造完成之后，让整颗树最终运转起来的步骤是非常简单，组需要调用上层对象的 execute 方法。每当对最上层的对象进行一次请求，实际上是对整个树进行深度优化的搜索，而创建组合对象的程序员并不关心内在的细节，往这棵树里面添加一些新的节点对象是非常容易的事情。

## 抽象类在组合模式中的作用

组合模式的最大优点在于可以一致地对待组合对象和基本对象。客户不需要知道当前处理的宏命令还是普通命令，只要它是一个命令，并且有 execute方法，这个命令就可以被添加到树中。

这种透明性带来的便利，在静态类型原样中体现尤为明显。在 Java 中，实现组合模式的关键是 Composite 类 和 Leaf 类都必须继承一个Component 类。这个 Component 抽象类既代表组合对象又代表叶对象，它能够保证组合对象和叶对象拥有同样名字的方法，从而对同一消息都做出反馈。组合对象和叶对象的具体类型被隐藏在 Component 抽象类身后。

针对 Component 抽象类来编写程序，客户操作始终是 Component 对象，而不用去区分到底是组合对象还是叶对象。所以我们往同一个对象里面的 add 方法，既可以添加 组合对象也可以添加叶对象。代码如下：

```java
public abstract class Component {
    public void add(Component child){}
    public void remove(Component child){}
}
public class Composite extends Component {
    public void add(Component child){}
    public void remove(Component child){}
}
public class Leaf  extends Component {
    public void add(Component child){
        throw new  UnsupportedOperationException(); // 叶对象不能添加子节点
    }
    public void remove(Component child){}
}
public class Client(){
    public static void main(String args[]){
        Component root = new Composite();
        Component c1 = new Composite();
        Component c2 = new Composite();
        Component leaf1 = new Leaf();
        Component leaf2 = new Leaf();
        root.add(c1);
        c1.add(leaf1);
        root.add(c2);
        c2.add(leaf1);
        
        root.remove();
    }
}
```

在 JavaScript 这种动态类型语言中，对象的多态性是与生俱来的，也没有编译器去检查变量的类型，所以我们通常不会去模拟一个 “怪异” 的抽象类，JavaScript 中实现组合模式的难点在于要保证组合对象和叶对象对象拥有同样的方法，这通常需要用鸭子类型的思想对它们进行接口检查。

在 JavaScript 中实现组合模式，看起来缺乏一些严谨性，我们的代码算不上安全，但能够更快速和自由地开发，这既是 JavaScript 的优点也是它的缺点。

## 透明性带来的安全问题

组合模式的透明性使得发起请的客户不用去估计组合对象和叶对象的区别，但它们在本质上是有去呗的。

组合对象可以拥有子节点，叶对象下面就没有子节点，所以我们也许会发生一些误操作，比如试图往叶对象中添加子节点。解决的方案是给叶对象也添加 add 方法，并调用这个方法，抛出一些异常来及时提醒用户，代码如下：

```javascript
var openTvCommand  = {
    execute:function(){
        console.log('打开电视');
    },
    add:function(){
        throw new Error( '叶对象不能添加子节点' ); 
    }
}	
openTvCommand.add( macroCommand ) // Uncaught Error: 叶对象不能添加子节点 
```

## 扫描文件夹

文件夹和文件之间的关系，非常使用组合模式来描述。文件夹既可以包含文件，又可以包含其他文件夹，最终可能组合成一颗树，组合模式在文件夹的应用中有一下两层好处：

- 例如我在同事的移动硬盘找到了一些电子书，想把它们复制到 F 盘中的学习资料文件夹。复制这些电子书的时候，我并不需要考虑这批文件的类型，不管它们是单独的还是被放在了文件夹中。
- 当我使用杀毒软件扫描该文件夹的时候，往往不会关心里面有多少个文件和子文件夹，组合模式使得我们需要操作最外层的文件进行扫描。

定义好文件夹 Folder 和 文件 File 这两个类：

```javascript
var Folder = function(name){
    this.name = name;
    this.files = [];
}
Folder.prototype.add = function(file){
    this.files.push(file);
}
Folder.prototype.scan = function(){
    console.log('开始扫描文件夹：'+this.name);
    for(var i=0,file,files = this.files;file=files[i++]){
        file.scan();
    }
}

var File = function(name){
    this.name = name;
}
File.prototype.add = function(){
    throw new Error('文件下面不能添加文件夹');
}
File.prototype.scan = function(){
    console.log('开始扫描文件:'+this.name);
}
```

接下来创建一些文件夹和文件对象，并且让它们组合成一棵树，这棵树就是我们 F 盘里面现有的文件结构：

```javascript
var folder = new Folder('学习资料');
var folder1 = new Folder('JavaScript');
var folder2 = new Folder('jQuery');

var file1 = new File( 'JavaScript 设计模式与开发实践' ); 
var file2 = new File( '精通 jQuery' ); 
var file3 = new File( '重构与模式' ) 

folder1.add(file1);
folder2.add(file2);
folder.add(folder1);
folder.add(folder2);
folder.add(file3);
```

现在的需求是把移动硬盘里面文件和文件夹都复制到这棵树中，假设我们已经得到了这些文件对象：

```javascript
var folder3 = new Folder( 'Nodejs' ); 
var file4 = new File( '深入浅出 Node.js' ); 
folder3.add( file4 ); 
var file5 = new File( 'JavaScript 语言精髓与编程实践' ); 
// 接下来就是把这些文件都添加到原有的树中：
folder.add( folder3 ); 
folder.add( file5 );
```

通过这个例子我们再次看到客户是如何同等对待组合对象和叶对象的。在添加一批文件的操作过程中，客户不用分辨它们到底是文件还是文件夹。新增加的文件和文件夹都能够很容易添加到原来的树结构中，和树里已有的对象一起工作。

我们改变了树的结构，增加了新的数据，却不用修改任何一句原有的代码，这是符合开发-封闭原则的。

运用了组合模式之后，扫描整个文件夹的操作也是轻而易举的，只需要操作最顶端的对象：

```javascript
folder.scan(); 
```

## 注意的地方

在使用组合模式的时候，下面有一些要注意的地方

1. **组合模式不是父子关系**

   组合模式的树型结构容易让人误以为组合对象和叶对象是父子关系，这是不正确的。组合模式是一种 HAS-A(聚合)的关系，而不是 IS-A。组合对象包含一组对象，但 Leaf 并不是 Composite 的子类。组合对象把请求委托给它所包含的所有叶对象，它们能够合作的关键是拥有同样的接口。

2. **对叶对象操作的一致性**

   组合模式要求组合对象和叶对象拥有同样的接口之外，还有一个必要的条件，就是对一组叶对象的操作必须具有一致性。

3. **双向映射关系**

   发送过节费的通知步骤是从公司到各个部门，再到各个小组，最后到每个员工的邮箱里。这本身是一个组合模式的好例子，但要考虑的一种情况是，也许某个员工隶属多个组织结构。这种情况下，对象之间的关系就不是严格意义上的层次结构，不适用用组合模式。

   可以给父节点和子节点建立双向映射关系，一个简单的方法就是给小组和成员对象增加集合来保存对方的引用。但是这种相互间的引用相当复杂，而且对象之间产生过多的耦合性，修改或者删除一个对象都变得非常困难，此时可以介入中介者模式来管理这些对象。

4. **用职责链模式提高组合模式性能**

   在组合模式中，如果树的结构过于复杂，节点数量很多，在遍历树的过程中，性能过程也许表现得不够理想。有时候我们确实可以借助一些技巧，在实际操作中避免遍历整颗树，有一种现成的方案是借助职责链模式一般需要我们手动去设置链条，但在组合模式中，父对象和子对象之间实际上形成了天然的职责链。让请求顺着链条从父对象往子对象传递，或者是反过来。直到遇到可以处理该请求的对象为止，这也是职责链模式的经典运用场景之一。

## 引用父对象

组合对象保存了它下面的子节点的引用，这是组合模式的特点，此时结构是从上至下的。但有时候我们需要在子节点保持对父节点的引用。比如在组合模式中使用职责链的时候。还有当我们删除某个文件的时候，实际上是从这个文件所在的上层文件夹删除该文件的。

现在来改写扫描文件夹的代码：

```javascript
var Folder = function(name){
    this.name = name;
    this.parent = null; // 增加父节点属性
    this.files = [];
}
Folder.prototype.add = function(file){
    file.parent = this; // 设置父对象
    this.files.push(file);
}
Folder.prototype.remove = function(){
    if(!this.parent){
        return; // 根节点或者树外的游离节点
    }
    for(var files = this.parent.files,l=files.length-1;l>=0;l--){
        var file = files[l];
        if(file === this){
            files.splice(l,1);
        }
    }
}
```

在 File.prototype.remove 方法里，首先会判断 this.parent，如果 this.parent 为 null，那么这个文件夹要么是树的根节点，要么是还没有添加到树的游离节点，这时候没有节点需要从树中移除，我们暂且让 remove 方法直接 return，表示不做任何操作。

如果 this.parent 不为 null，则说明该文件夹有父节点存在，此时遍历父节点中保存的子节点列表，删除想要删除的子节点.

File 类的实现基本一致:

```javascript
var File = function(name){
    this.parent = null; // 增加父节点属性
    this.name = name;
}
File.prototype.add = function(){
    throw new Error('文件下面不能添加文件夹');
}
File.prototype.remove = function(){
    if(!this.parent){
        return; // 根节点或者树外的游离节点
    }
    for(var files = this.parent.files,l=files.length-1;l>=0;l--){
        var file = files[l];
        if(file === this){
            files.splice(l,1);
        }
    }
}
File.prototype.scan = function(){
    console.log('开始扫描文件:'+this.name);
}
```

## 何时使用组合模式

组合模式运用得当的话，可以大大简化客户代码，一般来说，组合模式适用于下面两种情况：

- 表示对象的部分-整体层次结构。组合模式可以方便地构造一棵树来表示对象的部分-整体结构。特别是我们在开发期间不确定这棵树到底存在多少层次的时候。在树的构造最终完成之后，需要通过请求树的最顶层对象，便能对整棵树做统一的操作。在组合模式中增加和删除树的节点非常方便，并且符合开发-封闭的原则。
- 客户希望统一对待树中的所有对象。组合模式可以使客户忽略组合对象和叶对象的区别，客户在面对这颗树的时候，不用关心目前正在处理的对象是组合还是叶对象，也就是不用写一堆 if、else 来判断它们。组合对象和叶对象各自做自己正确的事情，这是组合模式最重要的能力。

## 小结

我们了解了组合模式在 JavaScript 开发中的应用。组合模式可以让我们使用树形方式创建对象的结构。我们把相同的操作应用在组合对象和单个对象上面。在大多数情况下，我们都可以忽略掉组合对象和单个对象之间的差别，从而用一致的方法来处理它们。

然后，组合模式并不是完美的，它可能会有一个这样的系统：系统中的每个对象看起来都与其他对象差不多。它们的区别只有在运行的时候才会显现出来，这会使得代码难以理解。此外，如果通过组合模式创建了太对的对象，那么这些对象可能会让系统负担不起。