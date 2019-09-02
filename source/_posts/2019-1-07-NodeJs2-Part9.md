---
title: Nodejs实战 —— 测试 Node 程序
date: 2019-01-07 11:30:00
tags: Nodejs
categories: 
	- Nodejs
---



读 《node.js实战2.0》，进行学习记录总结。

[当当网购买链接](http://product.dangdang.com/25329015.html)

[豆瓣网1.0链接](https://book.douban.com/subject/25870705/)

## 测试 Node 程序

本章内容

- 用 Node 的 assert 模块测试
- 使用其他断言库
- 使用 Node 单元测试框架
- 用 Node 模拟并控制 Web 浏览器
- 在测试失败时获取更多的信息

添加到 程序中的功能越来越多，出现的 bug 的风险就越高。没经过测试的程序是不完整的，而手动测试既繁琐又容易出错，所以自动测试越来越受欢迎。自动测试指的是编写代码来测试代码，而不是手动运行程序中的功能。

两种基本测试：单元测试和验收测试。单元测试直接测试代码逻辑，通常是在函数或方法层面，适用于所有类型的程序。单元测试可以分成两种形态：测试驱动开发（TDD） 和行为驱动开发（BDD）。实事求是地讲，TDD 和 BDD 基本是一样的，它们的区别在于风格上。这个是否重要取决于个人。验收测试一般是对 Web 程序进行的额外的测试，需要用脚本控制浏览器来触发 Web 程序的功能。

#### **TDD 与 BDD**

**BDD** ：Behavior Driven Development 行为驱动开发是一种敏捷软件开发的技术，它鼓励软件项目中的开发人员、QA和非技术人员之间的协作。

BDD 的重点是通过与利益相关者的讨论取得对预期软件的行为的清醒认识。它通过用自然语言书写非程序员可读的测试用例扩展了测试驱动开发行为。行为驱动开发人员使用了混合领域中统一的语言的母语语言来描述他们的代码的目的。这让开发者取得以把精力集中在代码应该怎么写，而不是技术细节上，而且也最大程度减少了将代码编写者的技术语言与商业客户、用户、利益相关者、项目管理者等的领域语言之间来回翻译的代价。

**TDD**：Test-Driven Development 就是测试驱动开发，它是一种测试先于编写代码的思想，用于指导软件开发，测试驱动开发是敏捷开发中的一项核心时间和技术，也是一种方法论，TDD的原理是在开发功能代码之前，先编写单元测试用例，测试代码确定需要编写什么产品代码。

TDD 的基本思路是通过测试来推动整个开发的进行，但测试驱动开发并不只是单纯的测试工作，而是把需求分析，设计，质量控制量化的过程。TDD的重要目的不仅仅是测试软件，测试工作保证代码只是其中一个部分，而且在开发过程中帮助客户和程序员去除模棱两可的需求。TDD 首先考虑使用的需求（对象、功能、过程、接口等）。主要编写的测试用例框架对功能的过程和接口进行设计，而测试框架可以持续进行验证。优点是在任意一个开发节点都可以拿出一个使用，含少量bug 并具有一定功能和能够发布的产品。缺点是增加代码量，测试代码是系统代码的两倍或者更多，但是同时节省了调试程序及挑错的时间。

TDD 的原则：

1. **独立测试**：不同的代码的测试应该互相独立，一个类对应一个测试类，一个函数对应一个测试函数。用例也应各自独立，每个用例不能使用其他用例的结果数据，结果也不能依赖用例执行顺序。一个角色，开发过程中包含多种工作，做不同工作时应专注于当前的角色，不要过多考虑其他方面的细节
2. **测试列表**：代码的功能点可能很多，并且需求可能是陆续出现的，任何阶段想添加功能时，应把先把功能点加到测试列表中，然后才能继续手头工作，避免疏漏。
3. **测试驱动**：即利用测试来驱动开发，是 TDD 的核心。要实现某个功能，要编写某个类或某个函数，应首先编写测试代码，明确这个类、函数如何使用、测试，然后再对其进行设计、编码。
4. **先写断言**：编写测试代码时，应该首先编写判断代码功能的断言语句，然后编写必要的辅助语句。
5. **可测试性**：产品代码设计、开发时应尽可能提高可测性。每个代码单元的功能应该比较单纯，每个类应该只做它应该做的事情，尤其是增加新功能，不要为图一时的便利，随便在原有代码中添加功能。多考虑使用子类、继承、重载的方法
6. **及时重构**：对结构不合理，重复不好的代码，在测试通过后，应及时进行重构。
7. **小步前进**：软件开发是复杂性很高的工作，小步前进是降低复杂性的最好的方法。

对于单元测试，会介绍 Node 的 assert 模块、Mocha、Vows、Should.js  框架和 Chai。对于验收测试，我们会介绍Node 中使用 Selenium。

| 测试类型 | 分类   | 工具                                     | 描述               |
| -------- | ------ | ---------------------------------------- | ------------------ |
| 单元测试 | TDD    | Mocha/Jasmine/Node 中的 assert 模块/Chai | 测试程序逻辑       |
|          | BDD    | Mocha/Vows/Chai/Should.js                |                    |
| 验收测试 | 浏览器 | WebdriverIO/Selenium                     | 测试程序界面和功能 |
|          | 无头   | WebdriverIO/Phantom/Zombie.js            |                    |

### 单元测试

单元测试是指通过编写代码来测试程序中的各个部分。编写测试代码会让你更认真思考程序的设计选择，尽早避开各种陷阱。测试还会让你确信自己最近的修改没有引入错误。尽管单元测试需要在编码时做些工作，但以后每次修改程序后都不用自己手动测试了，所以还是能节省很多时间的。

做单元测试需要些技巧，而异步逻辑有带来了新的挑战。因为异步单元测试可以并行运行，所以必须小心，确保测试不会互相干扰。比如说，如果测试在硬盘上创建了一个临时文件，那么在完成测试后删除文件时一定要谨慎，以免删除另外一个未完成的测试正在使用过的文件。因此很多单元测试框架都有流程控制，可以让测试按顺序运行。

本节介绍的内容：

- Node 自带的 assert 模块 —— TDD 风格自动化测试的工具
- Mocha —— 相对比较新的测试框架，可以用来做 TDD- 或 BDD-风格的测试
- Vows —— 得到广泛应用的 BDD 风格测试框架
- Should.js —— 构建在 Node assert 模块之上的模块，提供 BDD 风格的断言

#### **assert 模块**

assert 模块是 Node 中大多数单元测试的基础，它可以测试一个条件，如果条件未满足，则抛出错误。很多第三方测试框架都用到了 assert 模块，甚至没有测试框架也可以用它做测试。

**简单的例子：**

假设有一个简单的待办事项程序，它把事项存在内存里，而你要断言它做的是你认为它做的。

下面的代码定义的模块实现了程序的核心功能，包括待办事项的创建、获取和删除。它还包括一个简单的 doAsync 方法，所以还可以看到如何测试异步方法。

```javascript
// todo.js
class Todo {
  constructor() {
    // 定义待办项数据库
    this.todos = [];
  }

  add(item) {
    if (!item) throw new Error('Todo.prototype.add requires an item');
    this.todos.push(item);
  }

  deleteAll() {
    this.todos = [];
  }
  
  get length() {
    return this.todos.length;
  }

  // 两秒后带着 true 调用回调
  doAsync(cb) {
    setTimeout(cb, 2000, true);
  }
}

module.exports = Todo;
```

接下里用 assert 模块测试这段代码，下面的代码加载了必需模块，创建了新的待办事项列表，还声明一个变量记录完成的测试数量。

```javascript
// test.js
const assert = require('assert');
const Todo = require('./todo');
const todo = new Todo();
let testsCompleted = 0;
```

**用 equal 检查变量的值，测试待办事项程序的删除功能**

```javascript
// test.js
function deleteTest() {
  // 添加数据来删除
  todo.add('Delete Me');
  // 断言数据添加成功
  assert.equal(todo.length, 1, '1 item should exist');
  todo.deleteAll();
  assert.equal(todo.length, 0, 'No items should exist');
  // 记录测试已完成
  testsCompleted++;
}
```

上面的测试添加一个待办事项，然后再删除，所以最后应该没有待办事项。如果程序能正常工作，那么 todo.length 的值应该是 0。如果程序出现问题，则会有异常抛出。如果 todo.length 不是 0.那么这个断言会在栈跟踪中显示一条错误消息，在控制台中输出 ‘No items should exist“，在断言后面将 testCompleted 加一，记录已完成了一次测试。

**用 notEqual 找出逻辑中的问题**

```javascript
function addTest() {
  todo.deleteAll();
  todo.add('Added');
  // 断言有事项存在
  assert.notEqual(todo.length, 0, '1 item should exist');
  testsCompleted++;
}
```

assest 模块中还有一个 notEqual 断言，当程序产生特定的值表明逻辑有问题时，可以采用这种断言。上面展示了在删除所有待办事项又添加了一个事项，然后再获取所有事项，如果事项的数量为0，断言就会失败并抛出异常。

**其他功能：strictEqual、notStrictEqual、deelEqual、notDeepEqual **

除了 equal 和 notEqual ,assert 模块还提供了更严格的版本，strictEqual 和 notStrictEqual,它们在进行判断时用的是严格的相等操作符 ===。

assert 模块也有用来比较对象的 deepEqual 和 notEqual ，这些断言中的 deep 表明它们会层层深入对两个对象进行比较，比较两个对象的属性，如果属性也是对象，则会继续比较属性的属性。

**用 ok 测试异步值是否为 true**

因为是异步测试，所以要提供一个回调函数（cb），向测试运行者发送测试结束的信号——不能像同步测试那样靠返回语句来表明测试结束了。要判断 doAsync 的结果值是否为 true,可以用 ok 断言，用它判断一个值是否为 true 很容易。

```javascript
function doAsyncTest(cb) {
  todo.doAsync(value => {
    // 断言值为 true
    assert.ok(value, 'Callback should be passed true');
    testsCompleted++;
    cb();
  });
}
```

**测试能否正确抛出错误**

assert 模块还可以检查程序抛出消息 是否正确。throws 调用中的第二个参数是一个正则表达式，表示要在错误消息中检查文本 requires

```javascript
function throwsTest(cb) {
  assert.throws(todo.add, /requires/);
  testsCompleted++;
}
```

**运行测试**

测试已经定义好了，接下来要在测试文件中添加运行这些测试的代码，下面的代码会运行前面的定义的所有测试，然后输出完成的测试数量。

```javascript
deleteTest();
addTest();
throwsTest();
doAsyncTest(() => {
  console.log(`Completed ${testsCompleted} tests`);
})
```

如果测试成功了，脚本会告诉你以完成的测试数量。要防止某个测试出问题，可以追踪测试的开始和结束时间。比如说，某个测试可能没能执行到断言的地方。

使用 Node 自带的 assert 模块时，每个测试用例中都要包含很多套路化的代码用以设置测试（比如删除所有事项），追踪测试进程（’已完成‘计数器）。这些套路化的代码会占用编写测试用例的时间和精力。如果能把这些工作交给专用的框架，让你的精力都放在业务员逻辑的测试上就更好。接下来用 Mocha,了解一下如何使用这个第三方单玉测试框架让 工作变得更加轻松。

#### **Mocha**

Mocha 是个流行的测试框架，很容易上手，尽管 Mocha 默认是 BDD 风格的。但也可以用在 TDD 风格的测试中。Mocha 具有很多特性，包括全局变量泄露检测和客户端测试。

**全局变量泄露测试**

一般应该不需要整个程序范围内全都可读的全局变量，并且按照编程最佳实践来说，要尽量少用全局变量。但在 ES5中，一不小心就创建了一个全局变量来，只要在声明变量时忘记写 关键字 var ，这个变量就是全局变量了。Mocha 能够发现这种不小心创建出来的全局变量泄露，如果你创建了全局变量，它会在测试时抛出错误。如果你想禁用全局泄露检查，可以运行 mocha 命令时加上 --ignored-leaks 选项。此外，如果要指明要使用的几个全局变量，可以把它们放在 --globals 后面，用逗号隔开。

Mocha 测试默认使用 BDD 风格的函数定义和设置，这些函数包括 describe、it、before、after、beforeEach和 afterEach，另外，Mocha 也有 TDD 接口，用 suite 代替 describe,test代替 it,setup 代替 before ，teardown 代替 after。

**用 Mocha测试 Node 程序**

初始化 package.json，定义 scripts:

```json
"scripts": { 
 "test": "mocha" 
}, 
```

安装依赖：

```shell
npm i -S mocha
```

测试会放在 test 目录下。Mocha 默认使用 BDD 接口,Mocha 测试的基本结构

```javascript
// test/memdb.js
const memdb = require('..');
describe('memdb', () => {
  describe('.saveSync(doc)', () => {
    it('should save the document', () => {
    });
  });
});
```

Mocha 也支持 TDD 和 qunit 以及 exports 风格的接口，下面是一个例子

```javascript
module.exports = {
    'memdb':{
        '.saveSync(doc)':{
            'should save the document':()=>{
                
            }
        }
    }
}
```

这些接口提供的功能是一样的，依然用的是默认的 BDD 接口，下面是第一个测试

```javascript
const memdb = require('..');
const assert = require('assert');
describe('memdb', () => {
  describe('.saveSync(doc)', () => {
    it('should save the document', () => {
      const pet = { name: 'Tobi' };
      memdb.saveSync(pet);
      const ret = memdb.first({ name: 'Tobi' });
      assert(ret == pt);
    });
  });
});
```

添加 saveSync 功能

```javascript
const db = [];
exports.saveSync = (doc) => {
  db.push(doc);
}
exports.first = (obj) => {
  return db.filter(doc => {
    for (let key in obj) {
      if (doc[key] != obj[key]) {
        return false;
      }
    }
    return true;
  }).shift();
}
```

`describe`块被称为测试套件，表示一组相关的测试，它是一个函数，第一个参数是测试套件的名称，第二个参数是一个实际执行的函数。

`it`块被称为测试用例，表示一个单独的测试，是测试的最小单位，也是一个函数，第一个参数是测试用例的名字，第二个参数是一个实际执行的函数。

所有的测试用例都应该含有一句或多句的断言，它是编写测试用例的关键，断言功能由断言库来实现。

**用 Mocha挂钩定义设置和清理逻辑**

BDD 接口 beforeEach()、afterEach()、before()和 after()接受回调，可以用来定义设置和清理逻辑。

```javascript
const memdb = require('..');
const assert = require('assert');
describe('memdb', () => {
  beforeEach(() => {
    memdb.clear();
  });
  describe('.saveSync(doc)', () => {
    it('should save the document', () => {
      const pet = { name: 'Tobi' };
      memdb.saveSync(pet);
      const ret = memdb.first({ name: 'Tobi' });
      assert(ret == pet);
    });
  });
  describe('.first(obj)', () => {
    it('should return the first matching doc', () => {
      const tobi = { name: 'Tobi' };
      const loki = { name: 'Loki' };
      memdb.saveSync(tobi);
      memdb.saveSync(loki);
      let ret = mendb.first({ name: 'Tobi' });
      assert(ret == tobi);
      ret = memdb.first({ name: 'Loki' });
      assert(ret == loki);
    });
  });
  it('should return null when no doc matches', () => {
    const ret = mendb.first({ name: 'Manny' });
    assert(ret == null);
  });
});
```

理想的情况下，测试用例不会共享状态，要让 memdb 达到这一状态，只需要在 index.js 中实现 .clear 方法来移除所有的文档就可以了。

```javascript
exports.clear = () => {
  db.length = 0;
}
```

**测试异步逻辑**

为了演示异步测试，需要做一些改动，将 saveSync 函数加一个会在短暂延迟之后执行的回调（用来模拟某中异步操作）作为可选的参数：

```javascript
exports.saveSync = (doc, cb) => {
  db.push(doc);
  if (cb) {
    setTimeout(() => {
      cb();
    }, 1000);
  }
}
```

只要给定义测试逻辑函数添加一个参数，就可以把 Mocha 测试用例定义为异步的。这个参数通常被命名为 `done`,下面的代码中演示了如何给异步方法 `.save()` 写测试用例。

```javascript
  describe('asyncronous .saveSync(doc)', () => {
    it('should save the document', (done) => {
      // 保存文档
      const pet = { name: 'Tobi' };
      memdb.saveSync(pet, () => {
        // 用第一个文档调用回调
        const ret = memdb.first({ name: 'Tobi' });
        assert(ret == pet);
        // 告诉Mocha 这个测试用例完成了
        done();
      });
    });
  });
```

 这个规则适用于所有挂钩，比如给 `beforeEach()` 挂钩一个清理数据库的回调，Mocha 可以等它调用后再继续，如果调用 `done()`时它的第一个参数是个错误，Mocha会报告这个错误，并将这个挂钩或测试用例标记为失败：

```javascript
  beforeEach((done) => {
    memdb.clear(done);
  });
```

**Mocha 的非并行测试**

Mocha的测试不是并行测试的，而是一个接一个地测试，虽然这样执行慢，但写起来更容易。Mocha不会让测试运行太长时间，一个测试默认不超过 2000毫秒，超过这个时长会被当做失败的测试。如果有运行时间长的测试，可以用 --timeout 指定一个更大的数值。

不过说到并行的执行测试的框架有一个叫做 Vows的。

#### **Vows**

跟很多单元测试框架比，在Vows 下写的测试代码结构更加强，Vows想让测试更容易理解和维护。

Vows 用它自己的BDD 属于定义测试结构。按Vows 的定义，一个测试套件中包含一个或多个批次。可以把批次当做是一组相互关联的情境，或者要测试的概念领域。批次和上下文是并行运行的。上下文中可能包含主题、一个或多个誓约，以及/或者一或多个先关联的情境（内部情况也是并行运行的）。主题是跟情境相关的测试逻辑。誓约是对主题结果的测试。

```:arrow_down_small:
套件---批次---情境-----主题
			    |----誓约
				|----情境
```

Vows 跟 Mocha 一样，是专门用来做自动化程序测试的。它们的差别主要体现在风格和并行性上，Vows 测试有特定的结构和术语。

先初始化,并安装依赖

```shell
npm init -y
npm i -S -g vows
```

修改 package.json test属性

```json
  "scripts": {
    "test": "vows test/*.js"
  }
```

**用 Vows 测试程序逻辑**

在 Vows 中，既可以运行包含测试逻辑的脚本来触发测试，也可以用 vows 命令行测试运行器。下面这个例子是个独立的测试脚本，用了待办事项程序核心逻辑测试中的一个。

下面的代码创建一个批次，在这个批次中定义了一个情景，情景中定义了一个主题和一个誓约。注意它在主题如何使用回调处理一般逻辑，如果主题不是异步的，可以直接返回值，不用通过回调。

```javascript
// test/todo-test.js
const vows = require('vows');
const assert = require('assert');
const Todo = require('../todo');
// 批次
vows.describe('Todo').addBatch({
   // 情景
  'when adding an item': {
    // 主题
    topic: () => {
      const todo = new Todo();
      todo.add('Feed my cat');
      return todo;
    },
     // 誓约
    'it should exist in my todos': (er, todo) => {
      assert.equal(todo.length, 1);
    }
  }
}).export(module);
```

```javascript
// todo.js
class Todo {
  constructor() {
    this.todos = [];
  }

  add(item) {
    if (!item) throw new Error('Todo.prototype.add requires an item');
    this.todos.push(item);
  }

  deleteAll() {
    this.todos = [];
  }

  get length() {
    return this.todos.length;
  }

  doAsync(cb) {
    setTimeout(cb, 2000, true);
  }
}

module.exports = Todo;
```

Vows 提供了完备的测试方案，但仍然可以用别的断言库将不同测试库的功能混合搭配到一起.另外还有一个叫做 Chai的断言库，它可以代替 assert 模块。

#### **Chai**

Chai 是个流行的断言库，有三个接口：should、expect 和 assert。下面的代码中用到了 assert ，其看起来就像是 Node 自带的 assertion 模块，但它还有用来比较对象、数组和它们的属性的工具。比如用 `typeof`比较类型，用 `property`检查某个对象是否有我们想要的属性。

```javascript
const chai = require('chai');
const assert = chai.assert;
const foo = 'bar';
const tea = { flavors: ['char', 'earl grey', 'pg tips'] };
assert.typeOf(foo, 'string');
assert.equal(foo, 'bar');
assert.lengthOf(foo, 3);
assert.property(tea, 'flavors');
assert.lengthOf(tea.flavors, 3);
```

这个 API 看起来更像是英语句子——声明式风格更冗长，但看起来更加通顺。should 换了种风格：给对象添加属性，这样就不用把断言放在 expect 调用里了

```javascript
const chai = require('chai');
chai.should();
const foo = 'bar';
foo.should.be.a('string');
foo.should.equal('bar');
```

要用哪个接口取决于项目，如果先写测试，并将其作为项目的文档，详细的 expect 和 should 接口很好用。

Chai 有很多插件，包括一些趁手的工具，比如可以用 promise 写测试代码的 chai-as-promised，以及根据统计方法比较数值的 chai-stats。注意，因为 Chai 是断言库，所以要跟 Mocha 这样的测试运行者配合使用。另一个像 Chai 一样的 BDD 断言库是 Should.js。

#### **Should.js**

Should.js 是个断言库，它用类似于 BDD 的风格表示断言，让测试更容易看懂。它的设计初衷是搭配其他测试框架一起用，我们用的例子是编写代码测试一个定制的模块。Should.js 跟其他框架的搭配很容易，因为它只是给 Object.prototype 增加了一个 should属性。这样你就可以写出表达能力更强的断言，比如 user.role.should.equal('admin')，或者 users.should.include('rick')。

比如说，你要编写一个 Node 命令行的小费计算器，在你跟朋友们采用 AA 制付费时，要用它计算每个人该付多少钱。你希望非程序员朋友也能看懂测试计算逻辑的代码，以免他们怀疑你
耍诈。

初始化并安装依赖

```shell
npm init -y
npm i --save-dev should
```

新建并编辑 index.js ，放入实现核心功能的代码，具体来说，消费计算器包含四个辅助函数：

- addPercentageToEach——按给定的百分比加大数组中的所有数值
- sum——计算数组中所有数值的和
- percentFormat——将要显示的值变成百分比格式
- dollarFormat——将要显示的值变成美元格式

分账时计算小费的逻辑

```javascript
// index.js
// 按百分比加大数组元素汇总的数值
exports.addPercentageToEach = (prices, percentage) => {
  return prices.map(total => {
    total = parseFloat(total);
    return total + (total * percentage)
  });
}

// 计算数组中所有数值的和
exports.sum = (prices) => {
  return prices.reduce((currentSum, currentValue) => {
    return parseFloat(currentSum) + parseFloat(currentValue);
  })
}

// 将要显示的值变成百分比格式
exports.percentFormat = (percentage) => {
  return parseFloat(percentage) * 100 + '%';
}

//将要显示的值变成美元格式
exports.dollarFormat = (number) => {
  return `$${parseFloat(number).toFixed(2)}`;
}
```

分账时测试计算小费的逻辑

```javascript
// test/tips.js
const tips = require('..');
const should = require('should');
const tax = 0.12;
const tip = 0.15;
const prices = [10, 20];

const pricesWithTipAndTax = tips.addPercentageToEach(prices, tip + tax);
pricesWithTipAndTax[0].should.equal(12.7);
pricesWithTipAndTax[1].should.equal(25.4);

const totalAmount = tips.sum(pricesWithTipAndTax).toFixed(2);
totalAmount.should.equal('38.10');

const totalAmountAsCurrency = tips.dollarFormat(totalAmount);
totalAmountAsCurrency.should.equal('$38.10');

const tipAsPercent = tips.percentFormat(tip);
tipAsPercent.should.equal('15%');
```

修改 package.json 中的脚本

```json
  "scripts": {
    "test": "node test/tips.js"
  }
```

运行脚本，npm test 如果一切正常，不会输出什么。因为断言没有抛出错误。

Should.js 支持很多种断言，从使用正则表达式的到检查对象属性的全都有，其可以对程序生成的数据和对象进行全面的检查。处理断言库，测试时还经常要用到探测器、存根和模拟对象。

#### **Sinon.JS 的探测器和存根**

模拟对象和存根库是测试工具箱里的工具。我们写单元测试是为了把系统的各个组成部分隔离单独测试，但有时候这很难做到。比如测试图片缩放的代码时，如果不想读写真正的图片文件，该怎么写测试代码，代码中不应该有避开文件系统读写的特殊测试分支，因为那样就不是真正的测试了。在这种情况下，需要创建文件系统的功能的存根。可以用存根来代替尚未准备好的依赖项，这有助于实现真正的 TDD。

下面介绍如何 用 SinonJS 编写测试探测器、存根和模拟对象。

初始化，安装依赖

```shell
npm init -y
npm i --save-dev sinon 
```

接着创建要测试的样例文件，这个例子里用了一个简单的 JSON 键/值数据库，创建一个文件系统 API 的存根，这样就不用在文件系统里创建真正的文件了。我们也可以像下面一样只测试数据库代码，避免处理文件的代码

```javascript
// db.js
const fs = require('fs');
class Database {
  constructor(filename) {
    this.filename = filename;
    this.data = {};
  }
  save(cb) {
    fs.writeFile(this.filename, JSON.stringify(this.data), cb);
  }
  insert(key, value) {
    this.data[key] = value;
  }
}
module.exports = Database;
```

**探测器**

有时候我们只想看看某个方法是否调用了，探测器最适合做这个，借助它的 API ，可以将某个方法替换成能够进行断言的东西。比如用 Sinon 的方法替身 spy 模拟 db.js 中的 fs.writeFile:

```javascript
sinon.spy(fs.'writeFile');
```

测试完成后，可以用 fs.writeFile.restore() 复原原来的方法。

在 Mocha 之类的测试库中使用时，应该把这些操作放在 beforeEach 和 afterEach 中，下面是探测器用法的完整例子

```javascript
// spies.js
const sinon = require('sinon');
const Database = require('./db');
const fs = require('fs');
const database = new Database('./sample.json');

// ➊ 替换fs 方法
const fsWriteFileSpy = sinon.spy(fs,'writeFile');
const saveDone = sinon.spy();

database.insert('name','Charles Dickens');
database.save(saveDone);

// ➋ 断言 writeFile 只调用一次
sinon.assert.calledOnce(fsWriteFileSpy);
// ➌ 恢复原来的方法
fs.writeFile.restore();
```

设置好探测器后➊运行要测试的代码。然后调用 sinon.assert➋确保方法被调用了。恢复原来的方法➌。这项测试中的恢复操作不是必须的，但恢复之前改变的方法是最佳实践。

**存根**

有时候要控制代码流程，比如在测试错误处理代码时，需要强制执行错误处理分支。可以用前面的例子改写，用存根取代探测器，以便执行 writeFile 的回调函数。注意，我们并不是想调用真正的 writeFile.只是希望能运行那个回调函数。下面的是例子。

```javascript
// stub.js
const sinon = require('sinon');
const Database = require('./db');
const fs = require('fs');
const database = new Database('./sample.json');

// 用自己的函数替代 writeFile
const stub = sinon.stub(fs,'writeFile',(file,data,cb)=>{
  cb();
});

const saveDone = sinon.spy();

database.insert('name','Charles Dickens');
database.save(saveDone);

// 断言 writeFile 被调用了
sinon.assert.calledOnce(stub);
// 断言 database.save 的回调被调用了
sinon.assert.calledOnce(saveDone);

fs.writeFile.restore();
```

在测试有大量用户提供的函数、回调和 promise 的代码时，把存根和探测器结合起来用是最理想的办法。看过这些单元测试工具后，我们该去探究另一种风格的测试了：功能测试。

### 功能测试

在大多数 Web 项目的开发中，进行功能测试的办法都是按用户指定的需求列表驱动浏览器执行操作，然后检查各种 DOM 变化。比如在做一个内容管理系统时，要对图片库的上传功能做功能测试，也就是上传一张图片，然后检查是不是添加上了，在检查是不是加到了正确的图片列表中。

Node 中做功能测试的工具很多，它们大体上可以分成两类：无头测试和基于浏览器的测试。无头测试基本上都是用 PhantomJS 之类的工具提供一个可以在终端里使用的浏览器环境，也有轻便的一些方案会用 Cheerio 和 JSDOM 这样的库。基于浏览器的测试用 Selenium之类的浏览器自动化工具，通过脚本驱动真正的浏览器。两种测试方式用的底层工具都是一样的，可以根据自己的偏好用 Mocha、Jasmine 甚至Cucumber 驱动 Selenium 测试自己的程序。

Selenium 是基于 Java 的浏览器自动化库，在特定语言驱动器的帮助下，可以连接到  Selenium  服务上。用真正的服务器跑测试用例。

https://blog.csdn.net/huilan_same/article/details/51896672



### 总结

- 编写单元测试需要 Mocha 这样的测试运行器
- Node 自带了一个 断言库 assert
- 还有其他断言库，包括 Chai 和 Should.js 
- 如果不想运行某些代码，比如网络请求，可以用 SinonJS
- SinonJS 也可以探测代码，验证某个函数或方法是不是运行了。
- 通过利用脚本驱动真正的浏览器，可以用 Selenium 编写浏览器测试

