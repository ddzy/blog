---
title: react-router-dom源码解析(七) -Link
date: 2019-02-05 08:35:57
tags: react
categories: frontend
---

Happy New Year!


<!-- more -->


## 一、更新

------

### [2019-4-21]

#### Changed

- 改进文章格式

## 二、前言

------

给自己拜个早年, 祝自己在2019年:

- 前端技术大有长进
- 吃饭多多
- 找个好工作

![过年好, 疯狂暗示](https://oos.blog.yyge.top/2019/2/5/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%B8%83%29%20-Link/images/1.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

早上7点起来, 朦朦胧胧, 坐在电脑桌前, 一脸懵逼...

想了想, 今天还是继续之前的源码学习吧, 上一次学习了`Switch`组件, 今天主要是来看一下`Link`.

## 三、细说

------

`Link`组件被放置在`packages`下的`react-router-dom`包中, 与`react-router`做了隔离,

> **PS**: 虽然不知道作者这么做的意图, 但是佩服就对了

但这些都是次要的, 打开`Link.js`文件, 预览一下大致的结构, 可以看到, 源码行数与`Switch`并无两样, 仔细看, `Link`类中, 其实只有一个`handleClick`处理方法. 所以, 按理说, 并不应该很难理解:

![疯狂暗示_动图](https://oos.blog.yyge.top/2019/2/5/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%B8%83%29%20-Link/images/2.gif?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

正式开始分析之前, 先来回想一下使用思路? 平时用到最多的可能就是`Link`了, 一般都是做`Menu`点击跳转之类的, 与之功能类似的有一个`history.push`, 两者功能差不多. 与`Link`对应的还有一个`NavLink`, 可以自定义`active`样式, 这个下一篇再说吧.

> **PS**: `Link`点击跳转, `url`改变, 对应`模块`展现, 一气呵成.

带着这个想法, 开始阅读源码:

首先, 可以看到:

```ts
return (
  <RouterContext.Consumer>
    {...}
  <RouterContext.Consumer>
);
```

还是熟悉的做法, 接收`context`做内部操作.

接着:

```ts
const location =
  typeof to === "string"
    ? createLocation(to, null, null, context.location)
    : to;
const href = location ? context.history.createHref(location) : "";
```

分为两步

- 计算location
- 根据location计算href

根据`props`传递的`to`参数, 如果是`string`, 则调用`history.push`直接跳转, 如果是`object`, 则使用`history.createHref`进行包装, 并返回相应的`path`.

接着, 再往下看:

```ts
return (
  <a
    ...
    onClick={event => this.handleClick(event, context.history)}
    ...
  >
);
```

`Link`的核心处理函数, `vscode`中`Ctrl+MouseLeft`直接进入`handleClick`函数内部, 可以看到:

```ts
  handleClick(event, history) {
    if (this.props.onClick) this.props.onClick(event);

    if (
      !event.defaultPrevented && // onClick prevented default
      event.button === 0 && // ignore everything but left clicks
      (!this.props.target || this.props.target === "_self") && // let browser handle "target=_blank" etc.
      !isModifiedEvent(event) // ignore clicks with modifier keys
    ) {
      event.preventDefault();

      const method = this.props.replace ? history.replace : history.push;

      method(this.props.to);
    }
  }
```

这是源码所示, 下面将相关注解直接挂载到对应源码上:

```ts
  handleClick(event, history) {
    if (this.props.onClick) this.props.onClick(event);

    if (
      // ** 如果默认事件已被阻止 **
      // ** 避免innerRef直接操作a链接 **
      !event.defaultPrevented &&
      // ** 只允许鼠标左键点击 **
      event.button === 0 &&
      // ** 排除当target=_blank时, 打开新页面, 破坏单页原则 **
      (!this.props.target || this.props.target === "_self") &&
      // ** 避免使用键盘快捷键+鼠标的操作 **
      // ** 形如 Ctrl + MouseLeft, 会在新页面打开链接 **
      !isModifiedEvent(event)
    ) {
      event.preventDefault();

      // ** 通过 replace 判断是否将当前location替换为新的location **
      // ** 避免堆栈 **
      const method = this.props.replace ? history.replace : history.push;

      method(this.props.to);
    }
  }
```

执行完这一步操作, 基本上`Link`的核心已经学习完毕了, 感觉很简单, 这也许是整个`react-router-dom`体系中最简单的一个(哦, 还有下一篇将要分析的`NavLink`).

> **PS**: 多说不做非君子

下面, 接着来完善自己的`yyg-react-router-dom`库.

## 四、实践

------

这里只写个大概, 完整源码参考: [这里](#源码)

### 4.1 定义interface

> **PS**: 非常重要的一步, 本能反应

打开`react-router`的[官网](https://reacttraining.com/react-router/web/api/Link), 可以看到`Link`的所需props, 🆗, 现在来定义它:

```ts
// src/yyg-react-router-dom/components/Link.tsx
export interface ILinkProps {
  to?: string | LocationDescriptorObject<{
    [key: string]: any,
  }>;
  replace?: boolean;
  innerRef?: (node: React.Ref<HTMLLinkElement>) => void;
  children?: React.ReactChildren;
};
```

接着, 按照以前的分布策略, 将处理`context`的函数提取出来, 再分别进行不同的处理操作, 大致结构是这样的:

```ts
// src/yyg-react-router-dom/components/Link.tsx
function handleProcess(
  context: IStaticRouterComponentParams,
): JSX.Element {
  // ** do any **
}

<RouterContext.Consumer>
  {
    (context) => handleProcess(context as IStaticRouterComponentParams)
  }
<RouterContext.Consumer>
```

这里只是再三提醒自己注意代码规范, 俗话说:

> **PS**: 好记性不如烂笔头

做的多了, 自然而然就熟练了.

接着, 在`handleProcess`函数中, 要进行的则是计算`href`, 以及处理`a`的`onClick`, 所以按照之前的思路, 根据`props.to`的值进行`location`的计算, 得到`createHref`的返回值, 这个返回值就是`path`, 所以:

```ts
// src/yyg-react-router-dom/components/Link.tsx
...
    const href = (
      to
        ? _isString(to)
          ? context.history.createHref(createLocation(to, null, '', context.location))
          : context.history.createHref(to as LocationDescriptorObject<{
              [key: string]: any,
            }>)
        : ''
    ) as string;

    return (
      <a
        {...others}
        href={href}
        onClick={(e) => handleLinkClick(e, context, href, replace)}
      >{children}</a>
    );

...
```

计算完`href`, 就要处理`click`了, 将其提取至两一个function, 并传入所需的参数:

```ts
  function handleLinkClick(
    e: React.MouseEvent<HTMLAnchorElement>,
    context: IStaticRouteComponentParams,
    href: string,
    replace?: boolean,
  ): void {
    // ** 省略n个字 **
  }
```

最后, 做整合, 优化一下代码, 开始[测试环节](#测试)

## 五、测试

------

简单的写完自己的代码, 简单起见, 对`to`和`replace`这两个常用参数作测试:

在`App.tsx`中, 修改测试路由:

```ts
// src/App.tsx
  return (
    <div>
      <BrowserRouter>
        <Switch>
          <Route
            path={"/test"}
            render={() => (
              <Switch>
                <Route exact path="/test" component={One} />
                <Route exact path="/test/home" component={Three} />
                <Route exact path="/test/product" component={Four} />
              </Switch>
            )}
          />
        </Switch>
      </BrowserRouter>
    </div>
  );
```

进入到`One.tsx`中, 添加一些内容, 当然是`Link`组件了:

```ts
// src/test/One.tsx
  return (
    <div>
      <h1>Page One</h1>
      <ul>
        <li>
          <Link
            to="/test/home"
          >Home</Link>
        </li>
        <li>
          <Link
            to={{
              pathname: '/test/product',
              state: {
                id: '19980808',
                name: 'duan',
              },
            }}
          >Product</Link>
        </li>
      </ul>
    </div>
  );
```

测试代码并不是固定的, 自己随意测试即可...

之后, 打开`CentBrowser`, 可以看到:

![entbrowser测试](https://oos.blog.yyge.top/2019/2/5/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%B8%83%29%20-Link/images/3.gif?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

通过点击使用`Link`组件, 浏览器url可以正常改变, 并且对应的组件可以正常渲染, 充分证明我们自己封装的Link没有问题.

## 六、源码

------

完整源码已上传至 [Gayhub](https://github.com/ddzy/yyg-react-router-dom)

## 七、总结

------

最后, 以一张脑图来结束今天的学习:

![思维脑图](https://oos.blog.yyge.top/2019/2/5/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%B8%83%29%20-Link/images/4.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)