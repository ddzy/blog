---
title: react-router-dom源码解析(九) -withRouter
date: 2019-02-08 09:02:18
tags: react
categories: frontend
---


`withRouter`作为一个非常常用的组件, 重要性不言而喻, 而它的底层原理你知道吗? 今天来看一下!


<!-- more -->


## 一、更新

------

### [2019-4-21]

#### Changed

- 改进文章排版格式

## 二、前言

------

看完`流浪地球`, 睡一觉起来继续更新...

不知不觉`react-router-dom`源码分析系列已经写了`9`篇, 历经`15`天寒假, 虽然耗了点实践, 但是总体还是挺值得的.

通过`Router`、`Route`的源码学习, 掌握了`history`流向`react-router-dom`顶层, 通过`history.listen`监听浏览器`location`变化, 触发`setState`更新机制, 更新`RouterContext`. `Route`接收到最新的`context`, 结合`path-to-regexp`库计算出`match`. 同时也掌握了通过`Link`组件的自定义链接跳转机制, `url`变化, 从而触发`listen`钩子, 调度一系列更新.

今天要学习的`withRouter`, 同样非常常用, 它可以帮助我们获取到最新的`路由信息`, 当然也可以执行`更新路由`等操作.

## 三、细说

------

### hoist-non-react-statics

正式开始之前, 先来盘一下`hoist-non-react-statics`这个库.

进入[官网](https://github.com/mridgway/hoist-non-react-statics#hoist-non-react-statics), 看到官方的一段英文解释如下:

![官方解释](https://oos.blog.yyge.top/2019/2/8/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%B9%9D%29%20-withRouter/images/1.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

可以了解到, 该库的作用是解决`HOC`无法传递`静态方法`的问题. 好吧, 以前从没遇到这个问题, 也许是见识短的缘故吧, 今天先简单了解一下这个库, 知道它怎么用, 后续再深入探究其底层原理.

### withRouter

接着看`withRouter`的总体结构:

```ts
import hoistStatics from "hoist-non-react-statics";

function withRouter(Component) {
  ...

  return hoistStatics(C, Component);
}
```

`withRouter`就是一个`HOC`组件, 接收一个源component, 返回增强后的component. 顺藤摸瓜, 看到对应的`C`组件:

```ts
  const C = props => {
    const { wrappedComponentRef, ...remainingProps } = props;

    return (
      <Route
        children={routeComponentProps => (
          <Component
            {...remainingProps}
            {...routeComponentProps}
            ref={wrappedComponentRef}
          >
        )}
      >
    );
  };
```

可以看到, `C`是内部定义的一个`enhancedComponent`, 也就是`增强型`组件, 该组件通过返回一个由`Route`包装过的`Element`, 将`location`、`match`、`history`等`context`作为props传递给`wrappedComponent`.

好像就是这么点啊? 没错, 就是这么简单, `withRouter`就是做了这么一件事情, 并不复杂.

## 四、源码

------

源码在[这里](https://github.com/ddzy/yyg-react-router-dom)

## 五、总结

------

最后, 照例画一张`withRouter`的思维脑图, 毕竟一图胜千言.

![总结](https://oos.blog.yyge.top/2019/2/8/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%B9%9D%29%20-withRouter/images/2.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)