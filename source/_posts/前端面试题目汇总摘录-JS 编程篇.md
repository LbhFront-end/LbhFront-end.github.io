---
title: 前端面试题目汇总摘录（JS 编程篇）
date: 2018-12-18  09:30:54
tags: 前端面试题
categories: 
	- 前端面试
---



温故而知新，保持空杯心态.
[leetCode](https://leetcode-cn.com) 题目整理。

## JS 编程题

### 难度：简单

### 两数之和

给定一个整数数组 `nums` 和 一个目标值 `target`，找出和为目标值的那两个整数，并返回他们的数组下标。可以假设每种输入只会对应一个答案，但是，不能重复利用这个数组中相同的元素。

```javascript
// 最笨的解法
/**
* @param {number[]} nums
* @param {number} target
* @param {number[]}
*/
var twoNum = function(nums,target){
    for(let i=0;i<nums.length;i++){
        for(let j=0;j<nums.length;j++){
            if(i !== j && nums[i] + nums[j] === target){
                return [i,j]
            }
        }
    }
}
// 其他解法map
var twoNum = function(nums,target){
    for(let i=0;i<nums.length;i++){
        if(map.has(target-nums[i])){
            return [map.get(target-nums[i]),i]
        }
        map.set(nums[i],i)
    }
}
twoSum([3,2,4],6)
// [1,2]
```

### 两数之和-输出有序数组

给定一个已按照升序排列的有序数组，找出两个数使得它们相加之和等于目标数。

函数应该返回这两下标志 `index1` 和 `index2`，其中 `index1`必须小于 `index2`

```javascript
/**
 * @param {number[]} numbers
 * @param {number} target
 * @return {number[]}
 */
var twoNum = function(numbers,target){
    let map = new Map();
    for(let i=0;i<numbers.length;i++){
        if(map.has(target-numbers[i])){
            return [map.get(target-numbers[i]),i+1]
        }
        map.set(numbers[i],i+1)
    }
}

// 双指针
var twoNum = function(numbers,target){
    var left = 0,
        right=numbers.length-1;
    while(left < right){
        if(numbers[left]+numbers[right] === target){
            return [left+1,right+1]
        }else if(numbers[left]+numbers[right]<target){
            left++;
        }else{
            right--;
        }
    }
}
```

### 两数之和-输入 BST

给定一个二叉树和一个目标结果，如果 BST 中存在两个元素且它们的和一个目标结果，如果 BST 中存在两个元素且它们的和等于给定的目标结果，则返回 true

```javascript
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @param {number} k
 * @return {boolean}
 */
var findTarget = function(root,k){
    let array = [];
    dfs(node,array);
    let map = new Map();
    for(let i=0;i<array.length;i++){
        if(map.has(k-array[i])) return true
        map.set(array[i],i)
    }
    return false
}
function dfs(node,array){
    if(!node) return;
    dfs(node.right,array);
    array.push(node.val);
    dfs(node.left,array)
}
```

### 整数反转

给出一个32位有符号整数，需要将这个整数中每位的数字进行反转

**注意:**

假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 [−231,  231 − 1]。请根据这个假设，如果反转后整数溢出那么就返回 0。

```javascript
// 输入：123
// 输出：321
// 输入：-123
// 输出：-321
// 输入：120
// 输出：21
/**
* @param {number} x
* @return {number}
*/
var reverse = function(x){
    const MAX_VALUE = Math.pow(2,31) - 1;
    const MIN_VALUE = Math.pow(-2,31);
    let sum = 0;
    while(x){
        sum = sum * 10 + x % 10;
        x = x / 10 >> 0
    }
    if(sum < MAX_VALUE && sum > MIN_VALUE){
        return sum
    }
    return 0;
}
```

`>>0` 与 `>>>0`：前者是有符号右移，后者是无符号

移位操作符在移位前做了两种转换，第一将不是 `number` 类型的数据转换成 `number`，第二将 `number` 转换成无符号的 `32bit`数据，也就是 `Uint32`类型。这些与移位的位数无关，移位 0 位 主要就是用了 js 的内部特性做了前两种的转换。

`Unit32`类型是如何转换的：

1. 如果不能转换为 `Number`，那就是为 0
2. 如果为非整数，先转换成为整数
3. 如果是正数，就返回正数，如果是负数，就返回负数 +2 的 32次方

```javascript
function ToInteger(x){
    x = Number(x);
    return x < 0 ? Math.ceil(x) : Math.floor(x)
}
function modulo(a,b){
    return a - Math.floor(a/b) * b
}
function ToUnit32(x){
    return module(ToInteger(x),Math.pow(2,32))
}
```

### 颠倒二进制

颠倒给定的 32 位无符号整数的二进制位

```javascript
/**输入: 00000010100101000001111010011100
输出: 00111001011110000010100101000000
解释: 输入的二进制串 00000010100101000001111010011100 表示无符号整数 43261596，
      因此返回 964176192，其二进制表示形式为 00111001011110000010100101000000。
      */
/**输入：11111111111111111111111111111101
输出：10111111111111111111111111111111
解释：输入的二进制串 11111111111111111111111111111101 表示无符号整数 4294967293，
      因此返回 3221225471 其二进制表示形式为 10101111110010110010011101101001。
      */
/**
* @param {numebr} n - a positive integer
* @param {return} n - a positive integer
*/
var reverseBits = function(n){
    return Number.parseInt([...n.toString(2)].reverse().join('').padEnd(32,'0'),2)
}
```

`padEnd、padStart`

ES2017引入了字符串不全长度的功能，如果某个字符串不够指定长度，会在头部或者尾部补全。

```javascript
'x'.padStart(4,'ab'); // abax
'x'.padStart(5,'ab'); // ababx

'x'.padEnd(4,'ab'); // xaba
'x'.padEnd(5,'ab'); // xabab
// 如果原字符串的长度，等于或者大于指定的最小长度，则返回原字符串
//常见用途
// 为数值补全指定位数
'1'.padStart(10,'0'); // 000000001
'12'.padStart(10,'0'); // 000000012

// 提示字符串格式
'12'.padEnd(10,'YYYY-MM-DD') // 'YYYY-MM-DD'
'09-12'.padStart(10,'YYYY-MM-DD'); // 'YYYY-09-12'
```

### 回文数

判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数

```javascript
/**
* @param {number} x
* return {boolean}
*/
// 最笨的方法

var isPalindrome = function(x){
 	let str = x.toString();
    let len = str.length;
    let j = len - 1;
    for(let i=0;i<len;i++){
        if(str[i] !== str[j]){
            return false
        }
    }
    return true;
}

// 书写简单但是速度慢
var isPalindrome = function(x){
    return x == x.toString().split('').reverse().join('')    
}

// 不转换为字符串，用原来的数字解
var isPalindrome = function(x) {
    let original = x ; 
    let result = 0;
    while(x){
        result = result * 10 + x % 10;
        x = x /10 >> 0
    }
    return original === result
};
```



### 唯一的摩尔斯密码

际摩尔斯密码定义一种标准编码方式，将每个字母对应于一个由一系列点和短线组成的字符串， 比如: "a" 对应 ".-", "b" 对应 "-...", "c" 对应 "-.-.", 等等。

为了方便，所有26个英文字母对应摩尔斯密码表如下：

```
[".-","-...","-.-.","-..",".","..-.","--.","....","..",".---","-.-",".-..","--","-.","---",".--.","--.-",".-.","...","-","..-","...-",".--","-..-","-.--","--.."]
```

给定一个单词列表，每个单词可以写成每个字母对应摩尔斯密码的组合。例如，"cab" 可以写成 "-.-..--..."，(即 "-.-." + "-..." + ".-"字符串的结合)。我们将这样一个连接过程称作单词翻译。

返回我们可以获得所有词不同单词翻译的数量。

例如:

```javascript
// 输入: 
words = ["gin", "zen", "gig", "msg"]
//输出: 
2

// 解释: 
// 各单词翻译如下:
"gin" -> "--...-."
"zen" -> "--...-."
"gig" -> "--...--."
"msg" -> "--...--."

```



共有 2 种不同翻译, "--...-." 和 "--...--.".

**注意:**

- 单词列表words 的长度不会超过 100。
- 每个单词 words[i]的长度范围为 [1, 12]。
- 每个单词 words[i]只包含小写字母。

**解题思路**：

循环遍历获取单次列表中每个单词的 Unicode 编码，已知所有的单词都是小写字母，然后 **97~122**是26个小写字母的编码，减去 97 后可以对应密码表的索引。取出每个单词的密码相互比较，得出所有词不同单词的长度。

```javascript
const code = [".-","-...","-.-.","-..",".","..-.","--.","....","..",".---","-.-",".-..","--","-.","---",".--.","--.-",".-.","...","-","..-","...-",".--","-..-","-.--","--.."];
/**
 *@params {string[]} words
 *@return {number}
 */
 const words = ["gin", "zen", "gig", "msg"];
 var uniqueMorseRepresentations = function(words){
    const len = words.length;
    for(let i = 0;i < len;i++){
        let str = '';
        for(let j = 0; j < words[i].length; j++){
            str +=code[words[i][j].charCodeAt()-97];
        }
        words[i] = str
    }
    return [...new Set(words)].length;
 }
 uniqueMorseRepresentations(words);
```

### 宝石与石头

给定字符串`J` 代表石头中宝石的类型，和字符串 `S`代表你拥有的石头。 `S` 中每个字符代表了一种你拥有的石头的类型，你想知道你拥有的石头中有多少是宝石。

`J` 中的字母不重复，`J` 和 `S`中的所有字符都是字母。字母区分大小写，因此`"a"`和`"A"`是不同类型的石头。

**示例 1:**

```
输入: J = "aA", S = "aAAbbbb"
输出: 3
```

**示例 2:**

```
输入: J = "z", S = "ZZ"
输出: 0
```

**注意:**

- `S` 和 `J` 最多含有50个字母。
- `J` 中的字符不重复。

**解题思路**：

遍历获取石头 `S`中每个字母，用  match函数对  `J` 进行匹配，匹配成功计数加1。

**match:**

```javascript
/**
	searchvalue 必需。规定要检索的字符串值。
	regexp  必需。规定要匹配的模式的 RegExp 对象。如果该参数不是 RegExp 对象，则需要首先把它传递给 RegExp 构造函数，将其转换为 RegExp 对象。
	返回值 存放匹配结果的数组。该数组的内容依赖于 regexp 是否具有全局标志 g。
*/
stringObject.match(searchvalue)
stringObject.match(regexp)
```



```javascript
/**
* @param {string} J
* @param {string} K
* @return {number} 
*/
var J = "aA", S = "aAAbbbb";

var numJewelsInStones = function(J,S){
    let count = 0;
    for(let i = 0; i < S.length; i++){
        if(J.match(S.charAt(i)){
            count++;
        };
    }
    return count;
}
numJewelsInStones(J,S);
```

### 按奇偶排序数组

给定一个非负整数数组 A，返回一个由 A 的所有偶数元素组成的数组，后面跟 A 的所有奇数元素。

你可以返回满足此条件的任何数组作为答案。

示例：

```
输入：[3,1,2,4]
输出：[2,4,3,1]
输出 [4,2,3,1]，[2,4,1,3] 和 [4,2,1,3] 也会被接受。
```

**提示：**

- 1 <= A.length <= 5000
- 0 <= A[i] <= 5000

**解题思路：**

遍历数组中的元素，双指针，当指针1从开头开始，指针2从结尾开始。指针1检测到元素对2取余的时候等于0，跳出循环；接着指针2检测到元素对2取余时等于1，跳出循环，交换两个指针上面的值。指针1继续向右（+1），指针2继续向左（-1）搜寻。

```javascript
/**
 * @param {number[]} A
 * @return {number[]}
 */
 const numberArray = [1,2,3,4];
 var sortArrayByParity = function(A){
    const len = A.length;
    if(len === 1) return A;
    let i = 0;
    let j = len-1;
    while (i < j) {
        if(A[i] % 2 === 0){
            i++;	
            continue;	 			
        }
        if(A[j] % 2 === 1){
            j--;
            continue;
        }
        let temp = A[i];
        A[i] = A[j];
        A[j] = temp;
        i++;
        j--
    }
    // return A.sort(function(x){
    // 	if(x % 2 === 1) return 1;
    // });

    // const result = [];
    // A.map(v=>{
    // 	return v%2?result.push(v):result.unshift(v);
    // });	
    // return result;	
    return A;	
}
console.log(sortArrayByParity(numberArray));
```

### 按奇偶排序数组2

给定一个非负整数数组 A， A 中一半整数是奇数，一半整数是偶数。

对数组进行排序，以便当 A[i] 为奇数时，i 也是奇数；当 A[i] 为偶数时， i 也是偶数。

你可以返回任何满足上述条件的数组作为答案。

 

**示例：**

```
输入：[4,2,5,7]
输出：[4,5,2,7]
解释：[4,7,2,5]，[2,5,4,7]，[2,7,4,5] 也会被接受。
```

**提示：**

- 2 <= A.length <= 20000
- A.length % 2 == 0
- 0 <= A[i] <= 1000 

**解题思路：**

A[i] 为奇数时，i 也是奇数；当 A[i] 为偶数时， i 也是偶数。设定一个变量j为1，A[j]为奇数，i设为0，从0,2,4这样的间隔开始遍历，当 A[i]上的元素为奇数时，检测A[j]上面的数是否为奇数，如果是的话就+2检查下一项，如果不是奇数，就交换i,j位置。

```javascript
const array = [5,7,10,2];
/**
 * @param {number[]} A
 * @return {number[]}
*/
var sortArrayByParity2 = function(A){
    let j = 1;
    for(let i = 0; i < A.length-1; i = i+2){
        if(A[i] % 2 === 1){           
            while(A[j] % 2 === 1){        
                j = j + 2;
            }
            let temp = A[i];
            A[i] = A[j];
            A[j] = temp;
        }				
    }
    return A;
}
sortArrayByParity2(array);
```

### 机器人能否返回原点

在二维平面上，有一个机器人从原点 (0, 0) 开始。给出它的移动顺序，判断这个机器人在完成移动后是否在 (0, 0) 处结束。

移动顺序由字符串表示。字符 move[i] 表示其第 i 次移动。机器人的有效动作有 R（右），L（左），U（上）和 D（下）。如果机器人在完成所有动作后返回原点，则返回 true。否则，返回 false。

注意：机器人“面朝”的方向无关紧要。 “R” 将始终使机器人向右移动一次，“L” 将始终向左移动等。此外，假设每次移动机器人的移动幅度相同。

**示例 1:**

```
输入: "UD"
输出: true
```

解释：机器人向上移动一次，然后向下移动一次。所有动作都具有相同的幅度，因此它最终回到它开始的原点。因此，我们返回 true。
**示例 2:**

```
输入: "LL"
输出: false
```

解释：机器人向左移动两次。它最终位于原点的左侧，距原点有两次 “移动” 的距离。我们返回 false，因为它在移动结束时没有返回原点。

**解题思路：**

设定x,y坐标，遍历字符串，根据不同的字符串来移动x,y的距离。最后判断x,y是否等于初始值来判断是否在原地。

```javascript
/**
 *@params {string} moves
 *@return {boolean}
 */
 var judgeCircle = function(moves){
    let x=0,y=0,moveArr = moves.split('');
    moveArr.map(i =>{
        switch(i) {
            case 'R':
            x++
            break;
            case 'L':
            x--;
            break;
            case 'U':
            y++
            break;
            case 'D':
            y--;
            break;		 				
            default:
            break;
        }
    })
    return x === 0 && y === 0;
 }
 console.log(judgeCircle('UD'));
```

### 汉明距离

两个整数之间的汉明距离指的是这两个数字对应二进制位不同的位置的数目。

给出两个整数 x 和 y，计算它们之间的汉明距离。

**注意：**
0 ≤ x, y < 231.

**示例:**

```
输入: x = 1, y = 4
输出: 2
```

**解释:**

```
1   (0 0 0 1)
4   (0 1 0 0)
       ↑   ↑
```

上面的箭头指出了对应二进制位不同的位置。

**解题思路：**

异或（^）:位不相等时取1，否则取零

```
1   (0 0 0 1)
^
4   (0 1 0 0)
-----------------
	(0 1 0 1)
```

```javascript
/**
 * @param {number} x
 * @param {number} y
 * @return {number}
 */
 var hammingDistance = function(x, y) {
    return (x^y).toString(2).replace(/0/g,'').length;
 };
 hammingDistance(1,4);
```

### 翻转图像

给定一个二进制矩阵 A，我们想先水平翻转图像，然后反转图像并返回结果。

水平翻转图片就是将图片的每一行都进行翻转，即逆序。例如，水平翻转 [1, 1, 0] 的结果是 [0, 1, 1]。

反转图片的意思是图片中的 0 全部被 1 替换， 1 全部被 0 替换。例如，反转 [0, 1, 1] 的结果是 [1, 0, 0]。

**示例 1:**

```
输入: [[1,1,0],[1,0,1],[0,0,0]]
输出: [[1,0,0],[0,1,0],[1,1,1]]
```

解释: 首先翻转每一行: [[0,1,1],[1,0,1],[0,0,0]]；
​     然后反转图片: [[1,0,0],[0,1,0],[1,1,1]]
**示例 2:**

```
输入: [[1,1,0,0],[1,0,0,1],[0,1,1,1],[1,0,1,0]]
输出: [[1,1,0,0],[0,1,1,0],[0,0,0,1],[1,0,1,0]]
```

解释: 首先翻转每一行: [[0,0,1,1],[1,0,0,1],[1,1,1,0],[0,1,0,1]]；
​     然后反转图片: [[1,1,0,0],[0,1,1,0],[0,0,0,1],[1,0,1,0]]
**说明:**

- 1 <= A.length = A[0].length <= 20
- 0 <= A[i][j] <= 1

**解题思路：**

**~1**的计算步骤：

- 将**1**(这里叫：原码)转二进制 ＝ **00000001**
- 按位取反 ＝ **11111110**
- 发现符号位(即最高位)为**1**(表示负数)，将除符号位之外的其他数字取反 ＝ **10000001**
- 末位加1取其补码 ＝ **10000010**
- 转换回十进制 ＝ **-2**

```javascript
/**
 * @param {number[][]} A
 * return {number[][]}
 */	
 const img = [[1,1,0],[1,0,1],[0,0,0]];
 var flipAndInvertImage = function(A){
    return A.map(v=> v.map( i => ~i + 2).reverse()) 
 }
 flipAndInvertImage(img);
```

### 增减字符串匹配

给定只含 "I"（增大）或 "D"（减小）的字符串 S ，令 N = S.length。

返回 [0, 1, ..., N] 的任意排列 A 使得对于所有 i = 0, ..., N-1，都有：

如果 S[i] == "I"，那么 A[i] < A[i+1]
如果 S[i] == "D"，那么 A[i] > A[i+1]

**示例 1：**

```
输出："IDID"
输出：[0,4,1,3,2]
```

**示例 2：**

```
输出："III"
输出：[0,1,2,3]
```

**示例 3：**

```
输出："DDI"
输出：[3,2,0,1]
```

**提示：**

1 <= S.length <= 1000
S 只包含字符 "I" 或 "D"。

**解题思路:**

对 长度为N的从0开始顺序排序的数组根据 'I','D'进行排序。 

```javascript
/**
 * @param {string} S
 * @return {number[]}
 */
 const str = 'IDID';
 var diStringMatch = function(S){
    S += "I"
    let stArr = S.split('');
    let Arr = [...new Array(S.length)].map((v,i) => i);
    let result = [];
    stArr.map(v =>{
        if(v === 'I'){
            result.push(Arr.shift());
        }else{
            result.push(Arr.pop());
        }		 		
    })
    return result;
 }
 diStringMatch(str);
```

### 山脉数组的峰顶索引

我们把符合下列属性的数组 A 称作山脉：

A.length >= 3
存在 0 < i < A.length - 1 使得A[0] < A[1] < ... A[i-1] < A[i] > A[i+1] > ... > A[A.length - 1]
给定一个确定为山脉的数组，返回任何满足 A[0] < A[1] < ... A[i-1] < A[i] > A[i+1] > ... > A[A.length - 1] 的 i 的值。

**示例 1：**

```
输入：[0,1,0]
输出：1
```

**示例 2：**

```
输入：[0,2,1,0]
输出：1
```

**提示：**

3 <= A.length <= 10000
0 <= A[i] <= 10^6
A 是如上定义的山脉 

**解题思路：**

由于已知数组一定是山脉数组，所以可以理解为求数组中的最大数？

```javascript
/**
 * @param {number[]} A
 * @return {number}
 */
 const arr = [0,1,2,3,2,1,0];
 var peakIndexInMoutainArray = function(A){
    let max = A[0],index;
    for(let i =0; i < A.length; i++){
        if(A[i]>max){
            max = A[i]
            index=i;
        }
    }
    return index;
 }
 peakIndexInMoutainArray(arr);
```

### 二叉树的最大深度

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

说明: 叶子节点是指没有子节点的节点。

**示例：**
给定二叉树 [3,9,20,null,null,15,7]，

    	3
       / \
      9  20
        /  \
       15   7
返回它的最大深度 3 

**解题思路：**

递归

```javascript
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number}
 */
 var maxDepth = function(root){
    if(root === null){
        return 0;
    }
    return Math.max(arguments.callee(root.left),arguments.callee(root.right))+1;
 }
```

### 数字的补数

给定一个正整数，输出它的补数。补数是对该数的二进制表示取反。

**注意:**

给定的整数保证在32位带符号整数的范围内。
你可以假定二进制数不包含前导零位。
**示例 1:**

```
输入: 5
输出: 2
```

解释: 5的二进制表示为101（没有前导零位），其补数为010。所以你需要输出2。
**示例 2:**

```
输入: 1
输出: 0
```

解释: 1的二进制表示为1（没有前导零位），其补数为0。所以你需要输出0。

**解题思路：**

异或（^）:位不相等时取1，否则取零

```javascript
/**
 * @param {number} num
 * @return {number}		 
*/
const num = 5;
var findComplement = function(num){
    return parseInt(num.toString(2).split('').map(v=> 1^v ).join(''),2);
}
findComplement(num);
```

### 数值千分位格式化

几个常见的截取字符函数

#### substr

语法：

```javascript
stringObject.substr(start,length)
```

| 参数   | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| start  | 必需。要抽取的子串的起始下标。必需是数值，如果是负数，那么改参数声明从字符串的尾部开始算起的位置。也就是说，-1 指字符串中最后一个字符，-2指倒数第二个字符 |
| length | 可选，子串中字符数，必须是数值。如果省略了该参数，那么返回从 stringobject 的开始位置到结尾的子串 |

#### substring

语法：

```javascript
stringObject.substring(start,stop)
```

| 参数  | 描述                                                         |
| ----- | ------------------------------------------------------------ |
| start | 必需。一个非负的整数，规定要提取的子串的第一个字符在 stringObject 中的位置 |
| stop  | 可选。一个非负的整数，比要提取的子串的最后一个字符在 stringObject 中的位置多1.如果忽略该参数，那么返回的子串会一直到字符串的结尾 |
| 返回  | 一个新的字符串，该字符串值包含 stringObject 的一个子字符串，内容是从 start 到 stop-1 的所有字符，其长度为 stop 减 start |



#### slice

语法：

```javascript
stringObject.slice(start,end)
```

| 参数   | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| start  | 要抽取的片断的起始下标。如果是负数，则该参数规定是从字符串的尾部开始算起的位置。也就是说，-1指字符串的最后一个字符，-2指倒数第二个字符，以此类推 |
| end    | 紧接着要抽取的片段的结尾的下标。若未指定该参数，则要提取的子串包括 start 到原字符串结尾的字符串。如果该参数是负数，那么它规定的是从字符串的尾部开始算起的位置 |
| return | 一个新的字符串，包括字符串从 start 开始从 end （不包括end）结束为止的所有字符 |



```javascript
const n = 12345678910;
// 方法1
function format(n){
    const result = [];
    const str = n.toString();
    const len = str.length;
    for(let i=1;i<=len/3;i++){
        result.unshift(str.substr(len-i*3,3));
        if(len-i*3<3){
            result.unshift(str.substr(0,len-i*3))
        }
    }
    return result.join(',');
}
// 方法2
function format(n){
    let resultStr = '';
    const str = n.toString();
    const len = str.length;
    let j=0;
    for(let i=len-1;i>=0;i--){	
    j++;			
        resultStr=str.charAt(i)+resultStr
        if(!(j%3) && i!=0){
            resultStr=','+resultStr
        }
    }
    return resultStr;
}
// 方法3
function format(n){
    const result = [];
    const str = n.toString();
    const len = str.length;
    for(let i=0;i<len/3;i++){
        result.unshift(str.substring(len-(i+1)*3,len-i*3))
    }
    return result.join(',');
}
// 方法4
function format(){
    
}
// 方法5
function format(n){
    return (n.toFixed(2) + '').replace(/\d{1,3}(?=(\d{3})+(\.\d*)?$)/g,'$&,')
}
```
