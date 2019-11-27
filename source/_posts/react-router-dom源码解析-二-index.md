---
title: react-router-dom源码解析(二) -index
date: 2019-01-24 15:05:30
tags: react
categories: frontend
---

`react-router-dom`源码学习系列第二篇, 主要讲解如何对`react-router-dom`的目录结构进行规划, 毕竟看源码只是了解作者的想法, 如何用自己的想法写出来, 我想这才是最重要的


<!-- more -->


## 一、更新

------

### [2019-4-21]

#### Fixed

- 修复图片链接失效问题

#### Changed

- 改进文章格式

## 二、目录规划

------

上一篇文章主要说了`react-router-dom`的源代码目录的总体架构, 了解到了其基本的文件划分, 这一篇文章也是对我们自己的源代码目录进行规划. 毕竟`多说不练非君子`...

使用`create-react-app`快速搭建一个开发环境, 我习惯用tsx进行书写, 所以该命令大致是这样的:

```bash
create-react-app yyg-react-router-dom --scripts-version=react-scripts-ts
```

然后`cd`到项目文件夹下, 创建`src`并在其下创建同名目录, 代表我们自己编写的`react-router-dom`, 并在之中新建react-router-dom对应的各个组件:

![react-router-dom组件结构](https://oos.blog.yyge.top/2019/1/24/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%BA%8C%29%20-index/images/1.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

在任意一个组件文件中, 书写入如下代码:

```jsx
import * as React from 'react';

export interface IHashRouterProps { };

const HashRouter = React.memo<IHashRouterProps>((
  props: IHashRouterProps,
): JSX.Element => {
  return (
    <React.Fragment />
  );
});

export default HashRouter;
```

同理, 将上述模板分别copy到其他剩余的七个组件中, 并将组件内部的函数名称修改为组件对应的名称就可以了.

**注意**: 这里只是做一个测试而已, 同理, 将上述模板分别copy到其他剩余的七个组件中, 并且修改为组件对应的名称.

![我们穷鬼是这样的啦](https://oos.blog.yyge.top/2019/1/24/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%BA%8C%29%20-index/images/2.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

## 三、index导入

------

最后, 在`index.tsx`中导入我们自己的`react-router-dom`组件, 作统一导出, 一方面是为了美观, 更重要的是我们的组件规范.

在`index.tsx`中:

```ts
// src/yyg-react-router-dom/index.tsx

import BrowserRouter from './components/BrowserRouter';
import HashRouter from './components/HashRouter';
import Link from './components/Link';
import NavLink from './components/NavLink';
import Redirect from './components/Redirect';
import Route from './components/Route';
import Switch from './components/Switch';
import withRouter from './components/withRouter';

export {
  BrowserRouter,
  HashRouter,
  Link,
  NavLink,
  Redirect,
  Route,
  Switch,
  withRouter,
};
```

作完这一步, 我们的项目基本结构基本搭建好了, 后续的可能会用到的工具函数, 后面再说...

下一篇, 会学习`BrowserRouter`组件的源码, 搞起!