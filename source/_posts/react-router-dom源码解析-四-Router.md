---
title: react-router-dom源码解析(四) -Router
date: 2019-01-26 09:48:36
tags: react
categories: frontend
---

紧跟上一篇的步伐, 这一章主要学习react-router-dom的`Router`的思想, 和实现自己的`Router`组件


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

Router是整个`react-router-dom`体系中最重要的一环, `BrowserRouter`和`HashRouter`都依赖其, `Router`组件中做的事情主要有以下几个方面:

- context创建
- 路由监听

## 三、细说

------

### 3.1 context创建

首先看一下`Router.js`的源码:

```ts
class Router extends React.Component {
  render() {
    return (
      <RouterContext.Provider
        children={this.props.children || null}
        value={{
          history: this.props.history,
          loaction: this.state.location,
          match: Router.computeRootMatch(this.state.location.pathname),
          staticContext: this.props.staticContext,  // 这个可以不用理, 好像没多大用
        }}
      />
    );
  }
}
```

由于`Router.js`中用到了`RouterContext`这个小东西, 再把目光转向同目录下的`RouterContext.js`文件, 大致是这样的:

```ts
const context = createNamedContext('Router');
```

通过`React`提供的`createContext`API, 在`RouterContext`中轻松的创建了`context`, `context`静态函数, 分别是`Provider`和`Consumer`, 将我们常用的三大API: `history` & `location` & `match`作为value传递给该context的`Provider`, ***这也是`Router`为什么要作为最外层组件的原因***.

### 3.2 路由监听

如果说上面讲的`context`是整个`react-router-dom`体系的基石的话, 那么`history`监听则是整个体系的`powered by`, 也就是`动力来源`.

先来看一下主要的源码:

```ts
this.unlisten = props.history.listen((location) => {
  if(this._isMounted) {
    this.setState({ location });
  } else {
    this._pendingLocation = location;
  }
});
```

这里的`listen`函数并不稀奇, 这是`history`组件官方为开发者提供的钩子函数, 其本质上是对`window.onpopstate`做了一层封装. 因此, react应用中的所有`url变化`, 反射到Router组件中的`监听函数`, 从而将需要被渲染的`components`传递给`Route`组件, `Route`组件通过:

```ts
props.path === props.location.pathname
```

判断是否渲染该组件...

## 四、实践

------

多说无益, 但做无妨. 了解了`react-router-dom`的基本思想, 我们继续完善自己的库.

首先, 就是完成我们的`Router.tsx`:

```ts
// src/yyg-react-router-dom/components/Router.tsx
import * as React from 'react';
import {
  History,
  Location,
} from 'history';

import RouterContext from './RouterContext';
import {
  IStaticMatchParams,
} from '../types';

export interface IRouterProps {
  children: React.ReactNode;
  history: History;
};
interface IRouterState {
  match: IStaticMatchParams,
  location: Location,
};


const Router = React.memo<IRouterProps>((
  props: IRouterProps,
): JSX.Element => {
  const [state, setState] = React.useState<IRouterState>({
    match: {
      params: {},
      path: '/',
      url: '/',
      isExact: false,
    },
    location: {
      key: '',
      pathname: '/',
      search: '',
      hash: '',
      state: {},
    },
  });

  // ** 设置初始的location & match **
  React.useEffect(() => {
    setState({
      match: {
        ...state.match,
        isExact: props.history.location.pathname === '/',
      },
      location: props.history.location,
    });
  }, [props.history]);

  React.useEffect(() => {
    // ** 监听url **
    const unListen = props.history.listen((location) => {
      // ** 设置新的location **
      setState({
        ...state,
        location,
      })
    });

    return () => {
      unListen();
    };
  }, []);

  return (
    <RouterContext.Provider
      children={props.children}
      value={{
        ...state,
        history: props.history,
      }}
    />
  );
});

export default Router;
```

接着创建我们的`RouterContext`, 值得注意的是:

> **PS**: react-router-dom官方使用的是`create-react-context`这个库, 我们了解一下就行了, 我们可以自己创建.

```ts
// src/yyg-react-router-dom/components/RouterContext.tsx
import * as React from 'react';

function createRouterContext(
  defaultValue = {},
) {
  return React.createContext(defaultValue);
}

export default createRouterContext();
```

另外, 值得注意的是:

> **PS**: 在书写`.tsx`的时候, 最好将公共的types定义提取出来, 这样将可以将上述`match`对象的接口定义在`types.tsx`中, 方便后续融合到`*.d.ts`中发布...

```tsx
// src/yyg-react-router-dom/types.tsx
...
export interface IStaticMatchParams {
  params: {
    [key: string]: string,
  };
  isExact: boolean;
  path: string;
  url: string;
}
...
```

基本上到这里, Router组件的相关内容已经搞定了, 下面该做一下测试来验证我们的组件..

## 五、测试

------

实践完自己的`react-router-dom`库之后, 需要作一个简单的测试, 验证是否能预期工作...

这里, 由于Route组件需要直接使用`context.consumer`, 所以, 我们就地取材, 使用同目录下的`Route.tsx`来test, 在`Route`组件中写入我们自己的测试内容:

```ts
// src/yyg-react-router-dom/components/Route.tsx
import * as React from 'react';

import RouterContext from './RouterContext';


export interface IRouteProps { };

const Route = ((
  props: IRouteProps,
): JSX.Element => {
  return (
    <RouterContext.Consumer>
      {
        (context) => {
          console.log(context);   // 输出

          return <div />
        }
      }
    </RouterContext.Consumer>
  );
});

export default Route;
```

完成之后, 在`App.tsx`中引入我们的Route组件, 像这样:

```ts
// src/App.tsx

import * as React from 'react';

import {
  BrowserRouter,
  Route,
} from './yyg-react-router-dom/index';


export interface IAppProps { };

const App = React.memo<IAppProps>((
  props: IAppProps,
): JSX.Element => {
  return (
    <div>
      <BrowserRouter>
        Route
      </BrowserRouter>

      // 这里会出现一个问题, 马上会解决
      <Route />
    </div>
  );

});

export default App;
```

完成上述步骤之后, 我们`npm start`启动服务器, console栏中可以看到如下输出:

![测试context输出](https://oos.blog.yyge.top/2019/1/26/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E5%9B%9B%29%20-Router/images/1.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

我们可以看到, 这里输出了一个空对象, 当然这也就是我们为`RouterContext`设置的默认值了. 出现这一步, 代表我们已经打通了`context`传递的流程. 接下来, 我们还得测试**url的变化, 是否能反映到Router中的context, 然后又能否通过context的`Provider`分发给对应的`Consumer`**

这里, 有个小方法, 我们可以将`history`对象挂在到`window`上, 这样可以方便的在chrome中调试了. 打开`BrowserRouter.tsx`组件, 在创建好`browserHistory`对象之后, 同时将其挂载到`window`上:

```ts
// src/yyg-react-router-dom/components/BrowserRouter.tsx

...
const browserHistory: History = createBrowserHistory(props);
// 挂在到window
Reflect.set(window, 'browserHistory', browserHistory);
...
```

接着, 打开chrome的console栏, 输入`browserRouter`, 出现如图所示就代表已经成功了...

![console栏browserRouter测试挂载](https://oos.blog.yyge.top/2019/1/26/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E5%9B%9B%29%20-Router/images/2.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

然后, 命令行中输入`browserRouter.push('/duan')`, 可以看到url地址栏已经变化了, 但是..... console栏没有输出我们的`Route.tsx`中的`context`, 按理说, 我们已经在`Router`中监听了url的变化, 而这种变化会即时反映到`Context.Provider`中, 但是这里并不是我们预期的那样...原因在哪里呢???

带着这个问题, 我们再次打开`App.tsx`, 我们发现, 此时的

![Route和BrowserRouter并不是父子关系](https://oos.blog.yyge.top/2019/1/26/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E5%9B%9B%29%20-Router/images/3.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

`Route`和`BrowserRouter`并不是父子关系, 也就是说`Consume`和`Provider`是分开的, 这也是问题所在. 将`<Route>`组件放置在`<BrowserRouter>内`. 保存打开chrome, console中输入`browserRouter.push('/zhaoyang')`, 这时候, 可以清楚的看到:

![测试成功](https://oos.blog.yyge.top/2019/1/26/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E5%9B%9B%29%20-Router/images/4.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

每一次url的变化, `Route`组件都能正常的接收到这种改变, 到这里, 我们的测试步骤就完成了. 当然, 还有一些错误处理, 后续可以再完善...

## 六、源码

------

源码地址: [点我](https://github.com/ddzy/yyg-react-router-dom)

## 七、总结

------

这一篇主要学习了`Router`组件的工作流程, 下一篇将会看看`Route`, 同样也是非常重要的一个组件. 最后, 附上一张流程图, 对这一节作一个总结:

![流程图](https://oos.blog.yyge.top/2019/1/26/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E5%9B%9B%29%20-Router/images/5.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)