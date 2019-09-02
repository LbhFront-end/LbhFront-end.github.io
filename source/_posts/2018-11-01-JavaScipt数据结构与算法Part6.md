---
title: 为什么我要放弃javaScript数据结构与算法（第六章）—— 集合
date: 2018-11-01 14:13:41
tags: javaScript数据结构与算法
categories: 
	- javaScript相关

---



前面已经学习了数组（列表）、栈、队列和链表等顺序数据结构。这一章，我们要学习集合，这是一种不允许值重复的顺序数据结构。

本章可以学习到，如何添加和移除值，如何搜索值是否存在，也可以学习如何进行并集、交集、差集等数学操作，还可以学到如何使用ES6 原生的 Set类




## 第六章 集合

### 构建数据集合

集合是由一组无序且唯一（即不重复）的项组成的。这个数据结构使用了与有限集合相同的属性概念，但应用在计算机科学的数据结构中。

在数学中，集合时一组不同的对象（的集）。

比如说，一个由大于或等于0 的整数组成的自然数集合： `N：{0,1,2,3,4,5,6，...}`。在集合中的对象列表用 `{}`（大括号）包围

还有一个概念叫做空集。空集就是不包含任何元素的集合。

可以把集合想象成一个既没有重复元素，也没有顺序概念的数组。

在数学中，集合也有并集、交集、差集等基本操作。

### 创建集合

下面Set类的骨架

```javascript
function Set(){
    let items = {};
}
```

我们使用对象而不是数组来表示集合（items）,但也可以用数组实现。这里我们用对象来实现，同时，JavaScript 对象不允许一个键指向两个不同的属性，也保证了集合里的元素都是唯一的。

接下来，需要声明一些结合可用的方法

- add(value)：向集合添加一个新的项
- remove(value)：从集合移除一个值
- has(value)：如果值在集合中，返回 true,否则返回 false
- clear()：移除集合中的所有项
- size()：返回集合所包含元素的值，与数组的 length 属性类似
- values()：返回一个包含集合中所有值的数组

### has(value)方法

首先要实现的就是 has(value)方法，这是因为它会被 add、remove 等方法调用。

```javascript
this.has = function(value){
    return value in items;
}
```

既然我们使用对象来存储集合的值，就可以使用 JavaScript 的 `in` 操作符来验证给定的值是否是 items 对象的属性。

但这个方法还有一个更好的实现方式

```javascript
this.has = function(value){
    return items.hasOwnProperty(value);
}
```

所有的 JavaScript 对象都有 `hasOwnProperty`方法，这个方法返回一个表明对象是否具有特定属性的布尔值。

**`in` 和 `hasOwnProperty` 的区别**

`in`判断的是对象的所有属性，包括对象实例及其原型的属性。而`hasOwnProperty`则是判断对象实例的是否具有某个属性。

这也是为什么 `for in` 循环的时候会建议使用 `hasOwnProperty `进行过滤，在使用了 `eslint` 才不会报警告,同时也是为了可以有效避免扩展本地原型而引起的错误。

```javascript
for (var i in foo) { 
    if (foo.hasOwnProperty(i)) { 
    console.log(i); 
}
```

### add 方法

```javascript
this.add = function(value){
    if(!this.has(value)){
        items[value] = value
        return true
    }
    return false;
}
```

对于给定的 `value`，可以检测是否存在于集合中。如果不存在，就把 value 添加到结合中，返回 true,表示添加了这个值。如果集合中，已经有了，就返回 false,表示没有添加它。

### remove 和 clear 方法

remove 方法

```javascript
this.remove = function(value){
    if(this.has(value)){
        delete items[value];
        return true;
    }
    return false;
}
```

因为使用对象来存储集合的 items 对象，可以简单使用 delete 操作符从 items 对象中移除属性

clear 方法

```javascript
this.clear = function(){
    items = {};
}
```

重置 items 对象，需要做的只是把一个空对象重新赋值给它，我们也可以迭代集合，用 remove 方法依次移除所有的值，但既然有更简单的方法，那样做就太麻烦了。

### size 方法

返回集合中有多少项，有三种实现方法

第一种是像之前一样，使用 length 变量，每当移除或者添加的时候控制它。

第二种是使用 JavsScript 内建的 Object 类的一个内建函数

```javascript
this.size = function(){
    return Object.keys(items).length
}
```

JavaScript  的 Object 类有一个 keys 方法，它返回一个包含给定对象所有属性的数组。在这种情况下，可以使用这个数组的 length 属性来返回 items 对象的属性个数。

第三种方法是手动提取 items 对象的每一个属性，记录属性的个数并返回这个数字。

```javascript
this.size = function(){
    let count = 0;
    for(let key in items){
        if(items.hasOwnProperty(key)){
           ++count;
        }
    }
    return count;
}
```

遍历 items 对象的所有属性，检查它们是否是对象自身的属性（避免重复计数），如果是，就递增 count 变量的值，最后在方法结束时返回这个数字。

### values 方法

values 方法也应用了相同的逻辑，提取 items 对象的所有属性，以数组的形式返回

```javascript
this.values = function(){
    let values = [];
    for(let i=0 , keys=Object.keys(items); i<keys.length; i++){
        values.push(items[keys[i]]);
    }
    return values;
}
```

还有兼容旧浏览器的写法

```javascript
this.values = function(){
    let values = [];
    for(let key in items){
        if(items.hasOwnProperty(key)){
           values.push(items[key])
        }
    }
    return values;
}
```

### Set类全部代码

```javascript
function Set(){
    let items = {};
    this.has = function(value){
        return items.hasOwnProperty(value);
    }
    this.add = function(value){
        if(!this.has(value)){
            items[value] = value
            return true
        }
        return false;
    }
    this.remove = function(value){
        if(this.has(value)){
            delete items[value];
            return true;
        }
        return false;
    }
    this.clear = function(){
        items = {};
    }

    this.size = function(){
        let count = 0;
        console.log(items);
        for(let key in items){
            if(items.hasOwnProperty(key)){
                ++count;
            }
        }
        return count;
    }
    this.values = function(){
        let values = [];
        for(let i=0 , keys=Object.keys(items); i<keys.length; i++){
            values.push(items[keys[i]]);
        }
        return values;
    }
}
```

### 使用 Set类

现在数据结构已经完成了 。测试一下

```javascript
let set = new Set(); // 新建 Set类 实例
set.add(1); 
set.add(2);
set.add(3);
set.add('j');
console.log(set.has(2)); // true
console.log(set.size()); // 4
console.log(set.value()); // [1, 2, 3, "j"]
set.remove(2);
console.log(set.has(2)); // false	
console.log(set.size()); // 3
console.log(set.value()); //  [1, 3, "j"]
set.clear();
console.log(set.size()); // 0
console.log(set.value());	// []
```

### 集合操作

对集合可以进行以下操作

- 并集：对于给定的两个集合，返回一个包含两个集合中所有元素的新集合
- 交集：对于给定的两个集合，返回一个包含两个集合中共有元素的新集合
- 差集：对于给定的两个集合，返回一个所有存在于第一个集合且不存在于第二个集合的元素的新集合
- 字集：验证一个给定的集合是否是另一个的子集。

### 并集

```javascript
// 并集
this.union = function(otherSet){
    let unionSet = new Set();
    let values = this.values();
    for(let i = 0; i < values.length; i++){
        unionSet.add(values[i]);
    }
    values = otherSet.values();
    for(let i = 0; i < values.length; i++){
        unionSet.add(values[i]);
    }
    return unionSet;
}
```

首先需要创建一个新的集合，代表两个集合的并集，获取第一个集合（当前Set类实例）所有值（values），遍历并添加到代表并集的集合中。然后对第二个集合做同样的事情，最后返回结果。

测试上面的代码

```javascript
let setA = new Set();
setA.add(1);	
setA.add(2);	
setA.add(3);	
let setB = new Set();
setB.add(3);
setB.add(4);
setB.add(5);
setB.add(6);
console.log(setA.values()); // [1, 2, 3]
console.log(setB.values()); // [3, 4, 5, 6]
let unionAB = setA.union(setB);
console.log(unionAB.values()); // [1, 2, 3, 4, 5, 6]
```

可以注意到元素 3同时存在于A和B中，但是集合里只输出了一次

### 交集

```javascript
// 交集
this.intersection = function(otherSet){
    let intersectionSet = new Set();
    let values = this.values();
    for(let i = 0;i <values.length; i++){
        if(otherSet.has(values[i])){
            intersectionSet.add(values[i]);
        }
    }
    return intersectionSet;
}
```

验证

```javascript
let intersectionSetAB = setA.intersection(setB);
console.log(intersectionSetAB.values()); // [3]
```

### 差集

```javascript
// 差集
this.difference = function(otherSet){
    let differenceSet = new Set();
    let values = this.values();
    for(let i = 0; i < values.length; i++){
        if(!otherSet.has(values[i])){
            differenceSet.add(values[i]);
        }
    }
    return differenceSet;
}
```

验证

```javascript
let differenceSetAB = setA.difference(setB);
console.log(differenceSetAB.values()); // [1, 2]
```

differenceSetAB 会得到所有存在于集合A 但不存在与集合B的值。



### 子集

```javascript
// 子集
this.subset = function(otherSet){
    if(this.size() > otherSet.size()){
        return false
    }else{
        let values = this.values();
        for(let i = 0; i < values.length; i++){
            if(!otherSet.has(values[i])){
                return false;
            }
        }
        return true;				
    }
}
```

首要要验证当前Set实例的大小，如果当前实例中的元素比 otherSet 实例更多，它就不是一个子集。子集的元素个数需要小于或等于要比较的集合。

接下来遍历所有集合中的元素，验证这些元素也存在于 otherSet 中，如果有任何元素不存在与 otherSet 中的，这意味着它不是一个子集，返回 false。如果所有元素都存在于 otherSet 中，就返回 true。

验证

```javascript
let subSetAB = setA.subset(setB);
console.log(subSetAB); // false 

setB.add(1);	
setB.add(2);	
setB.add(3);	
console.log(subSetAB); // true 
```

### ES6——Set类

ES6新增了 Set 类，我们可以基于 ES6 的 Set 开发我们的 Set 类

```javascript
let set = new Set();
set.add(1);
console.log(set.values()); // SetIterator {1}
console.log(set.has(1)); // true
console.log(set.size); // 1
```

ES6的values 方法会返回 Iterator，而不是值构成的数组，需要通过 next() 方法来遍历获取值，另外size 是一个属性不是一个方法。

可以用 delete 方法删除 set 中的元素

```javascript
set.delete(1); // true
```

clear 方法会重置 set 数据结构，跟我们实现的功能一样。



### ES6 Set类的操作

我们的Set类实现了交集、差集、并集、子集等数学操作，然后ES6 原生的Set 并没有这些功能，不过我们可以尝试模拟。

例子

```javascript
let setA = new Set();
setA.add(1);
setA.add(2);
setA.add(3);
let setB = new Set();
setB.add(2);
setB.add(3);
setB.add(4);
```

### 模拟并集操作

模拟交集操作需要创建一个辅助函数，来生成包含 setA 和 setB 都有的元素的新集合。

```javascript
let unionAB = new Set();

for(let x of setA) unionAB.add(x);
for(let x of setB) unionAB.add(x);
console.log(unionAB); // Set(4) {1, 2, 3, 4}
```

### 模拟交集操作

```javascript
let intersection = function(SetA,SetB){
    let intersectionSet = new Set();
    for(let x of SetA){
        if(SetB.has(x)){
            intersectionSet.add(x);
        }
    }
    return intersectionSet;
}
let intersectionSetAB = intersection(setA,setB);
console.log(intersectionSetAB); // Set(2) {2, 3}
```

也可以用简单的语法实现

```javascript
let intersectionSetAB = new Set([x for (x of setA) if (setB.has(x))])
```

但是简化的函数，目前只有在火狐才可以运行。

### 模拟差集操作

```javascript
let difference = function(SetA,SetB){
    let differenceSet = new Set();
    for(let x of SetA){
        if(!SetB.has(x)){
            differenceSet.add(x);
        }
    }
    return differenceSet;
}
let differenceSetAB = difference(setA,setB);
console.log(differenceSetAB); // Set(1) {1}
```



### 小结

学习了从头实现一个ES6中定义的类似的Set 类，还实现了一些再其他语言的集合数据结构中不常见的一些方法，例如并集、交集、差集和子集。

后面，将会介绍散列和字典这两种非顺序数据结构。




书籍链接： [学习JavaScript数据结构与算法](https://book.douban.com/subject/26639401/)