---
title: 为什么我要放弃javaScript数据结构与算法（第四章）—— 队列
date: 2018-08-31 16:13:41
tags: javaScript数据结构与算法
categories: 
	- javaScript相关

---



有两种结构类似于数组，但在添加和删除元素时更加可控，它们就是栈和队列。



## 第四章 队列

### 队列数据结构

队列是遵循FIFO（First In First Out，先进先出，也称为先来先服务）原则的一组有序的项。队列在尾部添加新元素，并从顶部移除元素。最新添加的元素必须排在队列的末尾。

现实中，很常见的例子就是排队。在计算机科学里面是打印队列。

### 创建队列

我们需要创建自己的类来表示一个队列，先从最基本的声明开始：

```javascript
function Queue(){
    // 这里是属性和方法
}
```

首先需要一个用于存储队列中元素的数据结构。我们可以使用数组，就像上一章 Stack 类中那样使用（你会发现其实两者很相似，只是添加和移除元素不一样而已。）

```javascript
let items = []
```

接下来需要声明一些队列可用的方法。

- enqueue（element（s））:向队列尾部添加一个（或多个）新的项。
- dequeue（）：移除队列中的第一个（排列在队伍最前面的）项，并返回被移除的元素
- front（）：返回队列中的第一个元素——最先被添加，也将是最先被移除的元素。队列不做任何变动（不移除元素，只返回元素信息——与 Stack 类的 peek 方法非常相似）
- isEmpty（）：如果队列中不包含任何元素，返回 ture，否则返回 false
- size（）:返回队列包含的元素个数，与数组的 length 属性类似。

#### 向队列添加元素

首先要实现的是 enqueue 方法。这个方法负责向队列中添加新元素，还有一个非常重要的细节，新的项目只能添加到队列末尾：

```javascript
this.enqueue = function(element){
    return items.push(element);
};
```

#### 从队列中移除元素

接下来就是 dequeue 方法，这个方法负责从队列中移除项。由于队列遵循先进先出原则，最先添加的项也是要最先被移除的。数组中的 shift  方法会从数组中移除存储在索引0（第一个位置）的元素。

```javascript
this.dequeue = function(element){
    return items.shift();
}
```

只有 enqueue 方法和 dequeue 方法可以添加和移除元素，这样就确保了 Queue 类遵循先进先出的原则。

#### 查看队列头元素

为我们类实现一些额外的辅助方法。我们想知道队列最前面是什么，可以使用 front 方法查看

```javascript
this.front = function(){
    return items[0];
}
```

#### 检查队列是否为空

```javascript
this.isEmpty = function(){
    return items.length == 0;
}
```

#### 查看队列的长度

```javascript
this.size = function(){
    return items.length;
}
```

#### 打印队列元素

```javascript
this.print = function(){
    console.log(items.toString());
}
```

#### 实例

```javascript
	function Queue(){
		let items = [];
		this.enqueue = function(element){
			return items.push(element);
		}
		this.dequeue = function(){
			return items.shift();
		}
		this.front = function(){
			return items[0];
		}
		this.isEmpty = function(){
			return items.length == 0;
		}
		this.size = function(){
			return items.length;
		}
		this.clear = function(){
			items = [];
		}
		this.print = function(){
			console.log(items.toString());
		}
	}
	let queue = new Queue(); // 新建 类 Queue 的实例 queue 
	console.log(queue.isEmpty()); // 队列没有元素，返回 true
	queue.enqueue(5); // 先队列中加 5
	queue.enqueue(8); // 先队列中加 8
	queue.dequeue(); // 减去队列的开头
	console.log(queue.front()); // 8
	queue.enqueue(11); // 先队列中加 11
	console.log(queue.size()); // 队列的长度 2
	console.log(queue.isEmpty()); // 队列有元素，返回 false
	queue.enqueue(15); // 先队列中加 15
	queue.print(); // 输出队列中的元素 8,11,15
```



### 使用ES6 语法实现的 Queue 类

我们使用一个 WeakMap 来保存私有属性items，并用外层函数（闭包）来封装 Queue 类。

```javascript
let Queue = (function(){
const items = new WeakMap(); // 声明了一个 WeakMap 类型的变量 items
class Queue{
    constructor(){
        items.set(this, []) // 在 constructor 中，以this（Stack类自己引用）为键，把代表栈的数组存入 items
    }
    enqueue(element){
        let q = items.get(this);
        q.push(element);
    }
    dequeue (){
        let q = items.get(this);
        let r = q.shift();
        return r;
    }
    front (){
        let q = items.get(this);
        return q[0];
    }
    isEmpty (){
        let q = items.get(this);
        return q.length == 0;
    }
    size (){
        let q = items.get(this);
        let r = q.length
        return r;
    }
    clear (){
        items.set(this, [])
    }
    print (){
        let q = items.get(this);
        console.log(q.toString());
    }
}
return Queue;
})();
```

### 优先队列

队列大量应用在计算机科学以及我们的生活中，其中一个就是优先队列。元素的添加和移除是基于优先级的。现实中的例子就是登机的顺序。头等舱和商务舱的乘客优先级要优于经济舱乘客。

另外一个现实的例子就是医院的候诊室。医生会优先处理病情比较严重的患者。

实现一个队列，有两种选项：设置优先级，然后在正确的位置添加元素；或者用入列操作添加元素，然后按照优先级操作它们。在这个实例中，我们会在正确的位置添加元素，因此可以对它们使用默认的出列操作。

```javascript
function ProrityQueue(){
	let items = [];
	function QueueElement(element, priority){ 
		// 参数包含了添加到队列的元素以及其的优先级
		this.element = element;
		this.priority = priority;
	}
	this.enqueue = function(element, priority){		
		let queueElement = new QueueElement(element, priority);
		let added = false;
		// 如果队列为空可以直接将元素插入，否则就要比较元素与该元素的优先级。
		// 当找到一个比要添加元素的 priority 值更高（优先级更低）的项时，
		// 我们就把元素插入它之前，但是如果优先级相同的话就遵循先进先出的原则
		for(let i = 0; i < items.length; i++){
			if(queueElement.priority < items[i].priority ){
				items.splice(i,0,queueElement);
				added = true;
				break;
			}
		}
		if(!added){
			// 如果添加元素的 priority 值大于任何已有的元素，把它添加到队列的末尾就行了
			items.push(queueElement);
		}
	}
	this.dequeue = function(){
		return items.shift();
	}
	this.front = function(){
		return items[0];
	}
	this.isEmpty = function(){
		return items.length == 0;
	}
	this.size = function(){
		return items.length;
	}
	this.clear = function(){
		items = [];
	}	
	this.print = function(){
		for(let i = 0; i < items.length; i++){
			console.log(`${items[i].element} - ${items[i].priority}`);
		}
	}
}
let prorityQueue = new ProrityQueue();
prorityQueue.enqueue('John',2);
prorityQueue.enqueue('Mike',1);
prorityQueue.enqueue('Jenny',1);
prorityQueue.print(); 
/*
	Mike - 1
	Jenny - 1
	John - 2
*/
```



### 循环队列——击鼓传花

还有另一个修改版的队列实现，就是循环队列。循环队列的一个例子就是击鼓传花游戏（Hot Potato）。在这个游戏中，孩子们围成一个圆圈，把花尽快地传递给旁边的人，某一时刻传花停止，这个时候，花就在谁的手里，谁就退出圆圈结束游戏。重复这个过程，直到最后一个孩子，就是胜者。

在这个例子中，我们要实现一个模拟的击鼓传花游戏。

```javascript
function Queue(){
    let items = [];
    this.enqueue = function(element){
        return items.push(element);
    }
    this.dequeue = function(){
        return items.shift();
    }
    this.front = function(){
        return items[0];
    }
    this.isEmpty = function(){
        return items.length == 0;
    }
    this.size = function(){
        return items.length;
    }
    this.clear = function(){
        items = [];
    }
    this.print = function(){
        console.log(items.toString());
    }
}	
function hotPotata(nameList, num){
    let queue = new Queue();
    // 将姓名名单 nameList 逐个加入到队列中
    for(let i = 0; i < nameList.length; i++){
        queue.enqueue(nameList[i]);
    }
    // 给定一个数字，然后迭代队伍，从队列中开头移除一项
    // 然后将其添加到队伍的末尾，模拟击鼓传花
    // 一旦传递次数达到给定的数字，拿着花的那个人就被淘汰
    let eliminated = '';
    while(queue.size() > 1){
        for(let i = 0; i < num; i++){
            queue.enqueue(queue.dequeue());
        }
        eliminated = queue.dequeue();
        console.log(eliminated+'在击鼓传花游戏中被淘汰');
    }
    return queue.dequeue();
}
let names = ['John', 'Jack', 'Camila', 'Ingrid', 'Carl'];
let winner = hotPotata(names, 7);
console.log('获胜者是'+ winner);

// Camila在击鼓传花游戏中被淘汰
// John在击鼓传花游戏中被淘汰
// Carl在击鼓传花游戏中被淘汰
// Jack在击鼓传花游戏中被淘汰
// 获胜者是Ingrid
```

下图模拟了这个输出过程：

![击鼓传花](/images/js数据结构与算法-队列-击鼓传花.png)

可以改变传入 hotPotata 函数的数字，模拟不同的场景。



### JavaScript 任务队列

当我们在浏览器中打开新标签时，就会创建一个任务队列。这是因为每个标签都是单线程处理所有的任务，它被称为 **事件循环**。浏览器要负责多个任务，如渲染 HTML ，执行  JavaScript 代码，处理用户交互（用户输入，鼠标点击等），执行和处理异步请求。



### 小结

这一章学习了队列这种数据结构。实现了自己的队列算法，学习了如何通过 enqueue 方法和 dequeue 方法添加和移除元素。还学习了两种非常著名的特殊队列的实现，优先队列和循环队列（使用击鼓传花的实现）

下一章，将学习链表，一种比数组更加复杂的数据结构。



书籍链接： [学习JavaScript数据结构与算法](https://book.douban.com/subject/26639401/)