---
title: match.path和match.url的爱恨情仇
date: 2019-02-07 15:38:34
tags: react
categories: frontend
---

`react-router-dom`中的`match`是个很重要的点, 而`match`中的`url`和`path`又很难区分, 今天来看一下...


<!-- more -->


## **前言**

------

在之前写毕设的过程中, 一直在用`match`这个对象, 但是对于

- match.path
- match.url

这两个东西总是分不清, 很困惑...

在`Route`的源码分析章节, 对这两者有了简单的了解, 但并不是很深刻. 今天下定决心, 彻底搞清楚这两个参数.

## **分析**

------

### **字面量**

首先通过字面量来分析两者区别:

> 何为字面量?

这里的字面量就是`path`和`url`两者的中文语义, 分别是这样的:

|      |      |
| ---- | ---- |
| url  | 网址 |
| path | 路径 |

哎, 瞎猫遇上死耗子, 通过中文并不能看出任何差别. 那么, 接着[往下看](#官网解释)

### **官网解释**

打开[react-router官网](https://reacttraining.com/react-router/web/api/match), 看到`react-router`官方对两者的解释:

- path - (string) The path pattern used to match. Useful for building nested <Route>s
- url - (string) The matched portion of the URL. Useful for building nested <Link>s

google翻译一下:

- path - 用于匹配的路径模式, 通常用来构建嵌套的`Route`.
- url - URL的匹配模式, 通常用来构建嵌套`Link`.

🆗, 这是官方的解释, 好像并没有什么卵用? 相信没有看过源码的话, 看到这个肯定是一脸懵逼? 继续[往下看](#个人解读):

### **个人解读**

#### **match.path**

首先, 贴一张`Gayhub`上`react-router`的源码部分截图:

![react-router部分源码](https://oos.blog.yyge.top/2019/2/7/match.path%E5%92%8Cmatch.url%E7%9A%84%E7%88%B1%E6%81%A8%E6%83%85%E4%BB%87/images/1.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

通过遍历`paths`数组`path`, 看到`黄色标记`部分, 直接将符合条件的`path`赋值给`match`, 那么, 可以轻松的得出这样的结论:

> match.path === Route.props.path

也就是说, `match`的`path`参数, 其实就是将匹配成功的Route的`path`props原封不动的传递过去.

OK, 可能有点晦涩, 看一个例子:

```ts
  // 扮演 `location.pathname`
  const pathname = '/user/duan/profile/19980808';

  // 对应的 `嵌套路由`
  <BrowserRouter>
    <Switch>
      <Route path="/user/:name/profile/:secret" component={xxx}>
    <Switch>
  <BrowserRouter>
```

那么此时, `match.path`就等于`/user/:name/profile/:secret`.

#### **match.url**

同样, 引用上面的截图中的部分代码:

```ts
  ...

  const { regexp, keys } = compilePath(path, {
    end: exact,
    strict,
    sensitive
  });
  const match = regexp.exec(pathname);

  ...

  const [url, ...values] = match;
  const isExact = pathname === url;

  ...

  return {
    url: path === "/" && url === "" ? "/" : url,
  };
```

通过调用`path-to-regexp`库, 将`path`转化为`RegExp`对象, 并调用`regexp.exec()`方法, 得到一个`execMatch`, 长这样:

![exec到的match对象](https://oos.blog.yyge.top/2019/2/7/match.path%E5%92%8Cmatch.url%E7%9A%84%E7%88%B1%E6%81%A8%E6%83%85%E4%BB%87/images/2.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

该对象的第一个参数, 就是匹配后的`url`, 由此可以得出:

> url = path.match(regexp)[0]

## **结论**

经过上面的要点分析, 可以很轻松的看出来:

- match.path只是一种匹配模式, 就是告诉你`根据这种模式`去匹配.
- match.url则是`match.path`执行精确匹配后的具体路径

一般情况下:

- match.path = Route.props.path
- match.url = location.pathname