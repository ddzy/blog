---
title: react之禅系列之——函数组件(FunctionComponent)的更新
date: 2019-07-17 19:54:02
tags: [react, js]
categories: [frontend]
---

处于对`hooks`原理的求知心理, 所以大力研究`函数`组件的更新机制.


<!-- more -->


## 更新

------

### [2019-7-17]

- Initial release

## 前言

------

上一篇记录了`ReactDOM.render`的具体流程, 到了`beginWork`, 也就是react根据当前fiber节点的各种属性, 来做不同的更新处理.

这篇issue主要记录下react对于函数组件(`FunctionComponent`)的处理机制.

## 流程

------

### beginWork

`beginWork`内部有一个值得注意的地方, 那就是`fiber.elementType`和`fiber.type`, 这两者有什么区别?

`fiber.elementType`对应`createElement`的第一个参数`type`. 而`fiber.type`则是为了区分`Suspense`组件. 正常情况下两者是相等的, 当存在`按需`组件, 也就是`Suspense`时, `fiber.type`的值为null.

<details>
<summary>展开源码</summary>

```js
function beginWork(
  current: Fiber | null,
  workInProgress: Fiber,
  renderExpirationTime: ExpirationTime,
): Fiber | null {
  switch (workInProgress.tag) {
    case FunctionComponent: {
      const Component = workInProgress.type;
      const unresolvedProps = workInProgress.pendingProps;
      // elementType & type
      const resolvedProps =
        workInProgress.elementType === Component
          ? unresolvedProps
          : resolveDefaultProps(Component, unresolvedProps);
      return updateFunctionComponent(
        current,
        workInProgress,
        Component,
        resolvedProps,
        renderExpirationTime,
      );
    }
  }
}
```
</details>

### updateFunctionComponent

顾名思义, 函数组件更新入口

<details>
<summary>展开源码</summary>

```js
function updateFunctionComponent(
  current,
  workInProgress,
  Component,
  nextProps: any,
  renderExpirationTime,
) {
  // nextChildren即为Component.props.children
  let nextChildren;

  // React16.8新特性, 使用Hooks渲染
  nextChildren = renderWithHooks(
    current,
    workInProgress,
    Component,
    nextProps,
    context,
    renderExpirationTime,
  );

  // 调和子节点
  // 子节点可能为各种不同的类型, 所以需要分类处理
  // 你是什么垃圾?
  reconcileChildren(
    current,
    workInProgress,
    nextChildren,
    renderExpirationTime,
  );

  return workInProgress.child;
}
```
</details>

### renderWithHooks

在整个react应用渲染到某个`函数`组件的时候, 在`reconcileChildren`, 也就是解析其子节点之前, 会使用`hooks`进行渲染, 此时的渲染过程是这样的:

- renderHooks
- 由于挂载和更新阶段执行的生命周期可能有所不同, 所以判断当前处于`mount`或`update`阶段,
   - mount
     - ReactCurrentDispatcher.current = HooksDispatcherOnMount
   - update
     - ReactCurrentDispatcher.current = HooksDispatcherOnUpdate
- 组件内部`主动`或`被动`触发`hooks`API, 进而通过`ReactCurrentDispatcher`来处理当前组件的更新
- 而对于`ReactCurrentDispatcher`, 则是`hooks`和组件的桥梁.

<details>
<summary>展开源码</summary>

```js
export function renderWithHooks(
  current: Fiber | null,
  workInProgress: Fiber,
  Component: any,
  props: any,
  refOrContext: any,
  nextRenderExpirationTime: ExpirationTime,
): any {
  renderExpirationTime = nextRenderExpirationTime;
  currentlyRenderingFiber = workInProgress;

  // ! hooks以单链表的形式存储在fiber.memorizedState对象中
  // ! 每一个hook都会开辟一个新的hooks对象, 并追加至单链表
  /**
   * * fiber.memorizedState =
   */
  // ! nextCurrentHook用来判断是首次渲染还是更新阶段
  nextCurrentHook = current !== null ? current.memoizedState : null;

  ReactCurrentDispatcher.current =
    nextCurrentHook === null
      ? HooksDispatcherOnMount
      : HooksDispatcherOnUpdate;

  let children = Component(props, refOrContext);

  // ! didScheduleRenderPhaseUpdate: 当前的渲染进程中, 是否产生了更新
  // ! 如果有, 执行完所有的hook, 避免重新调度
  if (didScheduleRenderPhaseUpdate) {
    do {
      didScheduleRenderPhaseUpdate = false;
      numberOfReRenders += 1;

      // Start over from the beginning of the list
      nextCurrentHook = current !== null ? current.memoizedState : null;
      nextWorkInProgressHook = firstWorkInProgressHook;

      currentHook = null;
      workInProgressHook = null;
      componentUpdateQueue = null;

      ReactCurrentDispatcher.current = __DEV__
        ? HooksDispatcherOnUpdateInDEV
        : HooksDispatcherOnUpdate;

      children = Component(props, refOrContext);
    } while (didScheduleRenderPhaseUpdate);

    renderPhaseUpdates = null;
    numberOfReRenders = 0;
  }

  return children;
}
```
</details>

### hooks

`react@18`提供了`hooks`API, 可以方便的在函数组件中定义`state`、`ref`等, 那么react为什么耗尽心思推出这个东西? 它解决了什么痛点? 说下我自己的看法吧:

**1. 代码冗余**

一个简单的例子. 在传统的`class`类组件中, 可能会在`componentDidMount`时开启`定时器`、`事件监听器`、`Observable`, 在`componentWillUnMount`时移除. 这时问题就来了, `设置`和`监听`分别需要调用两个生命周期函数, 显得繁杂.

而`hooks`函数组件则很好的解决了这个问题, `useEffect`很好的模拟了`componentDidMount`、`componentDidUpdate`、`componentWillUnmount`等生命周期, 可以用最少的代码完成相同的工作.

**2. 可维护性**

这可谓是`突破性`的变化.

我自己的毕设全面使用`hooks`来完成, 可能是由于项目小的缘故吧, 我永远体会不到`class`组件充满了`state`的情形, 可以想象一下一个文件塞满了`成百上千`行代码, 会有多心累.

`hooks`同样解决了这个问题, 无关紧要、不需要提升的state全部交由`function`函数组件内部维护, 自然而然地减少了`class`组件内部的代码量, 可维护性大大提高.

**3. 编译性能**

由于暂时没有深入`v8`引擎, 对于`class`和`function`的编译性能无法做出完美解释. 而正常情况下, `class`组件的编译性能要远远低于`function`组件.

而具体的对于`hooks`的结构、原理、机制, 为了避免篇幅过长, 还是记录到其它`issue`里面吧:

- [hooks存储结构](#9)
- [hooks运行机制](#9)

## 总结