---
title: react-router-dom源码解析(八) -NavLink
date: 2019-02-07 07:56:05
tags: react
categories: frontend
---

前天学习了`Link`组件源码, 今天再来看一下与之相似的`NavLink`


<!-- more -->


## 一、更新

------

### [2019-4-21]

#### Changed

- 改进文章排版👌

## 二、前言

------

经过上一篇文章[Link](https://blog.yyge.top/2019/02/05/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90-%E4%B8%83-Link)的战火洗礼, 可以了解到, `Link`作为沟通`react-router`和`react`之间的桥梁, 经历`click -> url变化 -> history(onListen) -> Switch -> Route render`的过程, 据此, 有关`Link`的面试题便可迎刃而解.

正式开始分析之前, 先来回想一下我们平时是如何使用`NavLink`的?

> **PS**: 传入对应的`activeStyle`或者`activeClass`, 点击之后, 如果对应的组件成功渲染, 则该`NavLink`的样式也会改变.

带着平时使用的思路去分析源码, 会又事半功倍的效果.

OK, 正式开始对`NavLink`的探索.

## 三、细说

------

先来看一下`NavLink`的大致结构, 可以看到, 其实`NavLink`是一个`functional`组件, `functional`相较于`classes`的优势很多:

- 高度可拓展
- 易于优化重构
- 渲染性能较优

在`react v16.7`之后的`hooks`加持之下, 可用性更是飞步提升.

接着来看一下`NavLink`接收了哪些`props`?

```ts
function NavLink({
  "aria-current": ariaCurrent = "page",
  activeClassName = "active",
  activeStyle,
  className: classNameProp,
  exact,
  isActive: isActiveProp,
  location,
  strict,
  style: styleProp,
  to,
  ...rest
}) {...}
```

对应的props的功能如下表所示:

|                 |                                                                              |
| --------------- | ---------------------------------------------------------------------------- |
| aria-current    | 残障人士专用, 可参考[这里](https://www.w3.org/TR/wai-aria-1.1/#aria-current) |
| activeClassName | 匹配时添加的类名                                                             |
| activeStyle     | 匹配时添加的样式                                                             |
| className       | NavLink的类名, 会附加到Link                                                  |
| exact           | 是否实行完全匹配                                                             |
| strict          | 是否实行严格匹配                                                             |
| isActive        | 自行计算高亮条件                                                                 |
| location        | 与当前`to`进行比较的location, 默认是`context.location`                       |
| style           | 默认样式                                                                     |
| to              | 跳转的url                                                                    |
| ...rest         | 额外参数                                                                     |

接着, 可以看到下面:

```ts
  const path = typeof to === "object" ? to.pathname : to;

  // Regex taken from: https://github.com/pillarjs/path-to-regexp/blob/master/index.js#L202
  const escapedPath = path && path.replace(/([.+*?=^!:${}()[\]|/\\])/g, "\\$1");
```

值得注意的是, 这里的`excapedPath`很奇怪, 一直想不通为什么要进行这个操作? 怀着好奇的心理点开注释的链接, 可以看到:

![excapedPath](https://oos.blog.yyge.top/2019/2/7/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E5%85%AB%29%20-NavLink/images/1.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

该函数会将字符串中的特殊字符进行转义处理, 那么这到底有咩用? 假设有一个特殊的`path`序列是这样的:

```ts
const path: string = '/user\duan/profile/se+cr*et';
```

用户想传递该`path`, 该`path`中含有特殊字符, 那么`escaped`会将转义字符再此进行转义, 便于传递.

接着往下看:

```ts
  return (
    <Route
      path={escapedPath}
      exact={exact}
      strict={strict}
      location={location}
      children={({ location, match }) => {
        const isActive = !!(isActiveProp
          ? isActiveProp(match, location)
          : match);

        const className = isActive
          ? joinClassnames(classNameProp, activeClassName)
          : classNameProp;
        const style = isActive ? { ...styleProp, ...activeStyle } : styleProp;

        return (
          <Link
            aria-current={(isActive && ariaCurrent) || null}
            className={className}
            style={style}
            to={to}
            {...rest}
          />
        );
      }}
    />
  );
```

通过一个`Route`组件包裹, 根据其的`match`判断是否`active`, 然后在`Route`中返回对应的`Link`组件, 将处理后的`rest`等参数传递给`Link`组件.

这一步, 我刚开始看了好几遍都没看懂, 很难理解, 难点在于为什么要用一个`Route`??? 它的本质是为了获取`match`, 根据`match`匹配成功与否, 进行动态添加样式, 理解了这点, 就没啥可说的了.

另外, 还有一种思路 —— 通过获取`Link`的`innerRef`, 然后通过`matchPath`自行比对`location.pathname === to`, 根据判断的结果, 直接操作`DOM`, 这么做也是可以的, 不过值得思考的是, 这不正好违背了`Route`组件的设计原则:

> **PS**: 匹配路径规则, 渲染组件

既然有更专业的`Route`来帮我们作这件事, 何乐而不为呢? 为什么还要再次比对呢? 得不偿失.

🆗, `NavLink`的源码思路解析大致已经完成, 接下来继续完善自己的`yyg-react-router-dom`库.

## 四、实践

------

老样子, 首先定义我们所需的一切`props`, 具体的`props`可以看[这里](https://reacttraining.com/react-router/web/api/NavLink).

```ts
// src/yyg-react-router-dom/components/NavLink.tsx
export interface extends React.HtmlHTMLAttributes<HTMLAnchorElement> {
  to: string | LocationDescriptorObject<{
    [key: string]: any,
  }>;
  activeClassName?: string;
  activeStyle?: React.HtmlHTMLAttributes<HTMLAnchorElement>;
  exact?: boolean;
  strict?: boolean;
  sensitive?: boolean;
  isActive?: (
    data: {
      location: Location,
      match: IStaticMatchParams,
    },
  ) => boolean;
  location?: Location;
  ariaCurrent?: ARIA_CURRENT;
  children?: React.ReactChild;
};
```

接着, 到了下面, 根据判断`match`是否为null来断定path是否匹配:

```ts
  return (
    ...
    <Route
      children={(p) => {
        const isActive = !!(p.match);
        const activeStyles = isActive ? activeStyle : {};
        const activeClassNames = isActive ? activeClassName : '';

        return (
          <Link
            {...rests}
            to={path}
            style={activeStyles}
            className={activeClassNames}
          >{children}</Link>
        );
      }}
    >
  );
```

注意! 上面有一个值得注意的点, 那就是`Route`的`children`和`render`是不同的, 两者的具体差别可以看[这里](https://blog.yyge.top/2019/02/07/%E8%A7%A3%E6%9E%90Route%E4%B8%AD%E7%9A%84render%E5%92%8Cchildren/), 所以, 这里只能使用`children`来渲染.

## 五、测试

------

完成了自己的`yyg-react-router-dom`库, 可以来做一个简单的测试.

> **PS**: 当然, 测试用例是随意的

打开`App.tsx`, 修改`render`中的测试逻辑如下:

```ts
//src/App.tsx
...
  return (
    <div>
      <BrowserRouter>
        <Switch>
          <Route
            path={"/test"}
            component={One}
          />
        </Switch>
      </BrowserRouter>
    </div>
  );
...
```
然后, 在`vscode`的指引下, 进入`One.tsx`组件, 同样修改`render`中的逻辑:

```ts
  return (
    <div>
      <h1>Page One</h1>
      <ul>
        <li>
          <Link
            to="/test/home"
          >
            Home
          </Link>
        </li>
        <li>
          <NavLink
            to={{
              pathname: '/test/product',
              state: {
                id: '19980808',
                name: 'duan',
              },
            }}
            activeStyle={{
              color: 'red',
            }}
          >Product</NavLink>
        </li>
        <li>
          <NavLink
            to="/test/contact"
            activeStyle={{
              color: 'red',
            }}
          >
            Contact
        </NavLink>

        </li>
      </ul>
      <Two render={({ point }) => (
        <div>
          <div>{point.x}</div>
          <div>{point.y}</div>
        </div>
      )} />

      <div>
        <Route exact path="/test/home" component={Three} />
        <Route exact path="/test/product" component={Four} />
        <Route exact path="/test/contact" component={Five} />
      </div>
    </div>
  );
```

可以看到, 总共有三个`锚点`, 第一个为`Link`, 为了与`NavLink`的效果加以区分, 然后在最下方放置需要`router-view`, 也就是需要渲染的组件. 大致结构与在项目中使用的差不多的...

接着, 在浏览器中可以看到效果:

![测试NavLink](https://oos.blog.yyge.top/2019/2/7/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E5%85%AB%29%20-NavLink/images/2.gif?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

点击`Home`按钮, 视图变了, 但是链接颜色没变化? 当然了, 因为这只是普通的`Link`组件, 你想它有啥效果? 接着看下面两个, prefect, 两个锚点样式随着url变化而改变.

此时, 对于`NavLink`的测试工作已经完美结束了

## 六、源码

------

源码已上传, 点[这里](https://github.com/ddzy/yyg-react-router-dom)

## 七、总结

------

> **PS**: 路漫漫其修远兮, 吾必反复求其知

最后, 一张思维脑图来结束今天的源码学习

![NavLink思维图解](https://oos.blog.yyge.top/2019/2/7/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E5%85%AB%29%20-NavLink/images/3.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)