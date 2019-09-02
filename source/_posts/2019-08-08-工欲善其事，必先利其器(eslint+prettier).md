---
title: 工欲善其事，必先利其器(eslint+prettier)
date: 2019-08-08  09:30:54
tags: 规范
categories: 
	- 规范
---

## 工欲善其事，必先利其器(eslint+prettier)

### Prettier

[官网](https://prettier.io/docs/en/index.html)

#### 什么叫Prettier？

Prettier是一个固定的代码格式化程序，支持：

- JavaScript，包括[ES2017](https://github.com/tc39/proposals/blob/master/finished-proposals.md)
- [JSX](https://facebook.github.io/jsx/)
- [Angular](https://angular.io/)
- [Vue](https://vuejs.org/)
- [Flow](https://flow.org/)
- [TS](https://www.typescriptlang.org/)
- CSS，[Less](http://lesscss.org/)和[SCSS](http://sass-lang.com/)
- [HTML](https://en.wikipedia.org/wiki/HTML)
- [JSON](http://json.org/)
- [GraphQL](http://graphql.org/)
- [Markdown](http://commonmark.org/)，包括[GFM](https://github.github.com/gfm/)和[MDX](https://mdxjs.com/)
- [YAML](http://yaml.org/)

#### 安装

```shell
# yarn
yarn add prettier --dev --exact
# 全局
yarn global add prettier

# npm
npm install --save-dev --save-exact prettier
# 全局
npm install --global prettier
```

### 基本配置

```json
{
  // 排版宽度,即每行最大宽度，默认值是80
  "printWidth":100,
  // 制表符宽度，每个层级缩进几个空格，默认值为2
  "tabWidth": 2,
  // 是否使用 tab 替代 space 为单位缩进，默认值为false
  "useTabs": false,
  // 分号，句尾是否自动补全分号，默认为true
  "semi": true,
  // 启用双引号，不启用单引号,默认为true
  "singleQuote": true,
  // 在 JSX 文件中使用单引号替代双引号，默认为 false
  "jsxSingleQuote": true,
  // 为多行数组的非末尾添加逗号（单行数组不需要逗号），数值：none(不添加逗号)、es5(在ES5中生效的逗号，对象数组等)，all(任何可以添加逗号的地方)
  "trailingComma": "es5",
  // 括号空格，在对象字面量和括号之间添加空格，默认为 true
  "bracketSpacing": true,
  // 将多行 JSX 元素的 > 放置于最后一行的末尾，而非换行。默认为 false
  "jsxBracketSameLine": false,
  // 箭头函数圆括号，默认为 avoid(在可以消除的情况下，消除括号)，always(一直保留括号)
  "arrowParens": "avoid",
  "overrides": [
    {
      "files": ".prettierrc",
      "options": { "parser": "json" }
    }
  ]
}

```

### Eslint

### 代码规范

#### 变量命名

命名必须传递足够的信息。`fetchUserInfoAsync`比`getData`更加具体

**命名基础**

### 半自动构建

#### husky

Husky 可以阻止无效的 `git commit`、`git push`以及其他woff行为

```shell
npm install husky --save-dev
```

#### lint-staged

针对暂存的git文件运行linters并且不要让💩滑入你的代码库！

```shell
npm install lint-staged --save-dev
```

#### commitlint

规范 `commit message`,便于自动生成 `CHANGELOG`

```shell
npm install commitlint @commitlint/cli @commitlint/config-conventional --save-dev
```

配置：

```javascript
// commitlint.config.js
module.exports = {
  extends: ["@commitlint/config-conventional"],
  rules: {
    "type-enum": [
      2,
      "always",
      ["feat", "fix", "docs", "style", "refactor", "test", "chore", "revert"]
    ],
    "subject-full-stop": [0, "never"],
    "subject-case": [0, "never"]
  }
}
/** 
 * feat：新功能（feature）
 * fix：修补bug
 * docs：文档（documentation）
 * style： 格式（不影响代码运行的变动）
 * refactor：重构（即不是新增功能，也不是修改bug的代码变动）
 * test：增加测试
 * chore：构建过程或辅助工具的变动
*/
```

#### conventional-changelog

自动生成 `CHANGELOG`

```shell
npm install conventional-changelog conventional-changelog-cli --save-dev
```

#### package.json

```json
{
  "scripts": {
    "start": "concurrently \"node scripts/start.js\" \"npm run mock\"",
    "build": "node scripts/build.js",
    "test": "node scripts/test.js",
    "eslint": "eslint --fix **/*.js",
    "prettier": "prettier --write ./src/**/**/**/*",
    "mock": "json-server --watch db.json --port 3004",
    "changelog": "conventional-changelog -p angular -i CHANGELOG.md -s -r 0"
  },
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged",
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
    }
  },
  "lint-staged": {
    "src/**/*.{jsx,txs,ts,js,json,css,md}": [
      "prettier --write ./src/**/**/**/*",
      "eslint --fix **/*.js",
      "git add"
    ]
  },
  "config": {
    "commitizen": {
      "path": "cz-customizable"
    }
  },    
}
```

#### commit&push

```shell
# 当有新的改变
git add .

# 提交，输入不规范的提交信息，先校验代码， 提示不规范，并且不通过
git commit -m "test"

# 输入规范信息，规范自行百度，也可以从 commitlint.config.js中看出。运行钩子自动prettier，接着运行 eslint,没有报错则 git add，并开始校验提交信息是否规范，无误后顺利提交
git commit -m "feat: add semi-automatic construction"
husky > pre-commit (node v10.15.3)
Stashing changes... [started]
Stashing changes... [skipped]
→ No partially staged files found...
Running tasks... [started]
Running tasks for src/**/*.{jsx,txs,ts,js,json,css,md} [started]
prettier --write ./src/**/**/**/* [started]
prettier --write ./src/**/**/**/* [completed]
eslint --fix **/*.js [started]
eslint --fix **/*.js [completed]
git add [started]
git add [completed]
Running tasks for src/**/*.{jsx,txs,ts,js,json,css,md} [completed]
Running tasks... [completed]
husky > commit-msg (node v10.15.3)
[master 9afdd76] test: lint-staged
 1 file changed, 1 insertion(+)
 
# 推送到 orgin
git push

# 生成 changelog
npm run changelog
> conventional-changelog -p angular -i CHANGELOG.md -s -r 0

```



### CSS样式顺序

相关属性应该为一组，可以以下面的样式为编写顺序

- Positioning
- Box model
- Typographic
- Visual

```javascript
.declaration-order{
    /* Positioning */
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    z-index: 100;    
    /* Box model */
    display:block;
    box-sizing: border-box;
    width: 100px;
    height: 100px;
    padding: 10px;
    border: 1px solid #666;
    border-radius: 5px;
    margin: 10px;
    float: right;
    overflow: hidden;
    /* Typographic */
    font: normal 12px 'Helvetica Neue',sans-serif;
    line-height: 1.5;
    text-align: center;
    /* Visual */
    background-color: #333;
    color: #fff;
    opacity: 0.8;
    /* Other */
    cursor: pointer;
}
```



### 参考链接：

1. [编写「可读」代码的实践](http://taobaofed.org/blog/2017/01/05/writing-readable-code/)
2. [前端开发规范之命名规范、html规范、css规范、js规范](https://imweb.io/topic/5a5cc753a192c3b460fce3fc)
3. [Prettier](https://prettier.io/docs/en/install.html)
4. [eslint](http://eslint.cn/docs/rules/)
5. [airbnb规范](https://github.com/airbnb/javascript)
6. [eslint-plugin-react](https://github.com/yannickcr/eslint-plugin-react/tree/1aab93d0e3e91f73accdfc3a59afbdaf97c0d08e/docs/rules)
7. [eslint-config-alloy](https://github.com/AlloyTeam/eslint-config-alloy)
8. [eslint-plugin-jsx-a11y](https://github.com/evcohen/eslint-plugin-jsx-a11y/tree/master/docs/rules)
9. [husky](https://www.npmjs.com/package/husky)
10. [lint-staged](https://www.npmjs.com/package/lint-staged)
11. [commitlint](https://commitlint.js.org/#/concepts-commit-conventions)

