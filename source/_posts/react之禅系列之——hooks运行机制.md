---
title: react之禅系列之——hooks运行机制
date: 2019-07-17 19:58:58
tags: [react, js]
categories: [frontend]
---

着重记录`hooks`是如何进行操作整个`FunctionComponent`进行更新的.


<!-- more -->


## 更新

------

### [2019-7-17]

- Initial release

## 前言

------

俗话说, 工欲善其事必先利其器.

看完[函数组件的更新](https://github.com/ddzy/react-reading-sources/issues/8)源码, 自然而然地对于`hooks`的实现原理产生了浓厚的兴趣, 故记录在此.

## 从如何使用说起

------

`hooks`的出现, 完全颠覆了传统的`class`至上的原则, 解决了`代码冗余`、`可维护性`、`编译性能`等多个问题.

看源码之前, 先看一下`hooks`是如何使用的:

<details>
<summary>展开源码</summary>

```js
import React from 'react';

const TestHooks = () => {
  const [name, setName] = React.useState('ddzy');
  const [age, setAge] = React.useState(21);

  React.useEffect(() => {
    console.log('componentDidMount');
  }, []);

  return (
    <div className="test-hooks-wrapper">
      <p>{state.count}</p>
      <div>
        <button onClick={() => setName('duanzhaoyang')}>{ name }</button>
        <button onClick={() => setAge(22)}>{ age }</button>
      </div>
    </div>
  );
}
```
</details>

在源码`TestHook`组件中, 按照顺序依次调用了三个`hooks`API, 页面正常更新并渲染. 关于`useState`、`useEffect`的使用, 不记录太多了, 官方文档已经解释的非常清楚:

https://react.docschina.org/docs/hooks-intro.html

## 品内部实现原理

------

复习了`hooks`的基本使用, 着重看下它是如何实现的. 由于目前对于源码的理解还不是很深刻, 故本篇笔记会持续更新.

由于`hooks`的执行, 分为`mount`和`update`两个阶段. 前者在react应用初次渲染时执行, 后者则在组件全部挂载完成, 用户自定义操作(`dispatch`)时执行. 由于执行时机不同, 故内部的源码实现逻辑则大相径庭. 所以会分成两个部分来记录.

> 以下的源码分析都以`setState`为例

### mount阶段

#### useState

以最基本的`useState`为例, 其源码位于`ReactHooks.js`, 出乎意料的简单, 只有两行代码:

```js
export function useState<S>(initialState: (() => S) | S) {
  const dispatcher = resolveDispatcher();
  return dispatcher.useState(initialState);
}
```

接着看到`resolveDispatcher`:

```js
function resolveDispatcher() {
  const dispatcher = ReactCurrentDispatcher.current;

  return dispatcher;
}
```

没错, 很熟悉, 依稀记得在更新`function`组件之时, 通过`renderWithHooks`方法, 将`ReactCurrentDispatcher.current`赋值为`HooksDispatcherOnMount`.

#### mountState

省去了一大堆重复的步骤, 直接来看`useState`的最终定义. 由于`hooks`的执行分为`mount`和`update`阶段, 两者采取的更新策略略有不同, 这里只记录`mount`的过程.

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

从源码可以看出, `hooks`的内部还是比较绕的. 但是不影响理解, 由于目前处于`mount`阶段, 所以需要初始化相关的数据结构结构(有关`hooks`是如何存储的, 可参考[https://github.com/ddzy/react-reading-sources/issues/10](https://github.com/ddzy/react-reading-sources/issues/10)):

- hook
- hook.memorizedState
- hook.queue
- hook.dispatch

而正常情况下, 会在`update`阶段产生更新, 最常见的就是用户交互(`interactive`)时, 用上述例子来说:

- 点击按钮, 通过调用`setName`或`setAge`来产生一个更新

所以将重点放在`update`阶段的分析.

### FAQ

1. **如果在`mount`, 也就是首次渲染阶段, 在当前渲染的`函数`组件内部产生更新和在其它组件内部产生更新有什么区别?**

`mount`阶段的的`dispatch`实际上调用了`dispatchAction`方法, 其内部根据根据产生更新的`fiber`, 进行不同的处理:

- 如果当前fiber处于`mount`阶段, 且在其内部产生了更新
- 如果当前fiber处于`mount`阶段, 但不是它内部产生的更新, 新的调度(`scheduleWork`)

看下`dispatchAction`的源码:

<details>
<summary>展开源码</summary>

```js
function dispatchAction<S, A>(
  fiber: Fiber,
  queue: UpdateQueue<S, A>,
  action: A,
) {
  const alternate = fiber.alternate;

  // [情况一]: 如果当前fiber处于`mount`阶段, 且在其内部产生了更新
  if (
    fiber === currentlyRenderingFiber ||
    (alternate !== null && alternate === currentlyRenderingFiber)
  ) {
    // This is a render phase update. Stash it in a lazily-created map of
    // queue -> linked list of updates. After this render pass, we'll restart
    // and apply the stashed updates on top of the work-in-progress hook.

    // 标识是否在`mount`阶段产生更新
    // 产生的更新会保存到`renderPhaseUpdates`字典上
    // 后续在渲染完当前组件之后, 会根据`didScheduleRenderPhaseUpdate`, 来决定是否处理更新
    didScheduleRenderPhaseUpdate = true;
    const update: Update<S, A> = {
      expirationTime: renderExpirationTime,
      action,
      eagerReducer: null,
      eagerState: null,
      next: null,
    };
    if (renderPhaseUpdates === null) {
      renderPhaseUpdates = new Map();
    }
    const firstRenderPhaseUpdate = renderPhaseUpdates.get(queue);
    if (firstRenderPhaseUpdate === undefined) {
      renderPhaseUpdates.set(queue, update);
    } else {
      // Append the update to the end of the list.
      let lastRenderPhaseUpdate = firstRenderPhaseUpdate;
      while (lastRenderPhaseUpdate.next !== null) {
        lastRenderPhaseUpdate = lastRenderPhaseUpdate.next;
      }
      lastRenderPhaseUpdate.next = update;
    }
  }
  // [情况二]: 如果当前fiber处于`mount`阶段, 但不是它内部产生的更新, 安排一个新的调度工作
  else {
    flushPassiveEffects();

    const currentTime = requestCurrentTime();
    const expirationTime = computeExpirationForFiber(currentTime, fiber);

    const update: Update<S, A> = {
      expirationTime,
      action,
      eagerReducer: null,
      eagerState: null,
      next: null,
    };

    // Append the update to the end of the list.
    const last = queue.last;
    if (last === null) {
      // This is the first update. Create a circular list.
      update.next = update;
    } else {
      const first = last.next;
      if (first !== null) {
        // Still circular.
        update.next = first;
      }
      last.next = update;
    }
    queue.last = update;

    scheduleWork(fiber, expirationTime);
  }
}
```
</details>

### update阶段

`update`阶段产生的更新很常规, 省略掉与`mount`阶段相同的代码

#### updateState

...

### hook

> `hooks`与`hook`不一样. 前者指的是各种API(`useState`、`useEfffect`); 后者则特指单个`hooks`的存储结构. 在这里被坑了好久😂...

在react的`函数`组件内部, 每定义一个`hooks`API, 会产生新的`hook`对象, 追加到`fiber.memorizedState`链表中, 关于更多的有关于`hook`的存储结构的分析, 我抽离出了新的`issue`:

- [hook存储结构](https://github.com/ddzy/react-reading-sources/issues/10)

## 解以前心头之惑

------

### redux

看了`React.useState`的源码, 才发现其设计思想与`redux`极其类似, 或者说它完全借鉴了`redux`的策略:

- `dispatch`一个`action`
- 内部经过`reducer`处理得到新的`state`
- 返回新的`state`