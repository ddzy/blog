---
title: react之禅系列之——fiber.tag重要类型汇总
date: 2019-07-17 19:49:21
tags: [react, js]
categories: [frontend]
---

`fiber`上的`tag`属性极其重要, `react`在源码中也标注的很明确. 在后续的大循环进行更新每个节点(`beginWork`)时, `react`根据`fiber.tag`来对不同的组件进行更新.


<!-- more -->


## 更新

------

### [2019-7-17]

- Initial release

## 前言

------

之前已经记录过了`Fiber`对象的基本属性:

https://github.com/ddzy/react-reading-sources/issues/4

其中, `tag`属性及其重要, react在源码中也标注的很明确. 在后续的大循环进行更新每个节点(`beginWork`)时, react根据`fiber.tag`来对不同的组件进行更新

## 分析

------

简单标注下几个常用的`tag`

```js
// 函数组件
export const FunctionComponent = 0;
// 类组件
export const ClassComponent = 1;
// RootFiber
export const HostRoot = 3;
// 诸如`div`、`p`等原生DOM节点
export const HostComponent = 5;
// React.Fragment, 不会渲染出真实DOM节点
export const Fragment = 7;
export const Mode = 8;
export const ContextConsumer = 9;
export const ContextProvider = 10;
export const ForwardRef = 11;
export const Profiler = 12;
// 与LazyComponent配合使用, 异步组件
export const SuspenseComponent = 13;
export const MemoComponent = 14;
export const SimpleMemoComponent = 15;
// 与SuspenseComponent配合使用, 异步组件
export const LazyComponent = 16;
export const IncompleteClassComponent = 17;
export const DehydratedSuspenseComponent = 18;
export const EventComponent = 19;
export const EventTarget = 20;
```