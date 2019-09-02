---
title: 为什么我要放弃javaScript数据结构与算法（第二章）—— 数组
date: 2018-08-16 09:38:41
tags: javaScript数据结构与算法
categories: 
	- javaScript相关

---







## 第二章 数组

几乎所有的编程语言都原生支持数组类型，因为数组是最简单的内存数据结构。JavaScript里也有数组类型，虽然它的第一个版本并没有支持数组。本章将深入学习数组数据结构和它的能力。

### 为什么用数组

需求：保存所在城市每个月的平均温度，可以这么实现

```javascript
var averageTemp1 = 43.3;
var averageTemp2 = 53.2;
var averageTemp3 = 14.2;
var averageTemp4 = 42.8;
var averageTemp5 = 14.8;
var averageTemp6 = 78.9;
```

只是保存前六个月就用了6个变量，显然这种方式不适合保存这类需求。通过数组可以简单地实现我们的需求。

```javascript
var averageTemp = [];
averageTemp[0] = 43.3;
averageTemp[1] = 53.2;
averageTemp[2] = 14.2;
averageTemp[3] = 42.8;
averageTemp[4] = 14.8;
averageTemp[5] = 78.9;
```



### 创建和初始化数组

声明、创建和初始化数组的方式很简单

```
var temp = new Array(); // 使用 new 关键字，简单声明并初始化一个数组
var temp = new Array(8);  // 还可以创建一个指定长度的数组
var temp = new Array(1,2,4,9); // 直接将数组元素作为参数传递给它的构造器
```

除了用 `new`创建数组，还可以通过中括号 `[]`简单创建数组。

```javascript
var temp = [1,2,4,9];
```

#### 访问元素和迭代数组

通过在数组里指定特定位置的元素，可以获取该值或者赋值。而要知道一个数组里所有的元素，可以通过循环遍历数组。

```javascript
for(var i = 0; i < temp.length; i++){
    console.log(temp[i]); // 1,2,4,9
}
```

##### 案例：斐波那契数列

已知斐波那契数列中的第一个数字是1，第二个数字是2，从第三项开始，每一项都等于前两项之和。求斐波那契数列的前20个数

```javascript
var fibonacci = [];
fibonacci[1] = 1;
fibonacci[2] = 2;

for(var i =3; i < 20; i++){
    fibonacci[i] = fibonacci[i-1] + fibonacci[i-2];
}
console.log(fibonacci); // [ 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, 610, 987, 1597, 2584, 4181, 6765]

```



### 添加元素

```javascript
var number = [1,2,3];
number[number.length] = 4;
number // 1,2,3,4
```

上述代码可以在数组的最后一位添加元素，但其实还有更加简便的方法：

#### push

push 能添加任意个元素在数组的末尾

```javascript
number.push(5); // 5
number.push(6,7); //7
number // [1,2,3,4,5,6,7]
```

数组使用 push 会返回数组的长度

#### 插入元素到数组首位

首先我们要腾出数组的第一个元素的位置，把所有的元素向右移动一位。我们可以循环数组中的元素，从最后一位+1（长度）开始，将其对应的前一个元素的值赋给它，依次处理，最后把我们想要的值赋给第一个位置（-1）上。

```javascript
for(var i = number.length; i>=0; i--){
    number[i] = number[i-1];
}
number[0] = -0;
```

##### unshift

或者直接 使用 unshift 方法，可以将数值插入数组的首位：

```javascript
var number = [1,2,3,4];
number.unshift(-2); // 5
number.unshift(-4,-3); // 7
number // [-4, -3, -2, 1, 2, 3, 4]
```

数组使用 unshift 会返回数组的长度



### 删除元素

#### 从数组尾部删除元素

##### pop

要删除最靠后的元素可以使用 pop 方法，会删除并返回数组的最后一个元素。如果数组已经为空，则 pop() 不改变数组，并返回 undefined 值。 

```javascript
var number = [1,2,3,4];
number.pop(); //4
number // [1,2,3]
number.pop() // 3
number // [1]
```

#### 从数组首部删除元素

如果要移除数组里的第一个元素，可以使用下面的代码

```javascript
var number = [1,2,3,4];
for(var i = 0;i < number.length; i++){
    number[i] = number[i+1];
}
number // [2, 3, 4, undefined]
```

可以看出，我们将数组左移了一位，但数组的长度仍然没有变化，这意味着数组中有一个额外的元素，因为没有定义，所以是 `undefined`

##### shift

shift() 方法用于把数组的第一个元素从其中删除，并返回第一个元素的值。 数组的长度也会发生变化。如果数组是空的，那么 shift() 方法将不进行任何操作，返回 undefined 值。

### 小结

| 修改数组的方法 | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| push           | push() 方法可向数组的末尾添加一个或多个元素，并返回新的长度。 |
| unshift        | unshift() 方法可向数组的开头添加一个或更多元素，并返回新的长度。 |
| pop            | pop() 方法用于删除并返回数组的最后一个元素。  如果数组已经为空，则 pop() 不改变数组，并返回 undefined 值。 |
| shift          | shift() 方法用于把数组的第一个元素从其中删除，并返回第一个元素的值。  如果数组是空的，那么 shift() 方法将不进行任何操作，返回 undefined 值 |

push() 方法和 pop() 方法，能用数组模拟基本的栈的数据结构（先进后出）。 

shift()方法和unshift()方法，能用数组模拟基本的队列的数据结构（先进先出 ）。

### 在任意位置添加或者删除元素

已经知道如何删除数组开头和结尾的元素，那么该怎么在数组中的任意位置删除或者添加元素？

#### splice

splice() 方法向/从数组中添加/删除项目，然后返回被删除的项目。 splice() 方法可删除从 index 处开始的零个或多个元素，并且用参数列表中声明的一个或多个值来替换那些被删除的元素。 

语法

```javascript
arrayObject.splice(index,howmany,item1,.....,itemX)
```

例子

```javascript
var number = [1,2,3,4];
number.splice(2,0,4,4,5); // []
number //[1, 2, 4, 4, 5, 3, 4]
number.splice(2,5,7); // [4, 4, 5, 3, 4]
number //[1, 2, 7]
```



### 二维或者多维数组

我们知道如果要记录数天内每个小时的气温，可以使用数组来保存这些数据。那么要保存两天每小时气温的数据的时候可以这样。

```javascript
var averageTemp1 = [32,53,45,23,46,53];
var averageTemp2 = [98,32,74,34,63,73];
```

然而这不是最好的方法。可以使用矩阵（二维数组）来存储这些信息。矩阵的行保存每天的数据，列对应小时级别的数据。

```javascript
var averageTemp = [];
averageTemp[0] = [32,53,45,23,46,53];
averageTemp[1] = [98,32,74,34,63,73];
```

JavaScript只支持一维数组，并不支持矩阵。但是，可以用数组套数组来模拟矩阵或者任一多维数组。

#### 迭代二维数组的元素

如果想看到这矩阵的输出，可以创建一个通用函数，专门输出其中的值：

```javascript
function printMatrix(x){
    for(var i = 0; i < x.length; i++){
        for(var j = 0; j< x[i].length; j++){
            console.log(x[i][j]);
        }
    }
}
printMatrix(averageTemp);
```

#### 多维数组

我们也可以用这种方式来处理多维数组。假如我们要创建一个3x3x3的矩阵，每一个格子里包含矩阵的i（行）、j（列）、z（深度）之和：

```javascript
var matrix3x3x3 = [];

for(var i = 0; i < 3; i++){
	matrix3x3x3[i] = [];
    for(var j = 0; j < 3; j++){
        matrix3x3x3[i][j] = [];
        for(var z = 0; z < 3; z++){
            matrix3x3x3[i][j][z] = i+j+z;
        }
    }
}

```

数据结构中有几个维度都没有关系，都可以用循环遍历每个维度来访问所有格子

```javascript
    for(var i = 0; i < matrix3x3x3.length; i++){
        for(var j = 0; j< matrix3x3x3[i].length; j++){
            for(var z = 0; z < matrix3x3x3[i][j].length; z++){
            	console.log(matrix3x3x3[i][j][z]);
        	}
        }
    }
```

如果是一个3x3x3x3的矩阵，代码中就会用四层嵌套的 for 语句，以此类推。



### JavaScript 的数组方法参考

在JavaScript里，数组是可以修改的对象。这意味着创建的每一个数组都有一些可用的方法。

下面表格是数组的一些核心方法。

| 方法名      | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| concat      | 连接2个或者更多数组，并返回结果                              |
| every       | 对数组中的每一项运行给定函数，如果该函数对每一项都但返回true,则返回true |
| filter      | 对数组中度过每一项运行给定函数，返回该函数会返回true的项组成分数组 |
| forEach     | 对数组中更多每一项运行给定函数，这个方法没有返回值           |
| join        | 将所有的数组元素连接成一个字符串                             |
| indexOf     | 返回第一个与给定参数相等的数组元素的索引，没有找到则返回-1   |
| lastIndexOf | 返回在数组中搜索到的与给定参数相等的元素的索引里最大的值     |
| map         | 对数组中的每一项运行给定函数，返回每次函数调用结果组成的数组 |
| reverse     | 颠倒数组中的元素的顺序，原先第一个元素现在变成了最后一个，同样原先的最后一个元素变成了现在的第一个 |
| slice       | 传入索引值，将数组里对应索引范围内的元素作为新数组返回       |
| some        | 对数组中每一项运行给定函数，如果任一项返回true,则返回true    |
| sort        | 按照字母的顺序对数组排序，支持传入指定排序方法的函数作为参数 |
| toString    | 将数组作为字符串返回                                         |
| valueOf     | 和 toString 相似，将数组作为字符串返回                       |

#### 数组合并

有多个数组，需要合并起来成为一个数组。我们可以迭代各个数组，然后把每个元素加入最终的数组。

JavaScript也有提供相对应的方法 concat()

```javascript
var a = 0;
var b = [1,2,3];
var c = [-3,-2,-1];
var s = c.concat(a,b);
s // [-3, -2, -1, 0, 1, 2, 3]
```

#### 迭代器函数

有时候，我们需要迭代数组中的元素。可以使用循环语句（前面提到的for语句等）。而其实 JavaScript 内置了许多数组可以使用的迭代方法。

对于本节的例子，我们需要函数和数组。假如有一个数组，值是从1到15，如果数组里面的元素可以被2整除（偶数），函数就要返回true,否则就返回false:

```javascript
var isEven = function(x){
	// 如果是 2的倍数，就返回 true
    return (x % 2 == 0);
}
var number = [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15];
```

##### every

every 会迭代数组中的每个元素，直到返回 false。

```javascript
number.every(isEven)
```

在这个例子中，数组number第一个元素是1，不是2的倍数，因此 isEven 函数返回false,然后 every 执行结束。

##### some

some 方法和 every 相似，不过some方法会迭代数组中的每个元素，直到函数返回true

```javascript
number.some(isEven)
```

这个例子中，数组的第二个参数是2，为2的倍数，因此返回true，迭代结束

##### forEach

如果要迭代整个数组可以用 forEach 方法，和使用 for 循环相同：

```javascript
number.forEach(function(x){
    console.log((x % 2 == 0));
});
```

##### map & filter

JavaScript还有两个会返回新数组的遍历方法。第一个是 map：

```javascript
var myMap = number.map(isEven);
myMap // [false, true, false, true, false, true, false, true, false, true, false, true, false, true, false]
```

从上面代码可以看出，myMap保存了传入 map 方法的 isEven函数运行的结果。这样就可以很容易知道一个元素是否偶数。

还有一个filter方法，它返回的新数组由使函数返回 true 的元素组成：

```
var evenNumbers = number.filter(isEven);
evenNumbers // [2, 4, 6, 8, 10, 12, 14]
```

##### reduce

reduce() 方法接收一个函数作为累加器，数组中的每个值（从左到右）开始缩减，最终计算为一个值。 

语法

```javascript
array.reduce(function(total, currentValue, currentIndex, arr), initialValue)
```

| 参数           | 描述                                     |
| -------------- | ---------------------------------------- |
| *total*        | 必需。*初始值*, 或者计算结束后的返回值。 |
| *currentValue* | 必需。当前元素                           |
| *currentIndex* | 可选。当前元素的索引                     |
| *arr*          | 可选。当前元素所属的数组对象。           |

如果要对一个数组中所有元素进行求和，这就很有用

```javascript
number.reduce(function(total,currentValue,index){
    return total + currentValue;
});
// 120
```



### ES6 和数组的新功能

下表是ES6/7新增的数组方法

| 方法       | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| @@iterator | 返回一个包含数组键值对的迭代器对象，可以通过同步调用得到数组元素的键值对 |
| copyWithin | 复制数组中一系列元素到同一数组指定的起始位置                 |
| entries    | 返回包含数组所有键值对的@@iterator                           |
| includes   | 如果数组中存在某个元素则返回 true,否则返回false,ES7新增      |
| find       | 根据回调函数给定的条件从数组中查找元素，如果找到就返回该元素 |
| findIndex  | 根据回调函数给定的条件从数组中寻找元素，如果找到就返回该元素在数组中的索引 |
| fill       | 用静态值填充数组                                             |
| from       | 根据已有数组创建一个新数组                                   |
| keys       | 返回包含数组所有索引的@@iterator                             |
| of         | 根据传入的参数创建一个新数组                                 |
| values     | 返回包含数组中所有值的@@iterator                             |

除了这些新的方法，还有一种用 for... of循环来迭代数组的新做法，以及可以从数组实例得到的迭代器对象。

#### 使用 forEach 和箭头函数

箭头函数可以简化使用 forEach迭代数组元素的做法

```javascript
number.forEach(function(x){
    console.log (x % 2 == 0);
})
// 等于
number.forEach(x => {
    console.log(x % 2 == 0);
});
```

#### 使用 for...of 循环迭代

```javascript
for(let n of number){
    console.log(n % 2 == 0);
}
```

#### 使用ES6新的迭代器（@@iterator）

ES6还为 Array 类增加了一个 @@iterator 属性，需要通过 Symbol.iterator来访问。

```javascript
let iterator = number[Symbol.iterator]();
console.log(iterator.next().value); // 1
console.log(iterator.next().value); // 2
console.log(iterator.next().value); // 3
console.log(iterator.next().value); // 4
console.log(iterator.next().value); // 5
console.log(iterator.next().value); // 6
```

因为number数组中有15个值，所以需要调用15次 iterator.next().value ，数组中所有值都迭代完之后，就会返回 undefined。

- 数组的 entries、keys 和 values 方法

  ES6还增加了三种从数组中得到迭代器的方法。

  entries 方法返回包含键值对的 @@iterator

  ```javascript
  let aEntries = number.entries(); // 得到键值对的迭代器
  console.log(aEntries.next().value); // [0,1] -- 位置0的值为1
  console.log(aEntries.next().value); // [1,2] -- 位置1的值为2
  console.log(aEntries.next().value); // [2,3] -- 位置2的值为3
  ```

  number 数组中都是数字，key是数组中的位置，value是保存在数组中索引的值

  使用集合、字段、散列表等数据结构时，能够取出键值对是很有用的。后面会详细讲解。

  key 方法返回包含数组索引的 @@iterator

  ```javascript
  let aKeys  = number.entries(); // 得到数组索引的迭代器
  console.log(aKeys.next()); // { value： 0， done: false}
  console.log(aKeys.next()); // { value： 1， done: false}
  console.log(aKeys.next()); // { value： 2， done: false}
  ```

  keys方法会返回number数组的索引。一旦没有可以迭代的值，aKeys.next() 就会返回一个value属性为undefined，done属性为 true的对象。如果 done属性为false，就意味着还有可以迭代的值。

#### 使用from方法

Array.from方法根据已有的数组创建一个新数。比如复制number数组：

```javascript
let number2 = Array.from(number);
number2 // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15]
```

#### 使用Array.of

Array根据传入的参数创建一个新数组、

```javascript
let number3 = Array.of(1);
let number4 = Array.of(1,2,3,4,5,6);
number3 // [1]
number4 // [1,2,3,4,5,6]
// 复制已有的数组
let numberCopy = Array.of(...number4);
numberCopy // [1,2,3,4,5,6]
```

#### 使用fill方法

fill方法用静态值充填数组。

```javascript
let numberCopy = Array.of(1,2,3,4,5,6);
numberCopy.fill(0); // [0, 0, 0, 0, 0, 0]
// 指定开始充填的索引
numberCopy.fill(2,1); // [0, 2, 2, 2, 2, 2]
// 指定结束的索引(结束的索引不包括本身)
numberCopy.fill(1,3,5); // [0, 2, 2, 1, 1, 2] 
```

创建数组并初始化的时候，fill 方法就非常好用。

```
let ones = Array(6).fill(1); // 创建了一个长度为6，所有值都是1的数组
```

#### 使用copyWithin方法

copyWithin方法复制数组中的一系列元素到同一个数组指定的起始位置。

语法：

```javascript
array.copyWithin(target, start, end)
```

| 参数   | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| target | 必需。复制到指定目标索引位置。                               |
| start  | 可选。元素复制的起始位置。                                   |
| end    | 可选。停止复制的索引位置 (默认为 *array*.length)。如果为负值，表示倒数。 |

```javascript
let copyArray = [1,2,3,4,5,6];
copyArray.copyWithin(0,3); // [4, 5, 6, 4, 5, 6]

let copyArray1 = [1,2,3,4,5,6];
copyArray1.copyWithin(1,3,5); // [1, 4, 5, 4, 5, 6]

```



### 排序元素

反序输出最开始的长度为15的number数组。

```javascript
number.reverse();
// [15, 14, 13, 12, 11, 10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
```

尝试使用JavaScript自带的排序函数 sort();

```javascript
number.sort();
//[1, 10, 11, 12, 13, 14, 15, 2, 3, 4, 5, 6, 7, 8, 9]
```

跟我们预期的有些不一样，这是因为 sort 方法在对数组进行排序 的时候，把元素默认成字符串进行相互比较。所以我们要自己定义一个比较函数。

```javascript
number.sort((a,b) =>{
    return a -b;
})
// [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15]
```

上述代码，如果 b 大于 a，会返回负数，反之就是正数。如果相等的话，就会返回0。下面的写法会清晰一点

```javascript
function compare(a, b){
    if(a < b){
        return -1;
    }
    if(a > b){
        return 1;
    }
    return 0;
}
number.sort(compare);
```

sort 方法接受 compareFunction作为参数来比较元素。然后sort 会用它来排序数组

#### 自定义排序

我们可以对任何对象类型的数组排序，也可以创建 compareFuntion 来比较元素。例如对象 Person 有名字和属性，我们希望根据年龄排序。

```javascript
var friends = [
    {name: 'John', age: 30},
    {name: 'Ana', age: 20},
    {name: 'Chris', age: 25}
];

friends.sort((a, b) =>{
    return a.age - b.age;
})
// {name: "Ana", age: 20}
   {name: "Chris", age: 25}
   {name: "John", age: 30}
```

字符串排序

```javascript
var names = ['Ana', 'ana', 'John', 'john'];
names.sort();
// ["Ana", "John", "ana", "john"] 
```

字符串的比较是根据对应的 ASCⅡ值来比较的。例如 A、J、a、j对应分别是65、74、97、106。

虽然字母表的 a 是排靠前的，但是由于 ASCⅡ值 比较大，所以没有排在首位。如果忽略大小写呢？会是怎么样

```javascript
names.sort((a, b) =>{
    if(a.toLowerCase() < b.toLowerCase()){
        return -1;
    }
    if(a.toLowerCase() > b.toLowerCase()){
        return 1;
    }
    return 0; 
})
// ["Ana", "ana", "John", "john"]
```



### 搜索

搜索有两个方法：indexOf方法返回与参数匹配的第一个元素的索引，lastIndexOf返回与参数匹配的最后一个元素的索引。

```javascript
number.indexOf(10); // 9
number.indexOf(100); // -1 （因为100不存在数组里面）

number.puhs(10);
number.lastIndexOf(10); // 15
number.lastIndexOf(100) // -1
```

#### ES6 find 和 findIndex方法

```javascript
let number =  [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15];
const multipleof13 = (element, index, array) => {
    return (element % 13 == 0);
}
number.find(multipleof13); //13
number.findIndex(multipleof13); // 12
```

find 和 findIndex方法接受一个回调函数，搜索一个满足回调函数条件的值。上面的例子中，我们要从数组里找有个13的倍数。

#### ES7 使用includes方法

如果数组存在某个元素，includes  方法就会返回 true,否则就返回 false。

```javascript
number.includes(15) // true
number.includes(20) // false

number.includes(4,4) // false 第二个参数为开始搜索的索引
```



### 输出字符串

toString 和 jion 方法

如果想把数组里所有元素输出位一个字符串，可以使用 toString 方法

```javascript
number.toString(); // "1,2,3,4,5,6,7,8,9,10,11,12,13,14,15"
```

还可以用不同的分隔符将元素隔开

```
number.join('-'); // "1-2-3-4-5-6-7-8-9-10-11-12-13-14-15"
```



### 类型数组

JavaScript由于不是强类型的，因此可以存储任意类型的数据，而类型数组则用于存储单一的数据。

语法：

```
let myArray = new TypedArray(length);
```

| 类型数组          | 数据类型           |
| ----------------- | ------------------ |
| Int8Array         | 8位二进制补码整数  |
| Unit8Array        | 8位无符号整数      |
| Unit8ClampedArray | 8位无符号整数      |
| Int16Array        | 16位二进制补码整数 |
| Unit16Array       | 16位无符号整数     |
| Int32Array        | 32位二进制补码整数 |
| Unit32Array       | 32位无符号整数     |
| Float32Array      | 32位IEEE浮点数     |
| Float64Array      | 64位IEEE浮点数     |

```javascript
let length = 5;
let int16 = new Int16Array(length);

let array16 = [];
array16.length = length;

for(let i = 0;i < length; i++){
    int16[i] = i + 1;
}
console.log(int16); // [1, 2, 3, 4, 5]
```

使用 webGl API、进行位操作、处理文件和图像时候，类型数组都可以大展拳脚。它用起来和普通数组也毫无二致，本节所学的数组方法和功能都可以用于类型数组。



### 小结

学习了常见的数据结构：数组。学习了怎么声明和初始化数组，给数组赋值后，以及添加和移除数组元素，学了多维数组和数组的一些操作方法。

下一章，学习栈，一种具有特殊行为的数组。

书籍链接： [学习JavaScript数据结构与算法](https://book.douban.com/subject/26639401/)