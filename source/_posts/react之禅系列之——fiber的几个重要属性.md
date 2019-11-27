---
title: react之禅系列之——fiber的几个重要属性
date: 2019-07-17 19:31:48
tags: [react, js]
categories: [frontend]
---

`fiber`可以说是贯穿了整个`react@16`. 那么, `fiber`对象中存在着一些奇奇怪怪的属性, 随着源码阅读的进一步深入, 对于其中的一些重要属性有了简单了解.


<!-- more -->


## 更新

------

### [2019-7-17]

- Initial release

## 记录

------

`Fiber`可以说是贯穿了整个`react@16`.

那么, `fiber`对象中存在着一些奇奇怪怪的属性,

随着源码阅读的进一步深入, 对于其中的一些**重要属性**有了简单了解,

贴出源码以便对照:

```js
export type Fiber = {|

  // 标识不同的组件类型
  // FunctionComponent: 0; ClassComponent: 1...
  // 详细参考源码: https://github.com/ddzy/react/blob/master/packages/shared/ReactWorkTags.js
  tag: WorkTag,

  // 唯一标识此fiber
  // 用于DOM diff阶段
  key: null | string,

  // react元素的类型
  // createElement的第一个参数
  // 可以为ClassComponent、FunctionComponent、Symbol、HostComponent...
  elementType: any,

  // The resolved function/class/ associated with this fiber.
  type: any,

  // 该fiber节点对应的ReactElement对象
  stateNode: any,

  // fiber的父级
  return: Fiber | null,

  // 单链表树状结构
  child: Fiber | null,
  sibling: Fiber | null,
  index: number,

  // 节点的ref
  ref: null | (((handle: mixed) => void) & {_stringRef: ?string}) | RefObject,

  // 新的props
  pendingProps: any,
  // 老的props
  memoizedProps: any,

  // 组件自身的更新队列
  // 自己产生的setState、...会追加到该队列
  updateQueue: UpdateQueue<any> | null,

  // 旧的state
  memoizedState: any,

  // DOM diff相关
  // 详情参考源码: https://github.com/ddzy/react/blob/master/packages/shared/ReactSideEffectTags.js
  effectTag: SideEffectTag,

  // 单链表结构
  // 快速找到下一个产生effect的fiber节点
  nextEffect: Fiber | null,

  firstEffect: Fiber | null,
  lastEffect: Fiber | null,

  // fiber的精髓
  // 标识该组件应该在未来的某个时刻完成更新
  // expirationTime五种类型: NoWork(默认0)、Never(1)、Interactive(100 - 150)、Async(250 - 5000)、Sync(Number.MAXINTEGER)
  expirationTime: ExpirationTime,

  // 快速确定子fiber树有没有挂起的更改
  childExpirationTime: ExpirationTime,

  // fiber的镜像节点
  // 日常更新都在该节点进行
  // 也称作`WorkInProgress`
  alternate: Fiber | null,
|};
```