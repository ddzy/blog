---
title: react之禅系列之——fiber的updateQueue的基本结构
date: 2019-07-17 19:45:22
tags: [react, js]
categories: [frontend]
---

`react`的类组件每产生一个更新, 就会创建相应的`update`, 并追加到该组件对应的`fiber`上的`updateQueue`单向链表上, 那么`updateQueue`的结构是怎样的, 来记录下.


<!-- more -->


## 更新

------

### [2019-7-17]

- Initial release

## 前言

------

react每产生一次更新, 都会:

- 计算`expirationTime`
- 创建`update`
- 初始化`update`参数
- 将`update`追加到`updateQueue`

那么, 问题来了, 这个`updateQueue`是一种什么结构?

带着问题, 来到源码看一下

## 分析

------

### enqueueUpdate

react在执行`将update追加到updateQueue`操作的时候, 会判断当前`fiber`节点上是否已经具有`updateQueue`, 如果没有, 会调用`createUpdateQueue`创建一个新的队列.

```js
export function enqueueUpdate<State>(fiber: Fiber, update: Update<State>) {
  const alternate = fiber.alternate;
  let queue1;
  let queue2;

  if (alternate === null) {
    // There's only one fiber.
    queue1 = fiber.updateQueue;
    queue2 = null;
    if (queue1 === null) {
      // updateQueue不存在, 创建新的
      queue1 = fiber.updateQueue = createUpdateQueue(fiber.memoizedState);
    }
  }
}
```

### createUpdateQueue

上面已经说过了, `createUpdateQueue`会创建一个新的`单向链表`状的更新队列

```js
export function createUpdateQueue<State>(
  // 初始的state
  baseState: State
): UpdateQueue<State> {
  const queue: UpdateQueue<State> = {
    baseState,
    firstUpdate: null,
    lastUpdate: null,
    firstCapturedUpdate: null,
    lastCapturedUpdate: null,
    firstEffect: null,
    lastEffect: null,
    firstCapturedEffect: null,
    lastCapturedEffect: null,
  };

  return queue;
}
```

### updateQueue

最后来看一下`updateQueue`的结构定义:

```js
export type UpdateQueue<State> = {
  // 初始的state
  baseState: State,

  // 链表中的第一/最后一个update
  firstUpdate: Update<State> | null,
  lastUpdate: Update<State> | null,

  // 链表中的第一/最后一个更新出现错误的update
  firstCapturedUpdate: Update<State> | null,
  lastCapturedUpdate: Update<State> | null,

  // 链表中的第一/最后一个副作用(DOM diff的结果)
  firstEffect: Update<State> | null,
  lastEffect: Update<State> | null,

  firstCapturedEffect: Update<State> | null,
  lastCapturedEffect: Update<State> | null,
};
```