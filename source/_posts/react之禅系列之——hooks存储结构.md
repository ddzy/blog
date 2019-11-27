---
title: react之禅系列之——hooks存储结构
date: 2019-07-17 19:58:31
tags: [react, js]
categories: [frontend]
---

`hook`作为`FunctionComponent`最重要的机制, 当然要了解一下它是如何存储的.


<!-- more -->


## 更新

------

### [2019-7-17]

- Initial release

## 前言

------

在分析[hooks的运行机制](https://github.com/ddzy/react-reading-sources/issues/11)过程中, 我通过一个简单的案例——如何使用`hooks`? 来引申出在页面渲染阶段, `hooks`是如何执行的.

当然, `hooks`的执行分为两个阶段:

- _mount_ 首次渲染阶段
- _update_ 后续更新阶段

本篇笔记将记录有关:

- hooks是如何存储的
- useState-hooks的基本结构

附上一篇笔记的链接:

- [hooks运行机制](https://github.com/ddzy/react-reading-sources/issues/11)

## 记录

------

**注意**: 以下记录的有关`hooks`的内容, 均在`mount`阶段执行

### mountState

先来看一下`React.useState`的主要源码部分:

<details>
<summary>展开源码</summary>

```js
function mountState<S>(
  initialState: (() => S) | S,
): [S, Dispatch<BasicStateAction<S>>] {

  // 创建新的hook对象, 并追加至`workInProgressHook`单向循环链表尾部
  const hook = mountWorkInProgressHook();

  if (typeof initialState === 'function') {
    initialState = initialState();
  }

  hook.memoizedState = hook.baseState = initialState;

  // 创建当前hook对象的更新队列, 按次序保存当前hook上产生的所有dispatch
  const queue = hook.queue = {
    last: null,
    dispatch: null,
    lastRenderedReducer: basicStateReducer,
    lastRenderedState: (initialState: any),
  };

  // dispatch对应上述案例中的setName、setAge
  // 而对于当前hook来说, 此时的dispatch = setName
  const dispatch = queue.dispatch = dispatchAction.bind(
    null,
    currentlyRenderingFiber,
    queue,
  );

  return [hook.memoizedState, dispatch];
}
```
</details>

### mountWorkInProgressHook

在`mountWorkInProgressHook`函数内部, 定义了`hooks`在的存储方式. 之前有一个误区, 看了很多类似的对于`React-Hooks`的分析文章, 都说`hooks`是以`数组`的形式存储的, 并对此深信不疑. 但是直到今天看了源码, 才恍然大悟, 可能由于版本变迁, 至少在`react@16.8`版本, `hooks`是以`单向循环链表`的形式存储在`fiber`上. 话不多说, 看一下源码:

<details>
<summary>展开源码</summary>

```js
function mountWorkInProgressHook(): Hook {
  const hook: Hook = {
    memoizedState: null,

    baseState: null,
    queue: null,
    baseUpdate: null,

    next: null,
  };

  if (workInProgressHook === null) {
    // 当前的hook链表为空
    firstWorkInProgressHook = workInProgressHook = hook;
  } else {
    // 将新的hook对象追加至hook链表尾部
    workInProgressHook = workInProgressHook.next = hook;
  }

  return workInProgressHook;
}
```
</details>

### hook

在当前`函数`组件中, 每定义一个`hooks`API, 对应的, 会创建一个新的`hook`对象, 看一下`hook`的类型定义:

<details>
<summary>展开源码</summary>

```js
export type Hook = {
  // 每次dispatch之后, 计算出来的新的state, 也就是hooks API返回的state
  memoizedState: any,

  // 保存被中断update的上一个update计算出来的更新
  baseState: any,
  // 保存被中断(优先级不足)update的上一个update
  baseUpdate: Update<any, any> | null,
  // update更新队列, 单向循环链表结构
  queue: UpdateQueue<any, any> | null,

  // 下一个update更新
  next: Hook | null,
};
```
</details>

`hook`结构的几个重要的属性, 在`mount`阶段, 我并没有看懂, 转而看了一遍`update`的流程, 也就是在`React.useState`中, 自行调用`dispatch`来更新`state`, 之后才初步理解:

- memoizedState
- baseState
- baseUpdate
- queue
- next

### hook.memoizedState

顾名思义, 在`React.useState`中, 保存计算后的新`state`, 也就是下述代码返回的`newState`

```js
const [newState, dispatch] = React.useState(initialState);
```

### hook.baseState

在遍历`updateQueue`的过程中, 如果遍历到的`update`的`expirationTime`小于整体更新的`expirationTime`, 表明当前的`hooks`产生的update的优先级较小, 不会在此次更新流程中执行, 从而导致被中断. 此时`hook.baseState`则保存上一次的`update`计算出来的`state`.

### hook.baseUpdate

同`baseState`一样, `baseUpdate`保存上一次被中断的更新的上一个`update`, 等到下一次`renderRoot`时, 先从`baseUpdate`开始.

### hook.queue

`hook.queue`与`class`组件的`updateQueue`相似, 我姑且把它当成`updateQueue`吧, 不同的是:

> 在`class`组件中, `updateQueue`保存的是整个类产生的更新
> 而在`function`组件中, `updateQueue`保存的是单个`hooks`产生的更新, 此时可能有多个`hooks`

再来看一下`queue`的结构:

```js
type UpdateQueue<S, A> = {
  // 最后一个update
  last: Update<S, A> | null,
  // 返回给用户的dispatch
  dispatch: (A => mixed) | null,
  // 上一次使用的reducer
  lastRenderedReducer: ((S, A) => S) | null,
  // 上一次渲染之后的state
  lastRenderedState: S | null,
};
```

每个`hooks`产生的更新:

```js
type Update<S, A> = {
  expirationTime: ExpirationTime,
  action: A,
  eagerReducer: ((S, A) => S) | null,
  eagerState: S | null,
  next: Update<S, A> | null,
};
```

### hook.next

下一个`hook`节点