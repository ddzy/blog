---
title: react-router-dom源码解析(一) -开篇
date: 2019-01-24 09:46:31
tags: react
categories: frontend
---


库用的多了, 自然想了解以下它的原理是怎样的, 这是react-router-dom源码系列的第一篇, 后续会分多个部分来分别研究其中常用的`Browser`, `HashRouter`, `Link`, `NavLink`, `Route`, `Switch`, `Redirect`等组件

<!-- more -->


## 一、更新

------

### [2019-3-24]

#### Removed

- 移除`起源`部分

### [2019-4-21]

#### Fixed

- 修复图片链接失效问题

#### Changed

- 完善文章格式

## 二、准备工作

------

### 2.1 fork

进入`gayhub`, 找到`react-router`, 将其`fork`下来, 项目一级目录大概是这样的:

![react-router一级目录](https://oos.blog.yyge.top/2019/1/24/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%B8%80%29%20-%E5%BC%80%E7%AF%87/images/1.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

### 2.2 精简

`cd`到`react-router/packages/`目录下, 看到里面有四个对应的文件夹, 这个已经很熟悉了, 我们阅读的是`react-router-dom`, 所以只保留如下两个项目文件夹:

![react-router二级packages目录](https://oos.blog.yyge.top/2019/1/24/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%B8%80%29%20-%E5%BC%80%E7%AF%87/images/2.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

> 这里要注意, 源码中react-router和react-router-dom是分开的, 而我们项目中只引用了react-router-dom这一个包, 因为react-router的作者将两者合并为一个包, 方便开发者使用

### 2.3 观摩

`react-router-dom`和`react-router`的目录结构简直一毛一样, 先来看`react-router`, 找到`react-router/modules/` 目录:

![react-router的modules目录](https://oos.blog.yyge.top/2019/1/24/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%B8%80%29%20-%E5%BC%80%E7%AF%87/images/3.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

可以看到一个个熟悉的身影, 这里也就是我们的对应的源码目录, 一切将从这里开始... 同样的, 进入到`react-router-dom/modules`目录:

![react-router-dom的modules目录](https://oos.blog.yyge.top/2019/1/24/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%B8%80%29%20-%E5%BC%80%E7%AF%87/images/4.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

同样也是很熟悉了.

## 三、大致体系

------

其实`react-router-dom`的结构并没有那么复杂, 大致就是下面的这么一个图:

![react-router-dom体系结构](https://oos.blog.yyge.top/2019/1/24/react-router-dom%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%28%E4%B8%80%29%20-%E5%BC%80%E7%AF%87/images/5.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

可以看到, 大名鼎鼎的react-router-dom的底层API都是依靠了`history`这个依赖包, `history`提供了`createBrowserHistory`和`createHashHistory`这两个API供开发者使用. 而react-router-dom的`BrowserRouter`和`HistoryRouter`其实只是对上述提到的两个API进行了一层封装.

## 四、付诸实践

------

源码地址: [点我](https://github.com/ddzy/yyg-react-router-dom)

## 五、总结

------

这篇文章只是对整个流程做了一个概述, 后续的章节将会一一学习各个API.