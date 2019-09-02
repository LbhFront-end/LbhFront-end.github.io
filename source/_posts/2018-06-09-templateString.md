---
title: template_strings
date: 2018-06-09 12:45:00
tags: ES6
categories: 
	- ES6相关
---

模板字符串
==============

模板字面量是允许嵌入表达式的字符串字面量
---------------------------------------

>\`string text`
>
>\`string text line 1`  
>
>\`string text line 2`
>
>\`string text ${expression} string text`
>
>tag \`string text ${expression} string text`

描述
-----------------

模板字符串使用反引号（\`\`）来代替普通字符串中的双引号和单引号。模块字符串可以包含特殊语法（${expression}）的占位符。占位符中的表达式和周围的文本会一起传递给一个默认函数，该函数负责将所有的部分连接起来，如果一个模板字符串由表达式开头，则该字符串被称为带标签的模板字符串，该表达式通常是一个函数，它会在模板字符串处理后被调用，在输出最终结果前，都可以使用该函数来对模板字符串进行操作处。在模板字符串内使用反引号（\`）时，需要在它的前面加转移符（\）

>\`\\`` === "\`" //--> true

多行字符串
---------------

在新行中插入任何字符都是模板字符串的一部分，使用普通字符串，可以通过以下方式获得多行字符串

>console.log(\`string text line 1\n\`+\`string text line 2\`);  
> //"string text line 1  
> //string text line 2"

获得同样的多行字符串，只需使用以下代码

>console.log(\`string text line 1 string text line 2\`);  
> //"string text line 1  
> //string text line 2"

插入表达式
------------------

在普通字符中插入表达式  

>var a = 5;  
var b = 10;
console.log('sum is' +(a+b) + 'and\ndoubleSum is ' + (a+b)*2 +'.' );  
//sum is 15 and  
//doubleSum is 30.

通过模板字符串由更优雅的方式来表示
>var a = 5;  
var b = 10;  
console.log(\` sum is ${a+b} and doubleSum is ${(a+b)*2}. \`);

嵌套模板
--------------

在某些时候，嵌套模板具有可配置字符串的最简单也是更可读的方法，在模板中，只需在模板内的占位符 `${}` 内使用它们，就可以轻松地使用内部反引号。 例如：如果条件a是真的，那么就返回这个模板化的文字

ES5：

>var classes = 'hander'  
classes += ('isLargeScreen() ? '': item.isCollapsed ? 'icon-expander' : 'icon-collapser');

ES6使用模板文字没有嵌套

>const classes = \`header ${isLargeScreen() ? '' : (item.isCollapsed) ? 'icon-expander' : 'icon-collapser')}\`;

ES6使用嵌套模板字面量

>const classes = \`header ${isLargeScreen() ? '' : \`icon-${item.isCollapsed ? 'expander' : 'collapser'}\`}\`;

带标签的模板字符串
------------------

更高级的形式的模块字符串是带标签的模块字符串。标签可以用函数解析模块字符串。标签函数的第一个参数包含一个字符串值的数组，其余的参数与表达式相关。最后，函数可以返回处理好的字符串。用于该标签的函数的名称可以被命名为任何名字

```javascript
    var person = 'Mike';  
    var age = 28;  
    function myTag(strings,personExp,ageExp){
        var str0 = string[0];
        var str1 = string[1];
        var ageStr;
        if(ageStr > 99){
            ageStr = 'centenarian';
        } else{
            ageStr = 'youngster';
        }
        return str0 + personExp + str1 + ageStr;
    }

    var output = myTag `that ${person} is a ${age}`.;
    console.log(output); // that Mike is a youngster.
```

原始字符串
-----------------

在标签函数的第一个参数中，存在一个特殊的属性 `raw`,我们可以通过它来访问模板字符串的原始字符串，而不经过特殊字符串的替换

```javascript
function tag(strings){
    console.log(strings.raw[0]);
}

tag `string text line1 \n string text line 2`
// logs "string text line 1 \n string text line 2"
// including the two characters '\' and 'n'
```

另外使用 `String.raw()` 方法创建原始字符串和使用默认模板函数和字符串连接创建是一样的  

```javascript
var str = String.raw`Hi\n${2+3}!`  
//Hi\n5!

str.length;
//6

//str.split('').join(',');
//"H,i,\,n,5,!"
```

带标签的模版字面量及转义序列
-------------------------

自ES2016起，带标签的模版字面量遵守以下转义序列的规则：

Unicode字符以"\u"开头，例如\u00A9  
Unicode码位用"\u{}"表示，例如\u{2F804}  
十六进制以"\x"开头，例如\xA9  
八进制以"\"和数字开头，例如\251  
这表示类似下面这种带标签的模版是有问题的，因为对于每一个ECMAScript语法，解析器都会去查找有效的转义序列，但是只能得到这是一个形式错误的语法：

    latex`\unicode`
    // 在较老的ECMAScript版本中报错（ES2016及更早）
    // SyntaxError: malformed Unicode character escape sequence