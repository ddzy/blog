---
title: react-router-dom源码解析(五) -Route
date: 2019-01-27 13:29:03
tags: react
categories: frontend
---

react-router-dom源码解析系列第五篇 - Route, 一起来学习`Route`组件的思想和继续完善我们自己的`react-router`库.


<!-- more -->


## 一、更新

------

### [2019-4-21]

#### Changed

- 改进文章排版

## 二、前言

------

上一篇主要学习了`react-router-dom`中`Router`组件的基本思想和简单实现, 我们可以发现, `Router`作为整个体系的`Powered by`-动力来源, 起着至关重要的作用. 而这一篇将要学习的`Route`组件, 是整个体系中的`执行者`:

> **PS**: 给你一个`path`, 你拿去, 把对应的`component`render出来.

由此可见, `Route`同样是多么的重要.

## 三、细说

------

### 3.1 前置知识

花了几分钟时间, 画了一张脑图, 大概是这一篇文章的大致脉络😰...

![文章大致脉络](https://oos.blog.yyge.top/2019/1/27/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%BA%94%29%20-Route/images/3.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

### 3.2 path-to-regexp

学习`Route`组件源码之前, 先来看一下这个库:

`react-router-dom`官方再源码中引用了这个库, 库的地址也可以在[这里](https://github.com/pillarjs/path-to-regexp)找到. 见名闻其意 —— path-to-regexp, 顾名思义, 就是将path, 例如`url`转化为不同模式的`RegExp`, 同时也提供了一些配置项供我们使用.

来看一下path-to-regexp库提供的这个方法:

```ts
const path = '/user/:id/profile/:secret';
const keys: pathToRegexp.Key[] = [];
const regexp: RegExp = pathToRegexp(path, keys, {});

console.log({ path, keys, regexp });
```

这是官方提供的例子, 我们在控制台打印可以看到如下结果:

![path-to-regexp示例](https://oos.blog.yyge.top/2019/1/27/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%BA%94%29%20-Route/images/1.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

我们可以看到, `path-to-regexp`根据我们输入的`path`路径, 以及`keys`空数组, 生成了对应的`regexp`对象, 以及保存着url参数的数组. 那么我们可以思考一下, 我们能否将这个小例子, 引申到`react-router-dom`中呢? 答案是肯定的.

先来捋一下`Route`组件的设计思路:

1. 接收到带有{ path, sensitive, exact, ... }的props
2. 判断props是否具有`Switch`组件已经计算好的的`match`对象
  - 有的话, 执行下一步`render`
  - 没有的话, 计算`match`
3. 根据props的{ children, component, render }执行`render`

再将目光转向上面提到的例子, 我们可否将例子里的`path`当作`Route`的`path`props? 接着第一个例子, 抛砖引玉, 我们再来看一个:

```ts
const path = '/user/:id/profile/:secret';
const keys: pathToRegexp.Key[] = [];
const pathname = '/user/19980808/profile/duan';

const regexp: RegExp = pathToRegexp(path, keys, {});
const result = pathname.match(regexp);

console.log(result);
```

可以看到打印出了如下结果:

![例子2](https://oos.blog.yyge.top/2019/1/27/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%BA%94%29%20-Route/images/2.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

这里, 我增加了`pathname`常量, 根据例子一中生成的`regexp`的`match`方法, 来生成一个捕获之后的对象. 生成的数组的前三项分别是`精确匹配后的url`, `:id`参数, `:secret`参数. 得出这个结果, 我们可以整合一下, 能否例子二中的`pathname`当作`props.location.pathname`?

`Route`的`path`props根据`path-to-reg`生成一个`regexp`对象, 该`regexp`根据`porps.location.pathname`生成捕获到的对象, 该数组对象中具有匹配之后的`pathname`, 以及`path`中携带的参数.

### 3.3 源码分析

根据前置知识的分析, 了解到其实`Route`组件是引用了`path-to-regexp`这个库, 可以说一切的核心都是围绕这个库展开的, 下面来看一看具体的源码...

首先来看一下:

```ts
// src/yyg-react-router-dom/components/Route.js
const match = this.props.computedMatch
  ? this.props.computedMatch // <Switch> already computed the match for us
    : this.props.path
      ? matchPath(location.pathname, this.props)
      : context.match;
```

这一段代码刚好印证了之前提到了`Route`组件设计思路的第二步, 当然目前`Switch`组件的源码还没看... 其中可以看到, 当`props.computedMatch`不存在的话, 就会执行`matchPath`这个method. 接着, 我们进入到`matchPath`这个函数内部, 也就是`Route`同目录下的`matchPath.js`看一看...

```ts
// src/yyg-react-router-dom/components/matchPath.js
function matchPath(pathname, options = {}) {
  ...
  ...
}
```

可以看到, matchPath函数接受两个参数:

- pathname
  - 也就是对应的`context.location.pathname`
- options
  - Route接收到的`props` —— { path, sensitive, exact, strict }

可以说, 余下的一切都是围绕这两个参数展开的...

接着再往下看:

```ts
// src/yyg-react-router-dom/components/matchPath.js
const { path, exact = false, strict = false, sensitive = false } = options;

const paths = [].concat(path);
```

由于`path`可以将单个string, 也可以接收string[]数组作为值, 所以这里将`path`统一转化为数组进行操作.

```ts
// src/yyg-react-router-dom/components/matchPath.js
return paths.reduce((matched, path) => {
  if(matched) {
    return matched;
  }
}, null);
```

使用了`reduce`方法, 通过遍历`path`数组, 如果迭代的`match`对象存在, 也就是说匹配到了一个`path`, 则直接返回.

紧接着来看:

```ts
// src/yyg-react-router-dom/components/matchPath.js
const { regexp, keys } = compilePath(path, {
  end: exact, // 精确匹配, path === pathname
  strict,  // 是否忽略url后的斜杠/
  sensitive, // 是否忽略大小写
});
```

这里, 也是**重点**: 通过执行一个名叫`compilePath`方法, 传递了相关的props参数, 接收到了{ regexp, keys }两个返回值, 是不是很熟悉? 没错, 和我们的`例子一`中的是一样的, 将`path`转化成了对应的`regexp`, 将`path`中携带的参数提取到了`keys`数组中.

顺藤摸瓜, 来到`compilePath()`函数:

```ts
// src/yyg-react-router-dom/components/matchPatch.js
function compilePath(path, options) {
  ...
  ...
}
```

该函数作为连接`path-to-regexp`的桥梁, 主要任务是通过传入的`options`配置项 ,将`path`转化为`regexp` ,该函数同样接收两个参数:

- path 需要转化的路径
- options 配置项
    - sensitive 是否区分大小写
    - strict 是否忽略路径后的斜杠
    - end 是否精确匹配, 在做嵌套`Route`的时候非常有用, 本人被坑过🙂🙂🙂...

接着下一步:

```ts
// src/yyg-react-router-dom/components/Route.js
const cacheKey = '`${options.end}${options.strict}${options.sensitive}`';
const pathCache = cache[cacheKey] || (cache[cacheKey] = {});

if (pathCache[path]) return pathCache[path];

......
......

if (cacheCount < cacheLimit) {
  pathCache[path] = result;
  cacheCount++;
}
```

其中, 用到了几个全局常量对象, 这里就不做标注了.

***如上所示, 其实是对`path-to-regexp`处理的结果进行了缓存, 由于`options`的参数是可选的, 所以这时对其进行缓存处理, 每一次计算`regexp`都会查询`cache`中有无缓存, 有利于优化性能***. 这也是看源码值得学习的地方, 理解作者思路, 学习大牛写法🙂...

最后, 和我们之前写的例子一样:

```ts
const keys = [];
const regexp = pathToRegexp(path, keys, options);
const result = { regexp, keys };

return result;
```

将处理后的`regexp`返回给调用者, 供调用者使用.

分析完`compilePath`方法之后, 回到`matchPath`:

```ts
// src/yyg-react-router-dom/components/matchPath.js
const { regexp, keys } = compilePath(path, {
  end: exact,
  strict,
  sensitive,
});
// const match = pathname.match(regexp);
// 两者都可, 任选其一
const match = regexp.exec(pathname);

// 匹配值为null, 直接return
if(!match) {
  return null;
}

const [url, ...values] = match;
```

上面的源码中, 接收到了`compilePath`的返回值, 通过调用`regexp`正则对象的`exec`方法获取匹配值, 然后通过解构赋值, 将match匹配到的url, 也就是第一个参数赋值给了`url`, 该`url`日后将为`Link`组件享用. 余下的参数统一赋值给了`rest`参数, 方便接下来的`params`赋值操作...

最后一步, 返回整个`match`对象, 包括:

```ts
// src/yyg-react-router-dom/components/matchPath.js
...
const isExact = pathname === url;

return {
  path,
  url,
  isExact,
  params: keys.reduce((memo, key, index) => {
    memo[key.name] = values[index];
    return memo;
  }, {}),
};
...
```

`keys`数组中保存着`path`中的参数, `values`中保存着对应的参数值, 通过`reduce`迭代, 最终返回`match`对象...


------

终于分析完了`matchPath`这个方法, 我们回到`Route.js`文件, 继续之前的进度, 接着往下看:

```ts
// src/yyg-react-router-dom/components/Route.js
const props = { ...context, location, match };

let { children, component, render } = this.props;

// Preact uses an empty array as children by
// default, so use null if that's the case.
if (Array.isArray(children) && children.length === 0) {
  children = null;
}

if(typeof children === "function") {
  children = children(props);

  if(children === undefined) {
    if(__DEV__) {
      const { path } = this.props;

      warning(...);
    }

    children = null;
  }
}
```

上述代码中, 通过es6的解构赋值, 将原`context`中的`location`和`match`对象替换成处理过的location和match. 接着, 由于`Route`组件可能接收到{ children, render, component }这三个props, 所以这里分别做了处理... 当然, 上面有一段兼容`preact`框架的代码, 这里就不管他了😀...

最后, 到了`render`环节, 继续来看源码:

```ts
// src/yyg-react-router-dom/components/Route.js
return (
  <RouterContext.Provider value={props}>
      children && !isEmptyChildren(children)
        ? children
        : props.match
          ? component
            ? React.createElement(component, props)
            : render
              ? render(props)
              : null
          : null
  </RouterContext.Provider>
);
```

在`render`中, 将更新之后的`context`提供给`Provider`, 然后再根据`children` -> `component` -> `render`的顺序依次判断是否据此渲染.

其中有使用到了一个名为`isEmptyChildren`的方法, 在`vscode`内追踪到该函数:

```ts
// 判断props.children是否为空?
function isEmptyChildren(children) {
  return React.Children.count(children) === 0;
}
```

我们发现, 该函数内部其实是调用了`React`的静态方法, 所以, 不是那么神秘.

到了这里, 基本上`Route`组件的源码已经看完了, 剩下了一些`Error`或者`Warning`处理, 可以当作参考. 下面该继续来完善自己的`react-router-dom`库...

## 四、实践

------

> **PS**: 隔了一天写的, 接着上面的源码简单分析, 今天主要是完善一下自己的`yyg-react-router-dom库`

话不多说, 顺着昨天的思路: `Route`通过传入的`props`计算`match`, 在计算`match`的过程中引用了`path-to-regexp`npm包, 用来转化`path`为`RegExp`, 从而与`location.pathname`作匹配, 得出计算之后的`match`对象.

紧接着, 由于`Route`的props有三种渲染方式-`children` | `render` | `component`, 所以这里做了一些判断, 同时, 加入了对`preact`的兼容处理以及`错误处理`.

那么, 回顾了一下昨天的思路, 就开始动手coding了...

### 4.1 定义interface

> **PS**: 由于之前已经写入了基本结构, 所以首先要约束所需的`props`

```ts
// src/yyg-react-router-dom/components/route.tsx
...
export interface IRouteProps {
  location?: Location;
  component?: React.ComponentType<IStaticRouteComponentParams> | React.ComponentType<any>;
  render?: ((props: IStaticRouteComponentParams) => React.ReactNode);
  children?: ((props: IStaticRouteComponentParams) => React.ReactNode) | React.ReactNode;
  path?: string | string[];
  exact?: boolean;
  sensitive?: boolean;
  strict?: boolean;
};
...
```

### 4.2 拆分处理函数

本来, 这里是不想写的. 但是, 由于比较重要, 也当作是加深记忆把, 所以还是记录下来...

> **PS**: 在`render`中, 尽量减少逻辑代码

也就是说, 尽量将`render`中的处理逻辑拆分成单个函数来处理, 这个, 我觉得对于整个组件的整洁是非常重要的...

了解了这点, 我们可以这样 —— 将`render`中的处理逻辑提取至`handleProcess`这个处理函数:

```ts
return (
  <RouterContext.Consumer>
    {(context: IStaticRouteComponentParams) => {
      return handleProcess(context);
    }}
  </RouterContext.Consumer>
);
```

我们在组件中生成一个名为`handleProcess`的主进程处理函数, 该函数接收一个名为`context`的参数.

```ts
  function handleProcess(
    context: IStaticRouteComponentParams,
  ): JSX.Element {
    // 将任务分发至 多个子进程
    const xxx1 = handleXXX1();
    const xxx2 = handleXXX2(xxx1);
    const xxx3 = handleXXX3(xxx2);

    return (
      <div />
    );
  }
```

这样一来, 整个代码的可读性就好了很多, 同时美观程度也大有改观.

### 4.3 计算location

`location`的来源有两个方面:

- context.location
- props.location

当然, `props`的优先级肯定是要比`context`的高了, 所以对其作一下简单的处理:

```ts
// src/yyg-react-router-dom/components/route.tsx
return props.location
  ? props.location
  : context.location
    ? context.location
    : History.createLocation('/');
```

### 4.4 计算match

主函数接收到计算后的`location`之后, 将其传递给`matchProcess`, 也就是这一步 —— `计算match`:

```ts
// src/yyg-react-router-dom/components/route.tsx
const location = handleLocationProcess(context);
const match = handleMatchProcess(context, location);
```

对应的`match`处理主函数接收到`context`和`location`, 进行一系列判断, 如果父组件不是`Switch`并且`props.path`存在, 则进入`computeMatchProcess`处理函数:

```ts
// src/yyg-react-router-dom/components/route.tsx
return props.computedMatch
  ? props.computedMatch
  : props.path
    ? computeMatchProcess(location)
    : context.match;
```

在computeMatchProcess中, 我们要做的就是对`path`进行`迭代`处理, 将处理后的`match`对象返回, 该`match`可能为`null`或者`键值对`. 这样, 就可以在`render`阶段根据`match`的值存在与否来决定是否渲染对应的`components`.

注意, `computedMatchProcess`中引入了`computePath`辅助函数, 主要是为了代码分割, 避免一个函数内部的代码过于繁杂...

### 4.5 融合

计算完相应的`match`对象, 在我们自己的主进程处理函数中, 现在已经获取到了计算完毕的`location`和`match`对象. 这一步, 就是将原来的`context`中的对应的location和match替换为计算之后的, 也就是融合.

```ts
const composedContext = {...context, match, location};
```

### 4.6 重置Provider

将融合后的context, 作为新的value传递给`Provider`

```ts
return (
  <RouterContext.Provider
    value={composedContext}
  >
    { ...render... }
  </RouterContext.Provider>
);
```

### 4.7 render

根据不同的途径 —— `render` | `component` | `children`, 来进行渲染:

> **PS**: `react-router-dom`官方采用的渲染权重是: children > component > render

在这里, 我们自己玩, 所以不必在意这个东西

```ts
return (
  <RouterContext.Provider
    value={composedContext}
  >
    props.children
      ? props.children
        ? typeof props.children === 'function'
          ? props.children(composedContext as IStaticRouteComponentParams)
          : props.children
        : props.children
      : match
        ? props.component
          ? React.createElement(props.component, composedContext)
          : props.render
            ? props.render(composedContext as IStaticRouteComponentParams)
            : null
        : null
  </RouterContext.Provider>
);
```

## 五、测试

------

经过上面的实践环节, `Route`组件基本完成了, 现在应该做个测试...

在`src/test`下新建一个测试组件, 内容随意

在`src/App.tsx`中引入我们自己的`Route`和测试组件:

```ts
// src/App.tsx
import {
  BrowserRouter,
  Route,
} from "./yyg-react-router-dom/index";
import Test from "./test/One.tsx";
```

在`App`中写入我们的测试代码:

```ts
// src/App.tsx
const App = React.memo<IAppProps>((
  props: IAppProps,
): JSX.Element => {
  return (
    <div>
      <BrowserRouter>
        <Route
          path={["/user/:name/profile/:secret"]}
          exact
          component={One}
        />
      </BrowserRouter>
    </div>
  );
});
```

打开浏览器, 可以看到, 我们自己的组件可以正常渲染, 并且测试组件`Test.tsx`中也正常打印出了`context`对象, 像这样:

![动图展示测试Route组件](https://oos.blog.yyge.top/2019/1/27/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%BA%94%29%20-Route/images/4.gif?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

测试成功`exact`和`component`配置项之后, 再来试一下`render`.

在`src/test/`下新建`Three.tsx`, 作为我们的第三个测试组件, 当然, 内容随意, 能展示出具体内容就ok:

```ts
// src/test/Three.tsx
...
return (
  <h1>Three pages&</h1>
);
...
```

然后, 在`src/App.tsx`中引入该组件, 并且更改测试代码:

```ts
// src/App.tsx
  import Three from ./test/Three;

  return (
    <div>
      <BrowserRouter>
        <Route
          path={["/user/:name/profile/:secret"]}
          render={() => (
            <div>
              <Route path="/user/:name/profile/:secret" exact component={One} />
              <Route path="/user/:name/profile/:secret/test" exact component={Three} />
            </div>
          )}
        />
      </BrowserRouter>
    </div>
  );
```

可以看到, 这个开发中经常用到的结构, 完成之后, 重新编译, 在`CentBrowser`中作测试:

首先, 当url为`/user/:name/profile/:secret`时, 也就是匹配到了component为`One`的`Route`, 理所当然, 渲染这个组件:

![测试render嵌套Route](https://oos.blog.yyge.top/2019/1/27/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%BA%94%29%20-Route/images/5.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

可以看到, 结果是预期所示的, 没有问题, 那么, 接着将`url`改为`Three`组件对应的`path`, 再来看:

![测试render嵌套Route](https://oos.blog.yyge.top/2019/1/27/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%BA%94%29%20-Route/images/6.gif?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

结果是`Three`组件的内容被完全渲染了出来, 这也证实, 我封装的组件系没有啥大问题的📍

同理, 测试其他配置项都是没有问题的, 这里由于文章篇幅太长, 就不一一展示了...

## 六、源码

------

源码地址: [点我](https://github.com/ddzy/yyg-react-router-dom)

## 七、总结

------

写了两天, 终于搞定了这篇文章, 还是收获颇丰的: 通过`Route`组件, 掌握了内部的运作机理, 比如`match`计算, location计算等, 又通过计算match, 初步了解了`path-to-regexp`这个库.

> **PS**: 不静下心看看源码, 永远不知道你和别人查了多远

学无止境, 下一篇文章继续阅读学习`Switch`组件的源码, 再会!