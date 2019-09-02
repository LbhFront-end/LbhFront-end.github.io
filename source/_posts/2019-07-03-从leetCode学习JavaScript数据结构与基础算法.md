---
title: 从leetCode学习JavaScript数据结构与基础算法
date: 2019-07-03  09:30:54
tags: javaScript数据结构与算法
categories: 
	- javaScript相关
---

循序渐进，保持空杯

## 从leetCode学习JavaScript数据结构与基础算法

简单算法：

`字符串`、`数组`、`正则`、`排序`、`递归`

### 字符串

**反转字符串中的单词③**

给定一个字符串，需要反转字符串中每个单词的字符顺序，同时仍保留空格和单词的初始顺序

实例：

```shell
输入："Let's take LeetCode contest"
输出："s'teL ekat edoCteeL tsetnoc"
```

注意：在字符串中，每个单词由单个空格分隔，并且字符串中不会有任何额外的空格

```javascript
/**
* @param {string} s
* @return {string}
*/
const reverseWords = function(s){
    // 1.字符串按空格进行分隔，数组的元素的先后顺序就是单词的顺序
    // 2.遍历数组的元素，也就是每个单词String，通过 split分隔转Array 同时用Array 自带的 reverse方法将每个单词数组分隔后产生的数组反转
    // 3.接着用join('')将单词数组中的每个数组拼凑成字符串
    // 4.最后将字符串单词数组拼凑为最终的字符串
    return s.split(' ').map(i=>i.split('').reverse().join('')).join(' ')
    // 或者用正则，匹配空格
    return s.split(/\s/g).map(i=>i.split('').reverse().join('')).join(' ')
    // 匹配单词(match)
    return s.match(/[\w']+/g).map(i=>i.split('').reverse().join('')).join(' ')
}
```

测试用例：

```javascript
import reverseByWord from '../../code/string/lession1'

test('reverseByWord:Let\'s take LeetCode contest', () => {
  expect(reverseByWord("Let's take LeetCode contest")).toBe("s'teL ekat edoCteeL tsetnoc")
})
```

知识点：

`String.prototype.split`、`String.prototype.match`、`Array.prototype.map`、`Array.prototype.reverse`、`Array.prototype.join`

扩展：上面讲的是单个空格隔开，那如果不是当个空格呢？

```javascript
// 同样可以使用正则的贪婪匹配来解决
const reverseWords = function(s){
    // 或者用正则，匹配空格
    return s.split(/\s+/g).map(i=>i.split('').reverse().join('')).join(' ')
    // 匹配单词(match)
    return s.match(/[\w']+/g).map(i=>i.split('').reverse().join('')).join(' ')
}
```



**计数二进制子串**

给定一个字符串 `s`，计算具有相同数量 0 和 1的非空（连续）子符串的数量，并且这些子字符串中的所有 0 和 1 都是组合在一起的。

重复出现的子串要计算它们出现的次数。

示例：

```shell
输入："00110011"
输出：6
解释：有6个子串具有相同数量的连续1和0："0011","01","1100","10","0011"和"01"
请注意，一些重复出现的子串要计算它们出现的次数
```



```javascript
/**
 * @param {string} str
 * @return {number}
*/
export default (str) => {
  // 创建数据结构堆栈保存数据
  const result = []
  const match = (str) => {
    let j = str.match(/^(0+|1+)/)[0]
    // j=(n个)0->o=(n个)1;j=(n个)1->o=(n个)0;
    let o = (j[0] ^ 1).toString().repeat(j.length)
    let reg = new RegExp(`^(${j}${o})`)
    if (reg.test(str)) {
      return RegExp.$1
    } else {
      return ''
    }
  }
  // 循环
  for (let i = 0, len = str.length - 1; i < len; i++) {
    // 简单递归
    let sub = match(str.slice(i))
    if (sub) {
      result.push(sub)
    }
  }
  return result
}

```

测试用例：

```javascript
test('countBinarySubstring(00110011)', () => {
  expect(countBinarySubstring('00110011')).toEqual(['0011', '01', '1100', '10', '0011', '01'])
})

test('countBinarySubstring(10101)', () => {
  expect(countBinarySubstring('10101')).toEqual(['10', '01', '10', '01'])
})
```

知识点：

`发现规律`，`RegExp`

### 数组

**电话号码的组合**

给定一个仅包含 `2-9`的字符串，返回它能表示的字母组合。给出数字到字母的映射如下（与电话按钮相同）。注意1不对应任何字母

```shell
1[] 2[abc] 3[def]
4[ghi] 5[jkl] 6[mno]
7[pqrs] 8[tuv] 9[wxyz]
```

示例：

```shell
输入： "23"
输出： ["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

说明：上面的答案是按字典排序的，可以任意选择答案输出的顺序

```javascript
/**
 * @param {string} digits
 * @return {string[]}
*/

export default (digits) => {
  if (digits.length < 1) return []
  // 建立电话号码键盘映射
  const map = ['', 1, 'abc', 'def', 'ghi', 'jkl', 'mno', 'pqrs', 'tuv', 'wxyz']
  if (digits.length < 2) return map[digits].split('')
  // 将输入的digits 分隔成数组，234=>[2,3,4]
  const num = digits.split('')
  // 保存键盘映射后的字母内容，如 23=>['abc','def']
  const code = []
  num.forEach(item => {
    code.push(map[item])
  })
  const comb = (arr) => {
    // 临时变量用来保存两个组合的结果
    const temp = []
    // 循环
    for (let i = 0, ilen = arr[0].length; i < ilen; i++) {
      for (let j = 0, jlen = arr[1].length; j < jlen; j++) {
        temp.push(`${arr[0][i]}${arr[1][j]}`)
      }
    }
    // 去掉一开始遍历的前两个，替换为这两个循环后的结果
    arr.splice(0, 2, temp)
    // 当数组的长度大于1时递归
    if (arr.length > 1) {
      comb(arr)
    } else {
      return temp
    }
    // 返回真正的结果
    return arr[0]
  }
  // 开始递归运算
  return comb(code)
}

```

测试用例：

```javascript
test('letterCombinations(23)', () => {
  expect(letterCombinations('23')).toEqual(['ad', 'ae', 'af', 'bd', 'be', 'bf', 'cd', 'ce', 'cf'])
})

test('letterCombinations(234)', () => {
  expect(letterCombinations('234')).toEqual([
    'adg', 'adh', 'adi',
    'aeg', 'aeh', 'aei',
    'afg', 'afh', 'afi',
    'bdg', 'bdh', 'bdi',
    'beg', 'beh', 'bei',
    'bfg', 'bfh', 'bfi',
    'cdg', 'cdh', 'cdi',
    'ceg', 'ceh', 'cei',
    'cfg', 'cfh', 'cfi'
  ])
})

```

知识点：

`公式运算`



**卡牌分组**

给定一副牌，每张牌上都写着一个整数。

此时，需要选定一个数字 `x`，使得可以将整部牌按下述规则分成 1 组或者更多：

- 每组都有 `x` 张牌
- 组内所有的牌上都写着相同的整数

仅当可选的 `x>=2`时返回 `true`

示例：

```shell
输入：[1,2,3,4,4,3,2,1]
输出：true
解释：可行的分组是 [1,1],[2,2],[3,3],[4,4]
```

提示：

1. `1 <= deck.length <= 10000`
2. `0 <= deck[i] < 10000`

知识点：

`归并运算`



**种花问题**

知识点：

`筛选运算`



**格雷编码**

知识点：

`二进制运算`