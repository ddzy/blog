---
title: react-router-dom源码解析(三) -BrowserRouter
date: 2019-01-26 08:12:29
tags: react
categories: frontend
---

个人react-router-dom源码学习系列第三篇 —— BrowserRouter组件


<!-- more -->


## 一、更新

------

### [2019-4-21]

#### Fixed

- 修复图片链接失效问题

#### Changed

- 完善文章格式

## 二、前言

------

上一篇主要学习了官方的`react-router-dom`源码目录结构, 并且创建了我们自己的源码目录体系. 这一篇文章主要是一起学习`BrowserRouter`的源码, 并且使用`.tsx`完善自己的`react-router-dom`.

## 三、分析

------

`BrowserRouter`使用`history`库的`createBrowserHistory`API, 通过传递给其的相关`props`, 创建history对象, 作为`props`传递给`react-router`的`Router`组件, 源码是这样的:

```ts
...
// 创建history, 将history作为props传递至Router
history = createHistory(this.props);

render() {
  return <Router history={this.history} children={this.props.children} />;
}
...
```

这里, 我们可以看到, 其实整个BrowserRouter源码里面, 最重要的就是如上的所示代码.

这里要特别注意的是, 源码中`BrowserRouter`和`HashRouter`都只是对`Router`组件作了一层包装, 真正的路由顶层处理其实是在`Router`组件, 所以`BrowserRouter`似乎看起来不是很难懂...

## 四、实践

------

下面, 我们可以根据`react-router-dom`的思想, 来书写我们的`BrowserRouter`组件...

```ts
// src/yyg-react-router-dom/components/BrowserRouter.tsx

import * as React from 'react';
import {
  History,
  createBrowserHistory,
} from 'history';

import Router from './Router';


export interface IBrowserRouter {
  children: React.ReactNode,

  // ** createBrowserHistory的原汁原味props, 照写即可 **
  basename?: string;
  forceRefresh?: boolean;
  keyLength?: number;
  getUserConfirmation?: (
    message: string,
    callback: (result: boolean) => void,
  ) => void;
};


const BrowserRouter = React.memo<IBrowserRouter>((
  props: IBrowserRouter,
): JSX.Element => {
  const browserHistory: History = createBrowserHistory(props);

  return (
    <Router history={browserHistory} children={props.children}  />
  );
});


export default BrowserRouter;
```

照猫画虎完成了`BrowserRouter`组件, 这时候我们的项目应该是报错的, 因为我们的`Router`组件没有定义相关的`props`, 话不多说, 来到`Router.tsx`, 写入基本的测试代码, 测试对应的`history`API是否正常传递.

```ts
// src/yyg-react-router-dom/components/Router.tsx

import * as React from 'react';
import {
  History,
} from 'history';

export interface IRouterProps {
  children: React.ReactNode;
  history: History;
};


const Router = React.memo<IRouterProps>((
  props: IRouterProps,
): JSX.Element => {

  console.log(props); // { history: {...}, children: 'Route' }

  return (
    <div>Router&</div>
  );
});

export default Router;
```

如果在`Router`组件中正常打印相关的内容, 则说明我们的`BrowserRouter`基本完成了...

## 五、源码

------

源码地址: [点我](https://github.com/ddzy/yyg-react-router-dom)

## 六、总结

------

总而言之, `BrowserRouter`还算简单, 只是做了一层简单的封装, 下一篇主要学习一下`Router`组件, 这也是最重要的组件
!

> **PS**: 每天进步一点点

![最漆黑的那段路, 始终是要自己走完的](https://oos.blog.yyge.top/2019/1/26/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%B8%89%29%20-BrowserRouter/images/1.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)