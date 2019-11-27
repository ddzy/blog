---
title: react-router-dom源码解析(六) -Switch
date: 2019-01-30 15:59:07
tags: react
categories: frontend
---

上一篇文章学习了`Route`组件的实现机理, 而体系中的`Switch`组件, 与`Route`密切相关, 所以, 今天来咀嚼一下它.


<!-- more -->


## 一、更新

### [2019-4-21]

#### Changed

- 改进文章排版格式

## 二、前言

------

> **PS**: 无尽的知识, 能让一个沉默的人变得兴奋

一句话开头, 希望能带着这种劲头, 开始今天的`Switch`的学习...

## 三、细说

------

### 3.1 前置知识

经过上一次的`Route`的源码学习, 我们学习到了`Route`组件的设计思路, 即:

![Route回顾](https://oos.blog.yyge.top/2019/1/30/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E5%85%AD%29%20-Switch/images/1.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

`Route`的作用主要是计算`match`, 根据`match`和决定渲染`component`, 那么这篇文章学的`Switch`, 则是:

> **PS**: 计算match, 将计算之后的`computedMatch`传递给`Route`

`Route`组件接收到`computedMatch`之后, 不再去计算`match`, 而是直接进行`render`. 这也印证了`Switch`这个名称, 即为`切换`.

一句话概括:

> **PS**: `Switch`的子组件`Route`只能有一个被渲染出来

----------------------------------------------------------------------------------------------------------------

### 3.2 分割线

中间隔了五天, 由于在`Gayhub`上看到了一个`vscode`的`issue`, 关于在`工作区查找文件`的, 所以有感而发, 花了几天时间写了一个`vscode`插件 —— `File Positioning`, 昨天下午成功发布🐓🐓🐓

### 3.3 源码分析

废话少说, 还是接着上次的进度. 打开`react-router/modules/Switch.js`, 可以看到, `Switch`的源码非常简洁, 好像也并没有什么难的,
保持这种`轻敌`的心态, 一步步来分析:

首先, 可以看到:

```ts
return (
  <RouterContext.Consumer>
    {(context) => {
      ...
      ...
    }}
  </RouterContext.Consumer>
);
```

`Switch`同样作为一个`Consumer` - `消费者`, 接收到`RouterContext`组件.

接着:

```ts
const location = this.props.location || context.location;
```

可以看到, 和`Route`中一样, `Switch`做了同样的操作 —— 计算`location`, 这些都不是重点, 重点在下面这段代码:

```ts
let element, match;

React.Children.forEach(this.props.children, (child) => {
  if(match == null && React.isValidElement(child)) {
    element = child;

    const path = child.props.path || child.props.from;

    match = path
      ? matchPath(location.pathname, {
          ...child.props, path,
        })
      : context.match;
  }

  return match
    ? React.cloneElement(element, {
        location,
        computedMatch: match,
      })
    : null;
});
```

为了更深的理解, 我将注释直接写在`源码内`, 大致是这样的:

```ts
// ** 定义两个变量, 分别存储`子组件`和`match对象` **
// ** 注意, 这里的`element`通常是`Route`组件 **
let element, match;

// ** 调用`React.Children`的静态方法 **
// ** 遍历props.children, 当然如上面所说, **
// ** 这里的children通常是`Route` **
React.Children.forEach(this.props.children, (child) => {

  // ** match不存在 **
  if(match == null && React.isValidElement(child)) {
    element = child;

    // ** 获取到`Route`的props的path参数 **
    const path = child.props.path || child.props.from;

    // ** 计算`match` **
    match = path
      ? matchPath(location.pathname, {
          ...child.props, path,
        })
      : context.match;
  }

  // ** match存在 **
  // ** 克隆`Route`组件, 将`location`和`match`作为新的props替代 **
  return match
    ? React.cloneElement(element, {
        location,
        computedMatch: match,
      })
    : null;
});
```

可以清楚的看到, `Switch`内部同样是做了`matchPath`的操作, 目的就是为了渲染出单一children, 同时, 印证了我们在开发的时候, 使用`Switch`作`路由截取`.

再往下看, 好像已经没内容了? 没错, 太简单了, 下面就开始实践环节.

## 四、实践

------

> **PS**: 好记性不如烂笔头

分析完了源码之后, 接着完善自己的`yyg-react-router-dom`库.

`First and foremost` —— 首先也是最重要的一步, 就是定义`Switch`所需的`props`, 也就是约束`interface`.

打开[react-router官网](https://reacttraining.com/react-router/web/api/Switch), 可以看到, `Switch`接收了两个`props`, 分别是`location`和`children`, 🆗, 来书写`interface`.

```ts
// src/yyg-react-router-dom/components/Switch.tsx
export interface ISwitchProps {
  location?: Location;
  children: React.ReactNode;
};
```

定义完成`interface`, 回忆一下`Route`组件中, 将`context`处理分发到了`handleProcess`函数中, 然后再handleProcess函数中, 分两步计算`location`和依赖于`location`的`computeRoute`, 照猫画虎, 在`render`中:

```ts
// src/yyg-react-router-dom/components/Switch.tsx
return (
  <RouterContext.Consumer>
    {(context) => (
      handleProcess(context as IStaticRouteComponentParams)
    )}
  <RouterContext.Consumer>
);

// ** handleProcess() **
function handleProcess(
  context: IStaticRouteComponentParams,
): JSX.Element {
    // ** 计算location **
    const location = handleComputeLocation(context);
    // ** 计算Route, 即为渲染出来的组件 **
    const node = handleComputeRoute(location);

    ...
    ...
}
```

然后, 在`computeLocation`和`computeRoute`中, 分别进行相关的操作, 具体的代码下面会给出...

**注意点**: 在计算`match`的过程中, 再此用到了`matchPath`这个方法, 所以这里要将`matchPath`提取出来, 以便复用.

因此, 在`utils/utils.tsx`中新建工具组件, 主题内容不变, 只是将两个`match`处理函数抽离出来, 并且改一下参数即可:

```ts
// src/yyg-react-router-dom/utils/utils.tsx
export interface IStaticPathProps {
  path?: string | string[];
  exact?: boolean;
  sensitive?: boolean;
  strict?: boolean;
};


function computeMatchProcess(
  location: Location,
  options: IStaticPathProps,
): IStaticMatchParams | null {}

function computePath(
  path: string,
  options: IStaticPathProps,
): {
  keys: PathToRegExp.Key[],
  reg: RegExp,
} {}
```

## 五、测试

------

写完之后, 再来做一下测试. 为了看到更直观的效果, 可以多创建一个`Four.tsx`:

```ts
// src/test/Four.tsx
import * as React from 'react';


export interface IFourProps { };

const Four = React.memo<IFourProps>((
  props: IFourProps,
): JSX.Element => {
  return (
    <div>
      <h1>Fourth Page&</h1>
    <div>
  );
});

export default Four;
```

然后, 在`App.tsx`中, 引入`Switch.tsx`和`Four.tsx`组件, 在render中新增测试代码:

```ts
  return (
    <div>
      <BrowserRouter>
        <Switch>
          <Route
            path={["/user/:name/profile/:secret"]}
            render={() => (
              <Switch>
                <Route path="/user/:name/profile/:secret" exact component={One} />
                <Route path="/user/:name/profile/:secret/test" exact component={Three} />
                <Route path="/user/:name/profile/:secret/test2" exact component={Four} />
              </Switch>
            )}
          />
        <Switch>
      <BrowserRouter>
    <div>
  );
```

打开浏览器, 可以看到, 此时此刻是空白的, 因为我们现在处于根url下, 分别在url栏中输入上面三个测试地址:

![测试Switch](https://oos.blog.yyge.top/2019/1/30/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E5%85%AD%29%20-Switch/images/2.gif?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

上面的gif, 可以清楚看到是正常渲染的, 进而证明我们自己的思路是正确的.

## 六、源码

------

源码地址: [点我](https://github.com/ddzy/yyg-react-router-dom)

## 七、总结

------

> **PS**: 一张思维脑图结束今天的学习

![脑图总结](https://oos.blog.yyge.top/2019/1/30/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E5%85%AD%29%20-Switch/images/3.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)