---
title: 为什么我要放弃javaScript数据结构与算法（第七章）—— 字典和散列表
date: 2018-11-02 16:13:41
tags: javaScript数据结构与算法
categories: 
	- javaScript相关

---



本章学习使用字典和散列表来存储唯一值（不重复的值）的数据结构。
集合、字典和散列表可以存储不重复的值。在集合中，我们感兴趣的是每个值本身，并把它作为主要元素。而字典和散列表中都是用 `[键，值]`的形式来存储数据。但是两个数据结构的实现方式略有不同。




## 第七章 字典和散列表

### 字典

集合表示一组互不相同的元素（不重复的元素）。在字典里，存储的是 `[键，值]` 对，其中键名是用来查询特定元素的。字典和结合很相似，集合以 `[值，值]` 的形式存储元素，字典则是以 `[键，值]`的形式来存储元素。字典也成为映射。

### 创建字典

与 Set 类相似，ES6 中也包含了一个 Map 类的实现，即我们所说的字典。

我们在本章中，将要实现的是以ES6 中 Map 类的实现为基础的。你会发现它跟 Set 类很相似（但不同与存储 `[值，值]`对的形式，我们将要存储的是 `[键，值]`对。）

下面是我们的 Dictionary 类的骨架：

```javascript
function Dictionary(){
    let items = {};
}
```

然后我们要声明一些 映射/ 字典 所能使用的方法。

- set（key,value）：想字典中添加新的元素。
- delete（key）：通过使用键值来从字典中移除键值对应的数据值
- has（key）：如果某个键值存在于这个字典，则返回true,没有则返回 false
- get（key）：通过键值查找特定的数值并返回
- clear（）：键这个字典中的所有元素全部删除
- size（）：返回字典所包含元素的数量。与数组的 length 相似
- keys（）：将字典所包含的所有键名以数组形式返回
- values（）：将字典所包含的所有数值以数组形式返回。



### has 和 set 方法

首先要实现 has 这个方法，因为它会被 set 和 remove 其他方法调用。我么可以通过以下代码实现

```javascript
this.has = function(key){
    return items.hasOwnProperty(key);
}
```

这个方法 的实现和我们之前在 Set 类中的实现是一样的。我们使用 JavaScript 中的 in 操作符来验证一个 key 是否是 items 对象的一个属性。

然后就是 set 方法的实现

```javascript
this.set = function(key,value){
    items[key] = value;
}
```

该方法接受一个 key 和 一个 value 作为参数。我们直接将 value 设为 items 对象的 key 属性的值。它可以用来给字典添加一个新的值，或者用来更新一个已有的值。



### delete方法

它和 Set 类的 delete 方法很类似，唯一不同的是，我们将先搜索 key (而不是 value)

```javascript
this.delete = function(key){
    if(this.has(key)){
        delete items[key];
        return true;
    }
    return false;
}
```

我们可以使用 JavaScript 中的 delete 操作符来删除 items 对象中的 key 属性。



### get 和 values 方法

如果我们想在字典里查找一个特定的项，并检索它的值，可以通过以下方法

```javascript
this.get = function(key){
    return this.has(key) ? items[key] : undefined;
}
```

get 方法会首先通过has 方法验证是否有 key 值存在，存在就返回该值，没有就返回一个 undefined 。

下面是 values 方法，以数组的形式返回 字典中所有的 values 的值。

```javascript
this.values = function(){
    const values = [];
    for(let k i items){
        if(this.has(k)){
            values.push(items[k]);
        }
    }
    return values;
}
```

### clear、size、keys 和 getItems 方法

clear 和 size 方法和 集合那章的实现是完全一模一样的。

```javascript
this.clear = function(){
    this.items = {};
}
this.size = function(){
    let count = 0;
    if(let k in items){
        if(this.has(k)){
            ++count;
        }
    }
    return count;
}
```

keys 方法返回所有用于标识值的键名，可以使用 Object 类的 keys 方法

```javascript
this.keys = function(){
    return Object.keys(items);
}
```

最后，验证 items 属性的输出值，我们加多一个返回 items 变量的方法，叫做 getItems

```javascript
this.getItem = function(){
    return items;
}
```

### 使用 Dictionary 类

```javascript
const dictionary = new Dictionary();
dictionary.set('mail','544289495@qq.com');
dictionary.set('website','http://laibh.top');
dictionary.set('name','lbh');
dictionary.set('word','1414');
console.log(dictionary.getItem()); // {mail: "544289495@qq.com", website: "http://laibh.top", name: "lbh", word: "1414"}
console.log(dictionary.get('word')); // 1414
console.log(dictionary.size()); // 4
console.log(dictionary.keys()); // ["mail", "website", "name", "word"]
console.log(dictionary.values()); // ["544289495@qq.com", "http://laibh.top", "lbh", "1414"]

dictionary.delete('word');

console.log(dictionary.getItem()); // {mail: "544289495@qq.com", website: "http://laibh.top", name: "lbh"}
console.log(dictionary.get('word')); // undefined	
console.log(dictionary.size()); // 3
console.log(dictionary.keys());	// ["mail", "website", "name"]
console.log(dictionary.values()); // ["544289495@qq.com", "http://laibh.top", "lbh"]
```



### 散列表

本节中，你将会学到 HashTable 类，也叫 HashMap 类，它是 Dictionary 类的一种散列表实现方式。

**散列算法**的作用是尽可能快地在数据结构中找到一个值。之前的章节里面你已经知道要在数据结构中获得一个值（使用 get 方法），需要遍历整个数据结构来找到它。如果使用散列函数，就知道值的具体位置，因此能够快速检索到该值。散列函数的作用是给定一个键值，然后返回值在表中的地址。



### 创建散列表

我们将使用数组来表示我们的数据结构，与之前一样，从搭建类的骨架开始

```javascript
function HashTable(){
    let table = [];
}
```

然后，给类加一些方法。我们给每个类实现三个基本方法.

- put（key,value）：向散列表中增加一个新的项（也能更新散列表）
- remove（key）：根据键值从散列表中移除值
- get（key）：返回根据键值检索到的特定的值。

在实现三个方法之前，要实现的第一个方法就是散列函数，它是 HashTable 类中的一个私有方法。

```javascript
var loseloseHashCode = function(key){
    var hash = 0;
    for(var i = 0; i < key.length; i++){
        hash += key.charCodeAt(i);
    }
    return hash % 37;
}
```

给定一个 key 参数，我们就能根据组成 key 的每个字符的 ASCII码值的和的得到一个数字。所以，首先需要一个变量来存储这个总和。然后，遍历 key 并将从 ASCII 表中查到的每个字符对应的 ASCII 值加到 hash 变量中（可以使用String 类的 charCodeAt方法）。最后，返回 hash 值。为了得到较小的值，我们会使用 hash 值和一个任意的数做除法的余数。

现在有了散列函数，基于可以实现 put 方法

```javascript
this.put = function(key,value){
    var position  = loseloseHashCode(key);
    console.log(position + '-' + key);
    table[position] = value;
}
```

首先，根据给定的 key ，我们需要根据所创建的散列函数计算出它在表中的位置。为了方便展示消息，我们需要计算出的位置输出至控制台。由于它不是必需的，我们也可以选择省略。然后要做的就是，将 value 参数添加到 用散列函数计算出的对应的位置上。

从 HashTable 实例中查找一个值也很简单。为此我们会实现一个 get 方法

```javascript
this.get = function(key){
    return table[loseloseHashCode(key)];
}
```

首先我们会使用所创建的散列函数来求出所对应的位置。这个函数会返回值的位置，因为我们需要做的就是根据这个位置来从数组 table 中获得这个值。

实现的最后一个方法是 remove 方法

```javascript
this.remove = function(key){
    table[loseloseHashCode(key)] = undefined;
}
```

要从 HashTable 实例中移除一个元素，只需要求出元素的位置并赋值为 undefined。

对于 HashTable 类来说，我们不需要像链表一样从 table 数组中将位置也移除。由于元素分布整个数组范围内，一些位置会没有任何元素占据，并默认为 undefined值。我们也不能将位置本身刚从数组中移除（这样会改变元素的位置），否则，当下次需要获得或者是移除一个元素的时候，这个元素会不在我们用散列函数求出的位置上。

### 使用 HashTable 类

测试 HashTable 类

```javascript
const hashTable = new HashTable();

hashTable.put('mail','544289495@qq.com'); // 12-mail
hashTable.put('website','http://laibh.top'); // 15-website
hashTable.put('name','lbh'); // 10-name
hashTable.put('word','1414'); // 0-word

console.log(hashTable.get('website')); // http://laibh.top
console.log(hashTable.get('word')); // 1414


hashTable.remove('word');


console.log(hashTable.get('website')); // http://laibh.top	
console.log(hashTable.get('word'));	// undefined
```

### 散列表和散列结合

散列表和散列映射是一样的，在一些编程语言中，还有一种叫做散列集合的实现。散列集合由一个集合构成，但是插入、移除或获取元素时，使用的还是散列函数。我们可以重用本章中实现的所有代码来实现散列集合，不同之处在于，不再添加键值对，而是只插入而没有键。例如，可以使用散列集合来存储所有的英文单词（不包括它的定义）。和集合相似，散列集合值存储唯一的不重复的值。

### 处理列表中更多冲突

有时候，一些表会有相同的散列值。不同的值在散列表中对应相同的位置的时候，我们称其为冲突。

```javascript
hashTable.put('Gandalf','Gandalf@qq.com'); 
hashTable.put('John','John@qq.com');
hashTable.put('Tyrion','Tyrion@qq.com');
hashTable.put('Aaron','Aaron@qq.com');
hashTable.put('Donnie','Donnie@qq.com');
hashTable.put('Ana','Ana@qq.com');
hashTable.put('Jonathan','Jonathan@qq.com');
hashTable.put('Jamie','Jamie@qq.com');
hashTable.put('Sue','Sue@qq.com');
hashTable.put('Mindy','Mindy@qq.com');
hashTable.put('Paul','Paul@qq.com');
hashTable.put('Nathan','Nathan@qq.com');
```

输出结果如下

```javascript
19-Gandalf
29-John
16-Tyrion
16-Aaron
13-Donnie
13-Ana
5-Jonathan
5-Jamie
5-Sue
32-Mindy
32-Paul
10-Nathan
```

可以看出来，上面有很多相同的散列值。

为了获得结果，我们来实现一个print 的辅助函数。

```javascript
this.print = function(){
    for(let i = 0; i < table.length; ++i){
        if(table[i] !== undefined){
            console.log(i + ':' + table[i]);
        }
    }
}
```

运行 print 函数,得到以下输出

```javascript
5:Sue@qq.com
10:Nathan@qq.com
13:Ana@qq.com
16:Aaron@qq.com
19:Gandalf@qq.com
29:John@qq.com
32:Paul@qq.com
```

可以看出来，按照先后的顺序，有相同 散列值的被后面的覆盖了。

使用一个数据结构来保存数据的目的显然不是去丢失这些数据，而是通过某个方法将它们全部保存起来。因此，当这种情况发生的时候，处理冲突的方法有几种：分离链接、线性探查、双散列法。

这里介绍第一种方法

### 分离链接

分离链接法包括为散列表的每一个位置创建一个链表并将元素存储在里面。它是解决冲突的最简单的方法，但是它在 HashTable 实例之外还需要额外的存储空间。

例如：我们在之前的测试代码中使用分离链接的话，输出结果会是这样的：

![散列表-分离链接](/images/js数据结构与算法-散列表-分离链接.png)



在位置5上，将会有包含3个元素的 LinkedList 实例;在位置13、16、32上，将会有包含两个元素的 LinkedList 实例;在位置10、19和29上，将会有包含单个元素的 LinkedList 实例。

对于分离链接和线性探查来说，只需要重写三个方法：put、get和remove。这三个方法在每种技术实现中都是不同的。

为了实现一个使用分离链接的 HashTable 实例，我们需要一个新的辅助类来表示将要加入 LinkedList 实例的元素，我们管它叫 ValuePair 类（在 HashTable 类内部定义）

```javascript
var ValuePair = function(key,value){
    this.key = key;
    this.value = value;
    this.toString = function(){
        return '[' + this.key + '-' + this.value + ']';
    }
}
```

这个类只会将 key 和 value 存储在一个 Object 实例汇中，我们也重写了 toString 方法，以便于杂浏览器控制台输出结果。

### put 方法

```javascript
this.put = function(key,value){
    var position  = loseloseHashCode(key);

    if(table[position] !== undefined){
        table[position] = new LinkedList();
    }
    
    table[position].append(new ValuePair(key,value));
}
```

在这个方法中，将验证要加入新元素的位置是否已经被占据，如果这个位置是第一次被加入元素，我们会在这个位置上初始化一个 LinkedList 类的实例。然后使用 LinkedList里面的append 方法来向LinkedList 实例添加一个 ValuePair 实例（键和值）

### get 方法

```javascript
this.get = function(key){
    var position = loseloseHashCode(key);
    if(table[position] !== undefined){

        // 遍历链表来寻找键/值
        var current = table[position].getHead();
        while(current.next){
            if(current.element.key === key){
                return current.element.value;
            }
            current = current.next;
        }
        // 检查元素在链表的第一个或者是最后一个
        if(current.element.key ==== key){
            return current.element.value;
        }
    }
    return undefined;
}
```

首先要判断在特定位置上面是否有元素存在，没有则返回一个 undefined 。反之则遍历链表来寻找我们需要的元素，先获取链表表头的引用，然后就可以从链表的头部遍历到尾部

Node 链表包含 next 和 element 属性。而 element 属性又是 ValuePair 的实例，所以它又有 value 和 key 属性。可以通过 current.element.key 来获得 Node链表的 key 属性，并通过比较它来确定它是否就是我们要找的键。key 相同就返回 Node 值，不相同，就继续遍历。

如果要找的元素是链表的第一个或者是最后一个节点，就不会进入 while 循环，因此需要在后面加多一个判断。

### remove 方法

```javascript
this.remove = function(key){
    var position = loseloseHashCode(key);
    if(table[position] !== undefined){
        var current = table[position].getHead();
        while(current.next){
            if(current.element.key === key){
                table[position].remove(current.element);
                if(table[position].isEmpty()){
                    table[position] = undefined
                }
                return true;
            }
            current = current.next;	
        }

        // 检查是否为第一个或者是最后一个元素
        if(current.element.key === key){
            table[position].remove(current.element);				
            if(table[position].isEmpty()){
                table[position] = undefined
            }
            return true;
        }
    }
    return false;
}
```

在 remove方法中，我们使用和get 方法一样的步骤找到要找的元素。遍历 LinkedList 实例，如果链表中的 current 元素就是我们要找的元素，使用 remove 方法将其从链表中移除，然后进一步额外的验证，如果链表为空，就将散列表这个位置设为 undefined ,这样搜索一个元素或打印它的内容的时候，就可以跳过这个位置。最后返回 true 表示整个元素已经被移除或者在最后返回 false 表示整个元素在散列表中不存在。同样，和 get 方法一样，处理元素在第一个或者最后一个的情况。

重写了这三个方法，我们就拥有了一个分离链接法来处理冲突的HashMap 实例了。

重新运行之前的例子，会得到以下结果

```javascript
5:[Jonathan-Jonathan@qq.com]-[Jamie-Jamie@qq.com]-[Sue-Sue@qq.com]
10:[Nathan-Nathan@qq.com]
13:[Donnie-Donnie@qq.com]-[Ana-Ana@qq.com]
16:[Tyrion-Tyrion@qq.com]-[Aaron-Aaron@qq.com]
19:[Gandalf-Gandalf@qq.com]
29:[John-John@qq.com]
32:[Mindy-Mindy@qq.com]-[Paul-Paul@qq.com]
```



### 创建更好的散列函数

我们实现的 `lose lose`散列函数并不是一个表现良好的散列函数。因为它会产生太多的冲突。一个表现良好的散列函数是由几个方面构成的：插入和检索元素的时间（即性能），当然也包括较低的冲突可能性。

另一个可以实现的比 `lose lose` 更好的散列函数是 djb2

```javascript
var djb2HashCode = function(key){
    var hash = 5381;
    for(var i = 0; i < key.length; i++){
        hash = hash* 33 + key.charCodeAt(i);
    }
    return hash % 1013;
}
```

它包括初始化一个 hash 变量并赋值一个质数，然后迭代参数 key ，将 hash 与参数33相乘

，并和当前迭代到的字符的 ASCII码值相加。

最后，我们将使用相加的和于另一个随机质数相除的余数。

这并不是最好的散列函数，但是是社区最被推崇的散列函数之一。

### ES6——Map类

ES6中有Map,我们可以基于这个类来开发Dictionary 类

```javascript
const map = new Map();
map.set('Gandalf','Gandalf@qq.com');
map.set('John','John@qq.com');
map.set('Tyrion','Tyrion@qq.com');
map.set('Aaron','Aaron@qq.com');
map.set('Donnie','Donnie@qq.com');
map.set('Ana','Ana@qq.com');
map.set('Jonathan','Jonathan@qq.com');
map.set('Jamie','Jamie@qq.com');
map.set('Sue','Sue@qq.com');
map.set('Mindy','Mindy@qq.com');
map.set('Paul','Paul@qq.com');
map.set('Nathan','Nathan@qq.com');

console.log(map.has('Gandalf')); //true	
console.log(map.size); // 12
console.log(map.keys()); // MapIterator
console.log(map.values());	// MapIterator
console.log(map.get('Gandalf')); // Gandalf@qq.com
```

可以看到，values 和 keys 方法返回都是 MapIterator，而不是值或键构成的数组。另一个区别是，size 是属性。

删除 map 中的元素可以用 delete 方法

```javascript
map.delete('Gandalf');
```

clear 方法会重置 map 数据结构，这跟我们在之前的实现时一样的。

### ES6——WeakMap 类 和 WeakSet 类

除了 Set 和 Map 两种新的数据结构，ES6还增加了它们的弱化版本，WeakMap 和 WeakSet  类。

基本上，Map 和 Set  与其弱化版本之间仅有的区别是

- WeakMap 和 WeakSet  类没有 entries、keys、values 等方法。
- 只能用对象作为键

创建和使用这两个类主要是为了性能。没有强引用的键，这使得JavaScript 的垃圾回收器可以从中清除整个入口。

另外的一个优点是，必须用键才可以取出值。这些类没有 entries 、keys、values 等迭代器方法，因此，除非你知道键，否则没有办法取出值。

### 小结

本章，我们学习了字典的相关知识，了解了如何添加、移除和获取元素已经其他的一些方法，我们还了解了字典和集合的不同之处。

学习了散列运算，知道该怎么去创建一个散列表数据结构，如何创建一个散列函数。

下一章，我们将学习一种新的数据结构——树。




书籍链接： [学习JavaScript数据结构与算法](https://book.douban.com/subject/26639401/)