---
title: The Road to learn React Part4
date: 2018-06-26 19:00:45
tags: react
categories: 
	- react相关
---
The Road to learn React书籍学习笔记(第四章)
===========

[![The Road to learn React 代码](https://img.shields.io/badge/%E4%BB%A3%E7%A0%81%E8%AF%A6%E6%83%85%E8%A7%81%E6%88%91%E7%9A%84github-%E7%82%B9%E6%88%91-blue.svg)](https://github.com/LbhFront-end/react-pratice/tree/master/my-first-react-app)

高级React组件
-----------------

本章将重点介绍高级 React 组件的实现。我们将了解什么是高阶组件以及如何实现它们。此外，我们还将深入探讨 React 中更高级的主题，并用它实现复杂的交互功能。

引用 `DOM` 元素
-------------------

有时候我们需要在 `React` 与 `DOM` 节点进行交互。 `ref` 属性可以让我们访问元素中的一个节点，通常，访问 `DOM` 节点是 `React` 中的一个反模式，因为我们应该遵循它的声明式编程和单向数据流。当我们引入第一搜索组件时，就已经了解刀这些问题，但是在某些情况下，我们仍然要访问 `DOM` 节点。官方文档提到了三种情况  
使用 `DOM API`(`focus`事件，媒体播放等)  
调用命令式 `DOM` 节点动画  
与需要 `DOM` 节点的第三方库集成

让我们通过 Search 组件这个例子看一下。当应用程序第一次渲染时，`input` 字段应该被聚焦。这是需要访问 `DOM API` 的一种用例。本章将展示渲染时聚焦 input 字段是如何工作的，但由于这个功能对于应用程序并不是很有用，所以我们将在本章之后省略这些更改。尽管如此，你仍然可以为自己的应用程序保留它。

通常，`无状态组件`和 `ES6 类组件`中都可以使用 `ref` 属性。在聚焦 `input` 字段的用例中，我们就需要一个生命周期方法。这就是为什么接下来会先在 ES6 类组件中展示如何使用 `ref` 属性。

第一步是将无状态组件重构为 ES6 类组件。

```react
class Search extends Component {
    render() {
        const {
        value,
        onChange,
        onSubmit,
        children
        } = this.props;

        return (
        <form onSubmit={onSubmit}>
            <input
            type="text"
            value={value}
            onChange={onChange}
            />
            <button type="submit">
            {children}
            </button>
        </form>
        );
    }
}
```

ES6 类组件的this对象可以帮助我们通过 `ref` 属性引用 `DOM` 节点。

```react
class Search extends Component {
    render() {
        const {
        value,
        onChange,
        onSubmit,
        children
        } = this.props;

        return (
        <form onSubmit={onSubmit}>
            <input
            type="text"
            value={value}
            onChange={onChange}
            ref={(node) => { this.input = node; }}
            />
            <button type="submit">
            {children}
            </button>
        </form>
        );
    }
}
```

现在，你可以通过使用 `this` 对象、适当的生命周期方法和 `DOM API` 在组件挂载的时候来聚焦 `input` 字段

```react
lass Search extends Component {
    componentDidMount() {
        if(this.input) {
        this.input.focus();
        }
    }

    render() {
        const {
        value,
        onChange,
        onSubmit,
        children
        } = this.props;

        return (
        <form onSubmit={onSubmit}>
            <input
            type="text"
            value={value}
            onChange={onChange}
            ref={(node) => { this.input = node; }}
            />
            <button type="submit">
            {children}
            </button>
        </form>
        );
    }
}
```

当应用程序渲染时，input 字段应该被聚焦。这就是ref属性的基本用法。

但是我们怎样在没有this对象的无状态组件中访问ref属性呢？接下来我们在无状态组件中演示。

```react
const Search = ({
    value,
    onChange,
    onSubmit,
    children
    }) => {
    let input;
    return (
        <form onSubmit={onSubmit}>
        <input
            type="text"
            value={value}
            onChange={onChange}
            ref={(node) => input = node}
        />
        <button type="submit">
            {children}
        </button>
        </form>
    );
}
```

加载
-------------------

现在让我们回到应用程序。当向 `Hacker News API` 发起搜索请求时，我们想要显示一个加载指示符。

请求是异步的，此时应该向用户展示某些事情即将发生的某种反馈。让我们在 `src／App.js` 中定义一个可重用的 `Loading` 组件。

>const Loading = () =>  
> \<div>Loading ...\</div>

现在你将需要一个属性来存储加载状态。根据加载状态可以决定稍后是否显示所加载的组件。

src/App.js

```react
class App extends Component {
    constructor(props) {
        super(props);

    this.state = {
        results: null,
        searchKey: '',
        searchTerm: DEFAULT_QUERY,
        error: null,
        isLoading: false,
    };

    ...
}

...

}
```

isLoading 的初始值是 false。在 App 组件挂载完成之前，无需加载任何东西。

当发起请求时，将加载状态 (isLoading) 设置为 true。最终，请求会成功，那时可以将加载状态 (isLoading) 设置为 false。

src/App.js

```react
class App extends Component {

...

setSearchTopStories(result) {
        ...

        this.setState({
        results: {
            ...results,
            [searchKey]: { hits: updatedHits, page }
        },
        isLoading: false
        });
    }

    fetchSearchTopStories(searchTerm, page = 0) {
        this.setState({ isLoading: true });

        fetch(`${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${searchTerm}&${PARAM_PAGE}\
    ${page}&${PARAM_HPP}${DEFAULT_HPP}`)
        .then(response => response.json())
        .then(result => this.setSearchTopStories(result))
        .catch(e => this.setState({ error: e }));
    }

    ...

}
```

最后一步，我们将在应用程序中使用 Loading 组件。基于加载状态 (isLoading) 的条件来决定渲染 Loading 组件或 Button 组件。后者为一个用于获取更多数据的按钮。

src/App.js

```react
class App extends Component {

    ...

    render() {
        const {
        searchTerm,
        results,
        searchKey,
        error,
        isLoading
        } = this.state;

        ...

        return (
        <div className="page">
            ...
            <div className="interactions">
            { isLoading
                ? <Loading />
                : <Button
                onClick={() => this.fetchSearchTopStories(searchKey, page + 1)}>
                More
                </Button>
            }
            </div>
        </div>
        );
    }
}
```

由于我们在componentDidMount（）中发起请求，Loading 组件会在应用程序启动的时候显示。此时，因为列表是空的，所以不显示 Table 组件。当响应数据从 Hacker News API 返回时，返回的数据会通过 Table 组件显示出来，加载状态 (isLoading) 设置为 false，然后 Loading 组件消失。同时，出现了可以获取更多的数据的“More”按钮。一旦点击按钮，获取更多的数据，该按钮将消失，加载组件会重新出现。

高阶组件
-------------------

高阶组件（HOC）是 React 中的一个高级概念。HOC 与高阶函数是等价的。它接受任何输入 - 多数时候是一个组件，也可以是可选参数 - 并返回一个组件作为输出。返回的组件是输入组件的增强版本，并且可以在JSX中使用。

HOC可用于不同的情况，比如：准备属性，管理状态或更改组件的表示形式。其中一种情况是将 HOC 用于帮助实现条件渲染。想象一下现在有一个 List 组件，由于列表可以为空或无，那么它可以渲染一个列表或者什么也不渲染。当没有列表的时候，HOC 可以屏蔽掉这个不显示任何内容的列表。另一方面，这个简单的 List 组件不再需要关心列表存不存在，它只关心渲染列表。

高级排序
-------------------

我们已经实现了客户端和服务器端搜索交互。因为我们已经拥了 Table 组件，所以增强 Table 组件的交互性是有意义的。那接下来，我们为 Table 组件加入根据列标题进行排序的功能如何？

你自己写一个排序函数，但是一般这种情况，我个人更喜欢使用第三方工具库。lodash就是这些工具库之一，当然你也可以选择适用于你的任何第三方库。让我们安装 lodash 并使用。



参考链接：https://leanpub.com/the-road-to-learn-react-chinese