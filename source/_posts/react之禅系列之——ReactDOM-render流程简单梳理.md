---
title: react之禅系列之——ReactDOM.render流程简单梳理
date: 2019-07-17 19:37:10
tags: [react, js]
categories: [frontend]
---

相对完整的梳理一下`ReactDOM.render`的大致流程


<!-- more -->


## 更新

------

### [2019-7-17]

- Initial release

## 前言

------

前几天已经简单总结过了`ReactDOM.render`的简单流程(#1).

但是没有进行深入的理解, 对于内部机制, 比如:

- reconcile
- schedule
- ...

没有过多的去解读, 毕竟错综复杂的`源码`加上`FB`那帮人的神奇脑洞.

让整个`react`源码变得晦涩难懂.

所以决定新开一个`issue`, 对照`源码`, 将`ReactDOM.render`的流程再梳理一遍.

## 必备

------

react体系中有几个常见的对象

- DOMContainer
- ReactRoot
- FiberRoot
- RootFiber
- Fiber

至于它们之间的关系, 我之前有画过简单的`xmind`脑图:

https://github.com/ddzy/react-reading-sources/blob/master/xmind/1-react%E5%90%84%E5%A4%A7%E8%8A%82%E7%82%B9%E4%B9%8B%E9%97%B4%E7%9A%84%E5%85%B3%E7%B3%BB.xmind

## 调和(`reconcile`)

------

### 节点准备

通过一系列的内部函数调用, 分别创建`ReactRoot`、`FiberRoot`、`RootFiber`等多个内部节点

- **ReactDOM.render**

应用入口

<details>
<summary>源码</summary>

```js
const ReactDOM = {
  render(
      element,
      container,
  ) {
      return legacyRenderSubtreeIntoContainer(
          null,
          element,
          container,
          false,
          callback,
      );
  }
};
```
</details>

- **legacyRenderSubtreeIntoContainer**

创建react应用`根节点` —— `ReactRoot`, 并且调用`ReactRoot.render`开始渲染

<details>
<summary>源码</summary>

```js
function legacyRenderSubtreeIntoContainer(
  parentComponent: ?React$Component<any, any>,
  children: ReactNodeList,
  container: DOMContainer,
  forceHydrate: boolean,
  callback: ?Function,
) {
  let root: Root = (container._reactRootContainer: any);

  if (!root) {
    // 创建React应用根节点
    root = container._reactRootContainer = legacyCreateRootFromDOMContainer(
      container,
      forceHydrate,
    );
    // 首次渲染, 不采用批量更新
    unbatchedUpdates(() => {
      root.render(children, callback);
    });
  }
}
```
</details>

- **ReactRoot**

react`根节点`构造函数. 内部会创建`FiberRoot`

<details>
<summary>源码</summary>

```js
function ReactRoot(
  // DOM根节点
  container: DOMContainer,
  // 是否启用ConcurrentMode, 也就是任务优先级、任务切片, 默认为false
  isConcurrent: boolean,
) {
  // 创建FiberRoot
  // 记录整个应用更新过程中的各种信息
  const root = createContainer(container, isConcurrent, hydrate);
  this._internalRoot = root;
}
```
</details>

- **createContainer**

依次调用`createFiberRoot`、`createHostRootFiber`, 来创建并返回一个新的`FiberRoot`对象

<details>
<summary>源码</summary>

```js
function createContainer(
  containerInfo: DOMContainer,
  isConcurrent: boolean = false,
) {
  return createFiberRoot(containerInfo, isConcurrent);
}
```
</details>

<details>
<summary>源码</summary>

```js
function createFiberRoot(
  containerInfo: DOMContainer,
  isConcurrent: boolean = false,
) {
  // 创建RootFiber => HostRoot
  const uninitializedFiber = createHostRootFiber(isConcurrent);

  return {
      // 对应的DOMContainer
      containerInfo: any,

      // Used only by persistent updates.
      pendingChildren: any,

      // FiberRoot => 整个`Fiber树`的顶点 => HostRootFiber
      current: Fiber,

      // 暂停提交的最早和最新优先级
      earliestSuspendedTime: ExpirationTime,
      latestSuspendedTime: ExpirationTime,

      // 不确定是否暂停的最新和最老的优先级
      earliestPendingTime: ExpirationTime,
      latestPendingTime: ExpirationTime,

      // promise执行resolve之后的最新优先级
      latestPingedTime: ExpirationTime,

      pingCache:
        | WeakMap<Thenable, Set<ExpirationTime>>
        | Map<Thenable, Set<ExpirationTime>>
        | null,

      // 如果出现错误, 并且队列中没有更多的更新
      // 在处理错误之前, 会重新从根开始同步渲染
      didError: boolean,

      pendingCommitExpirationTime: ExpirationTime,
      // A finished work-in-progress HostRoot that's ready to be committed.

      // 完成调度&更新的RootFiber.alternate
      finishedWork: Fiber | null,

      // Timeout handle returned by setTimeout. Used to cancel a pending timeout, if
      // it's superseded by a new one.
      timeoutHandle: TimeoutHandle | NoTimeout,
      // Top context object, used by renderSubtreeIntoContainer
      context: Object | null,
      pendingContext: Object | null,
      // Determines if we should attempt to hydrate on the initial mount
      +hydrate: boolean,

      // 当前RootFiber剩余的更新时间
      nextExpirationTimeToWorkOn: ExpirationTime,

      // 更新过期时间
      expirationTime: ExpirationTime,

      // List of top-level batches. This list indicates whether a commit should be
      // deferred. Also contains completion callbacks.
      // TODO: Lift this into the renderer
      firstBatch: Batch | null,

      // FiberRoot为单向链表结构
      // 防止有多个root, 也就是调用多次ReactDOM.render
      nextScheduledRoot: FiberRoot | null,
  };
}
```
</details>

### 节点更新

创建好了基本的节点, 开始更新

又回到了`legacyRenderSubtreeIntoContainer`这个方法

其内部调用了`ReactRoot.render(children)`来进行首次更新.

- **ReactRoot.prototype.render**

<details>
<summary>源码</summary>

```js
ReactRoot.prototype.render = function(
  children: ReactNodeList,
  callback: ?() => mixed,
): Work {
  const root = this._internalRoot;

  // 与`createContainer`相比
  // 前者创建`FiberRoot`
  // 后者更新`FiberRoot`
  updateContainer(children, root);
};
```
</details>

- **updateContainer**

更新`RootFiber`

<details>
<summary>源码</summary>

```js
export function updateContainer(
  element: ReactNodeList,
  container: OpaqueRoot,
){

  // 获取RootFiber => HostRoot
  const current = container.current;

  // 是`动态`的, 表示页面从js开始加载到当前的时间戳, 也有可能是上一个fiber的`currentSchedulerTime`, 这
是为了让批量更新具有相同的expirationTime, 避免多次更新.
  const currentTime = requestCurrentTime();

  // `concurrent`根据其来进行优先级更新调度, 值越小, 优先级越高
  const expirationTime = computeExpirationForFiber(currentTime, current);

  return updateContainerAtExpirationTime(
    element,
    container,
    parentComponent,
    expirationTime,
    callback,
  );
}
```
</details>

由于`requestCurrentTime`和`computeExpirationTimeForFiber`非常重要, 所以还是mark一下

先来看一下`requestCurrentTime`, 它的作用是: `计算从页面开始加载到当前的时间戳`

也有可能是另外几种情况, 会在下面的源码中进行注释:

<details>
<summary>源码</summary>

```js
function requestCurrentTime() {
  if (isRendering) {
    // 已经开始渲染了, 为了避免重复更新, 所以返回上一次的调度时间
    // currentRendererTime >= currentSchedulerTime
    return currentSchedulerTime;
  }

  findHighestPriorityRoot();
  if (
    nextFlushedExpirationTime === NoWork ||
    nextFlushedExpirationTime === Never
  ) {
    // 如果没有等待处理的任务
    // 更新渲染时间, 也就是`currentRendererTime`
    recomputeCurrentRendererTime();
    currentSchedulerTime = currentRendererTime;

    return currentSchedulerTime;
  }

  // 如果有被挂起的, 也就是等待处理的任务
  // 直接返回该任务的currentSchedulerTime
  return currentSchedulerTime;
}
```
</details>

看到这里, `requestCurrentTime`利用`currentSchedulerTime`这个变量

巧妙地让批量更新(`batchedUpdates`)具有相同的`expirationTime`, 避免了`expirationTime`的不同导致进行多次更新, 影响性能

话不多说, 接着来看`computeExpirationTimeForFiber`这个方法, 其内部对`5`种不同类型的`expirationTime`进行了不同的处理:

<details>
<summary>源码</summary>

```js
function computeExpirationForFiber(
  // 经由`requestCurrentTime`计算出来的值
  currentTime: ExpirationTime,
  // fiber对象
  fiber: Fiber
) {
  let expirationTime;

  if (expirationContext !== NoWork) {

  } else if (isWorking) {
    if (isCommitting) {
      // 在commit提交阶段产生的更新, 应该是同步(Sync)的expirationTime
      // ①: Sync
      expirationTime = Sync;
    } else {
      // render渲染阶段产生的更新, 应该与上一次具有相同的expirationTime
      expirationTime = nextRenderExpirationTime;
    }
  } else {
    // 如果没有正在进行的工作, 也就是不处于`isWorking`状态

    // 根据`isConcurrentMode`来判断是否开启异步渲染模式
    if (fiber.mode & ConcurrentMode) {
      if (isBatchingInteractiveUpdates) {
        // 如果是交互式(Interactive)更新, react的事件系统
        // ②: Interactive
        expirationTime = computeInteractiveExpiration(currentTime);
      } else {
        // 如果是普通的异步(Sync)更新, 普通的setState
        // ③: Async
        expirationTime = computeAsyncExpiration(currentTime);
      }
    } else {
      // 同步渲染
      // 因为在`legacyCreateRootFromDOMContainer`函数内部, 默认设置了`isConcurrent`为false
      // ④: NoWork
      expirationTime = Sync;
    }
  }

  return expirationTime;
}
```
</details>

- **updateContainerAtExpirationTime**

- **scheduleRootUpdate**

`reconcile -> schedule`的桥梁

在其内部进行创建更新(`update`), 更新入队...等一系列操作

之后进入`schedule`阶段, 嘀嘀嘀

<details>
<summary>源码</summary>

```js
function scheduleRootUpdate(
  // RootFiber
  current: Fiber,
  // ReactDOM.render的第一个参数
  element: ReactNodeList,
  expirationTime: ExpirationTime,
) {
  // 创建更新
  const update = createUpdate(expirationTime);

  // 更新参数
  update.payload = {element};
  update.callback = callback;

  // 更新入队
  // 将更新追加到对应fiber的`updateQueue`上
  enqueueUpdate(current, update);

  // 进入调度(schedule)阶段
  scheduleWork(current, expirationTime);
}
```
</details>

--------------------------------------简简单单的分割线--------------------------------------------------

## 调度(`schedule`)

------

经过了上面的调和(`reconcile`)阶段, 已经做好了基本的准备工作:

- 计算`expirationTime`
- `update`的创建、初始化、入队
- 其它的杂七杂八的操作

而在调和阶段的最后, 有一段这样的代码:

<details>
<summary>源码</summary>

```js
scheduleWork(current, expirationTime);
```
</details>

标志着, 整个应用开始进入最核心的调度(`schedule`)阶段

### scheduleWork

调度主入口. `ReactDOM.render`、`ClassComponentInstance.setState`... 都会进行调用该方法:

<details>
<summary>源码</summary>

```js
function scheduleWork(fiber: Fiber, expirationTime: ExpirationTime) {
  // 寻找`FiberRoot`, 更新`expirationTime`
  const root = scheduleWorkToRoot(fiber, expirationTime);

  // ! [MARK]: nextRenderExpirationTime -> 当前正在调度工作的过期时间
  // ! [MARK]: expirationTime -> 新产生的任务的过期时间
  // ! [MARK]: childExpirationTime -> 快速确定子fiber有没有挂起的等待执行的任务

  // 此次判断就是fiber的特性之一 --> 任务优先级机制
  if (
    !isWorking &&
    nextRenderExpirationTime !== NoWork &&
    expirationTime > nextRenderExpirationTime
  ) {
    // 新的任务(ReactDOM.render)优先级较高, 终止当前任务(首次渲染时产生的更新)
    // nextUnitOfWork - 表示当前正在调度的fiber
    // 重置当前调度的任务为null
    resetStack();
  }

  // 标志优先级
  markPendingPriorityLevel(root, expirationTime);

  if (
    // If we're in the render phase, we don't need to schedule this root
    // for an update, because we'll do it before we exit...
    !isWorking ||
    isCommitting ||
    // ...unless this is a different root than the one we're rendering.
    nextRoot !== root
  ) {
    const rootExpirationTime = root.expirationTime;
    // 开始进入请求工作
    // react应用产生的所有更新, 都交由FiberRoot来处理.
    requestWork(root, rootExpirationTime);
  }
}
```
</details>

### requestWork

请求工作:

<details>
<summary>源码</summary>

```js
function requestWork(root: FiberRoot, expirationTime: ExpirationTime) {
  // 添加FiberRoot到调度队列
  addRootToSchedule(root, expirationTime);

  if (isRendering) {
    // Prevent reentrancy. Remaining work will be scheduled at the end of
    // the currently rendering batch.
    return;
  }

  /**
   * ! 注意点:
   * !    1). 批次更新
   * !    2). react的事件系统会自动设置`isBatchingUpdates`的值为true
   */
  if (isBatchingUpdates) {
    // 在当前批次渲染的最后, 直接进行同步更新
    if (isUnbatchingUpdates) {
      // ...unless we're inside unbatchedUpdates, in which case we should
      // flush it now.
      nextFlushedRoot = root;
      nextFlushedExpirationTime = Sync;
      performWorkOnRoot(root, Sync, false);
    }
    return;
  }

  /**
   * ! 注意点:
   * !    1). 同步任务直接执行, 不能被打断
   * !    2). 异步任务, requestIdleCallback
   */
  if (expirationTime === Sync) {
    // 执行同步任务
    performSyncWork();
  } else {
    // 在浏览器空闲时间, 执行异步任务
    scheduleCallbackWithExpirationTime(root, expirationTime);
  }
}
```
</details>

----------------------------------有空再更-----------------------------------


接着更新, 上面在`requestWork`中, 根据`expirationTime`是否同步or异步, 来进行不同的`performWork`, `performWork`内部机制是什么? 继续看源码.

### performSyncWork

关于`performSyncWork`较简单, 由于属于同步任务, 所以直接省去`requestIdleCallback`这个环节, 直接调用`performWork`:

<details>
<summary>源码</summary>

```js
function performSyncWork() {
  performWork(Sync);
}
```
</details>

### scheduleCallbackWithExpirationTime

`scheduleCallbackWithExpirationTime`主要的功能是:

- 计算`timeout`, 也就是requestIdleCallback`的`timeout`参数

先来列出几个重要的全局变量:

- **callbackExpirationTime** 正在分片的任务的expirationTime
- **callbackID** 正在分片的任务的id, 功能类似于setTimeoutId, 但是结构不同, react自行定义了它的结构.

接着看一下源码:

<details>
<summary>源码</summary>

```js
function scheduleCallbackWithExpirationTime(
  root: FiberRoot,
  expirationTime: ExpirationTime,
) {
  // 如果当前已经有任务在分片了 - 任务优先级机制
  if (callbackExpirationTime !== NoWork) {
    // 如果新的任务优先级 < 当前正在进行的任务
    if (expirationTime < callbackExpirationTime) {
      return;
    } else {
    // 如果新的任务优先级 > 当前正在进行的任务
      if (callbackID !== null) {
        // 强制退出当前分片的任务
        cancelCallback(callbackID);
      }
    }
  }

  /**
   * ! 注意点:
   * !    1). 浏览器正常的刷新频率为30HZ
   * !    2). 等同于在`一秒`内需要刷新30次
   * !    3). 每一帧的时间为`33ms`
   * !    4). 在`33ms`内需要做js执行、动画、重绘重排等操作
   */

  // 保存即将进行分片的任务
  callbackExpirationTime = expirationTime;
  const currentMs = now() - originalStartTimeMs;
  const expirationTimeMs = expirationTimeToMs(expirationTime);

  // timeout = 组件过期的时间 - 当前时间 = requestIdleCallback的timeout参数
  const timeout = expirationTimeMs - currentMs;

  // 模拟`requestIdleCallback`API
  // react通过`scheduleCallback`来实现任务分片机制
  // 在一个个任务分片中, 实现任务优先级
  // 将传递的`performAsyncWork`参数加入到双向循环链表.
  callbackID = scheduleCallback(performAsyncWork, {timeout});
}
```
</details>

### performAsyncWork

react通过模拟`requestIdleCallback`API, 使得浏览器可以在每一帧的空闲时间来执行react代码. react维护了一个`callback`双向循环链表. 因此, 浏览器可以在空闲时间遍历改链表, 进而实现任务的`分片`机制.

关于`scheduleCallback`是如何实现的? 或者react是如何模拟`requestIdleCallback`的? 后面再开新的issue记录吧.

<details>
<summary>源码</summary>

```js
function performAsyncWork(didTimeout) {
  // 如果FiberRoot的expirationTime超时
  if (didTimeout) {
    if (firstScheduledRoot !== null) {
      // 更新FiberRoot单向循环链表, 更新所有已经超时的expirationTime
      // 为了安排它们在一个批次进行批量更新
      recomputeCurrentRendererTime();
      let root: FiberRoot = firstScheduledRoot;
      do {
        didExpireAtExpirationTime(root, currentRendererTime);
        // The root schedule is circular, so this is never null.
        root = (root.nextScheduledRoot: any);
      } while (root !== firstScheduledRoot);
    }
  }

  findHighestPriorityRoot();

  if (disableYielding) {
    while (nextFlushedRoot !== null && nextFlushedExpirationTime !== NoWork) {
      performWorkOnRoot(nextFlushedRoot, nextFlushedExpirationTime, false);
      findHighestPriorityRoot();
    }
  }

  // If we're inside a callback, set this to false since we just completed it.
  callbackExpirationTime = NoWork;
  callbackID = null;

  // 如果在本次时间分片内没有执行完任务
  // 重新安排
  if (nextFlushedExpirationTime !== NoWork) {
    scheduleCallbackWithExpirationTime(
      ((nextFlushedRoot: any): FiberRoot),
      nextFlushedExpirationTime,
    );
  }

  // Clean-up.
  finishRendering();
}
```
</details>

### performWorkOnRoot

先梳理一下同/异步任务的走向:

<details>
<summary>源码</summary>

```js
// 同步任务
performSyncWork  ->  performWorkSync  ->  performWork  ->  performWorkOnRoot

// 异步任务
scheduleCallbackWithExpirationTime  ->  scheduleCallback  ->  performAsyncWork  -> performWorkOnRoot
```
</details>

接着简单看一下`performOnRoot`, 此时进入`isRendering`阶段. 它包括两个部分:

- 如果`root.finishWork`为null, 则进入`isCommitting`阶段(**completeRoot**)
- 反之, 进入`isWorking`阶段(**renderRoot**)

<details>
<summary>源码</summary>

```js
function performWorkOnRoot(
  root: FiberRoot,
  expirationTime: ExpirationTime,
  isYieldy: boolean,
) {
  isRendering = true;

  if (!isYieldy) {
    // ! 同步任务
    // ! yield --> 等待, 表示不能暂停、被打断的任务

    let finishedWork = root.finishedWork;
    if (finishedWork !== null) {
      completeRoot(root, finishedWork, expirationTime);
    } else {
      renderRoot(root, isYieldy);
      finishedWork = root.finishedWork;
      if (finishedWork !== null) {
        // We've completed the root. Commit it.
        completeRoot(root, finishedWork, expirationTime);
      }
    }
  } else {
    // ! 异步任务

    let finishedWork = root.finishedWork;
    if (finishedWork !== null) {
      // This root is already complete. We can commit it.
      completeRoot(root, finishedWork, expirationTime);
    } else {
      renderRoot(root, isYieldy);
      completeRoot(root, finishedWork, expirationTime);
    }
  }

  isRendering = false;
}
```
</details>

### renderRoot

开始真正的渲染工作

<details>
<summary>源码</summary>

```js
function renderRoot(root: FiberRoot, isYieldy: boolean): void {
  isWorking = true;
  const previousDispatcher = ReactCurrentDispatcher.current;
  ReactCurrentDispatcher.current = ContextOnlyDispatcher;

  const expirationTime = root.nextExpirationTimeToWorkOn;

  // ! 凡是带有`next`字样的, 都代表当前正在进行的工作, 反之则代表新的工作
  if (
    expirationTime !== nextRenderExpirationTime ||
    root !== nextRoot ||
    nextUnitOfWork === null
  ) {
    // 创建WorkInProgress-Fiber-Tree
    nextUnitOfWork = createWorkInProgress(
      nextRoot.current,
      null,
      nextRenderExpirationTime,
    );
    root.pendingCommitExpirationTime = NoWork;
  }

  // 是否出现致命错误
  let didFatal = false;

  do {
    try {
      // 大循环, 遍历整颗WorkInProgress树
      workLoop(isYieldy);
    } catch (thrownValue) {
      // 如果出现错误, 对应的错误处理机制
      if (nextUnitOfWork === null) {
        // This is a fatal error.
        didFatal = true;
        onUncaughtError(thrownValue);
      } else {
        const sourceFiber: Fiber = nextUnitOfWork;
        let returnFiber = sourceFiber.return;
        if (returnFiber === null) {
          // This is the root. The root could capture its own errors. However,
          // we don't know if it errors before or after we pushed the host
          // context. This information is needed to avoid a stack mismatch.
          // Because we're not sure, treat this as a fatal error. We could track
          // which phase it fails in, but doesn't seem worth it. At least
          // for now.
          didFatal = true;
          onUncaughtError(thrownValue);
        } else {
          throwException(
            root,
            returnFiber,
            sourceFiber,
            thrownValue,
            nextRenderExpirationTime,
          );
          nextUnitOfWork = completeUnitOfWork(sourceFiber);
          continue;
        }
      }
    }
    break;
  } while (true);

  // Yield back to main thread.
  // 如果渲染出现致命错误, 会退出并重新安排渲染
  if (didFatal) {
    onFatal(root);
    return;
  }

  // We completed the whole tree.
  const rootWorkInProgress = root.current.alternate;

  // Ready to commit.
  // 进入commit阶段
  onComplete(root, rootWorkInProgress, expirationTime);
}
```
</details>

## 渲染(`render`)

------

此时, 从`RootFiber`的`WorkInProgress`的开始, 逐步遍历整个`Fiber`树, 进入渲染阶段

### WorkLoop

<details>
<summary>源码</summary>

```js
function workLoop(isYieldy) {
  if (!isYieldy) {
    // 同步任务
    // 不能被打断
    // nextUnitOfWork === currentFiber.child, 也就是下一次进行渲染的fiber
    while (nextUnitOfWork !== null) {
      nextUnitOfWork = performUnitOfWork(nextUnitOfWork);
    }
  } else {
    // 异步任务
    // 可以被优先级更高的打断
    while (nextUnitOfWork !== null && !shouldYield()) {
      nextUnitOfWork = performUnitOfWork(nextUnitOfWork);
    }
  }
}
```
</details>

### performUnitOfWork

对单个`Fiber`节点进行更新. 同时更新`fiber.stateNode`, 也就是`ReactElement`.

<details>
<summary>源码</summary>

```js
function performUnitOfWork(workInProgress: Fiber): Fiber | null {
  const current = workInProgress.alternate;

  // next === fiber.return
  // 下一个渲染的fiber节点
  let next;

  next = beginWork(current, workInProgress, nextRenderExpirationTime);
  workInProgress.memoizedProps = workInProgress.pendingProps;

  ReactCurrentOwner.current = null;

  return next;
}
```
</details>

### beginWork

开始真正的渲染, 此时会从根据当前`fiber`的`tag`属性, 来对不同的组件做对应的处理. 关于`fiber.tag`有哪些具体属性, 可以参考这篇笔记:

[fiber.tag类型汇总](https://github.com/ddzy/react-reading-sources/issues/7)

接着看一下源码:

<details>
<summary>源码</summary>

```js
function beginWork(
  current: Fiber | null,
  workInProgress: Fiber,
  renderExpirationTime: ExpirationTime,
): Fiber | null {
  const updateExpirationTime = workInProgress.expirationTime;

  if (current !== null) {
    const oldProps = current.memoizedProps;
    const newProps = workInProgress.pendingProps;

    if (oldProps !== newProps || hasLegacyContextChanged()) {
      // If props or context changed, mark the fiber as having performed work.
      // This may be unset if the props are determined to be equal later (memo).

      // ! 根据`oldProps === newProps`判断是否已更新
      didReceiveUpdate = true;
    } else if (updateExpirationTime < renderExpirationTime) {
      // ! 如果当前fiber节点的子树产生更新的优先级小于当前更新

      didReceiveUpdate = false;
      // This fiber does not have any pending work. Bailout without entering
      // the begin phase. There's still some bookkeeping we that needs to be done
      // in this optimized path, mostly pushing stuff onto the stack.
      switch (workInProgress.tag) {
        case HostRoot:
          pushHostRootContext(workInProgress);
          resetHydrationState();
          break;

        // ! HostComponent -> 代表原生DOM元素的字符串(div、p)
        case HostComponent:
          pushHostContext(workInProgress);
          break;
        case ClassComponent: {
          const Component = workInProgress.type;
          if (isLegacyContextProvider(Component)) {
            pushLegacyContextProvider(workInProgress);
          }
          break;
        }
        case HostPortal:
          pushHostContainer(
            workInProgress,
            workInProgress.stateNode.containerInfo,
          );
          break;
        case ContextProvider: {
          const newValue = workInProgress.memoizedProps.value;
          pushProvider(workInProgress, newValue);
          break;
        }
        case Profiler:
          if (enableProfilerTimer) {
            workInProgress.effectTag |= Update;
          }
          break;
        case SuspenseComponent: {
          const state: SuspenseState | null = workInProgress.memoizedState;
          const didTimeout = state !== null;
          if (didTimeout) {
            // If this boundary is currently timed out, we need to decide
            // whether to retry the primary children, or to skip over it and
            // go straight to the fallback. Check the priority of the primary
            // child fragment.
            const primaryChildFragment: Fiber = (workInProgress.child: any);
            const primaryChildExpirationTime =
              primaryChildFragment.childExpirationTime;
            if (
              primaryChildExpirationTime !== NoWork &&
              primaryChildExpirationTime >= renderExpirationTime
            ) {
              // The primary children have pending work. Use the normal path
              // to attempt to render the primary children again.
              return updateSuspenseComponent(
                current,
                workInProgress,
                renderExpirationTime,
              );
            } else {
              // The primary children do not have pending work with sufficient
              // priority. Bailout.
              const child = bailoutOnAlreadyFinishedWork(
                current,
                workInProgress,
                renderExpirationTime,
              );
              if (child !== null) {
                // The fallback children have pending work. Skip over the
                // primary children and work on the fallback.
                return child.sibling;
              } else {
                return null;
              }
            }
          }
          break;
        }
        case DehydratedSuspenseComponent: {
          if (enableSuspenseServerRenderer) {
            // We know that this component will suspend again because if it has
            // been unsuspended it has committed as a regular Suspense component.
            // If it needs to be retried, it should have work scheduled on it.
            workInProgress.effectTag |= DidCapture;
            break;
          }
        }
      }
      return bailoutOnAlreadyFinishedWork(
        current,
        workInProgress,
        renderExpirationTime,
      );
    }
  } else {
    didReceiveUpdate = false;
  }

  // Before entering the begin phase, clear the expiration time.
  workInProgress.expirationTime = NoWork;

  switch (workInProgress.tag) {
    case IndeterminateComponent: {
      const elementType = workInProgress.elementType;
      return mountIndeterminateComponent(
        current,
        workInProgress,
        elementType,
        renderExpirationTime,
      );
    }
    case LazyComponent: {
      const elementType = workInProgress.elementType;
      return mountLazyComponent(
        current,
        workInProgress,
        elementType,
        updateExpirationTime,
        renderExpirationTime,
      );
    }
    case FunctionComponent: {
      const Component = workInProgress.type;
      const unresolvedProps = workInProgress.pendingProps;
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
    case ClassComponent: {
      const Component = workInProgress.type;
      const unresolvedProps = workInProgress.pendingProps;
      const resolvedProps =
        workInProgress.elementType === Component
          ? unresolvedProps
          : resolveDefaultProps(Component, unresolvedProps);
      return updateClassComponent(
        current,
        workInProgress,
        Component,
        resolvedProps,
        renderExpirationTime,
      );
    }
    case HostRoot:
      return updateHostRoot(current, workInProgress, renderExpirationTime);
    case HostComponent:
      return updateHostComponent(current, workInProgress, renderExpirationTime);
    case HostText:
      return updateHostText(current, workInProgress);
    case SuspenseComponent:
      return updateSuspenseComponent(
        current,
        workInProgress,
        renderExpirationTime,
      );
    case HostPortal:
      return updatePortalComponent(
        current,
        workInProgress,
        renderExpirationTime,
      );
    case ForwardRef: {
      const type = workInProgress.type;
      const unresolvedProps = workInProgress.pendingProps;
      const resolvedProps =
        workInProgress.elementType === type
          ? unresolvedProps
          : resolveDefaultProps(type, unresolvedProps);
      return updateForwardRef(
        current,
        workInProgress,
        type,
        resolvedProps,
        renderExpirationTime,
      );
    }
    case Fragment:
      return updateFragment(current, workInProgress, renderExpirationTime);
    case Mode:
      return updateMode(current, workInProgress, renderExpirationTime);
    case Profiler:
      return updateProfiler(current, workInProgress, renderExpirationTime);
    case ContextProvider:
      return updateContextProvider(
        current,
        workInProgress,
        renderExpirationTime,
      );
    case ContextConsumer:
      return updateContextConsumer(
        current,
        workInProgress,
        renderExpirationTime,
      );
    case MemoComponent: {
      const type = workInProgress.type;
      const unresolvedProps = workInProgress.pendingProps;

      // Resolve outer props first, then resolve inner props.
      let resolvedProps = resolveDefaultProps(type, unresolvedProps);
      resolvedProps = resolveDefaultProps(type.type, resolvedProps);

      return updateMemoComponent(
        current,
        workInProgress,
        type,
        resolvedProps,
        updateExpirationTime,
        renderExpirationTime,
      );
    }
    case SimpleMemoComponent: {
      return updateSimpleMemoComponent(
        current,
        workInProgress,
        workInProgress.type,
        workInProgress.pendingProps,
        updateExpirationTime,
        renderExpirationTime,
      );
    }
    case IncompleteClassComponent: {
      const Component = workInProgress.type;
      const unresolvedProps = workInProgress.pendingProps;
      const resolvedProps =
        workInProgress.elementType === Component
          ? unresolvedProps
          : resolveDefaultProps(Component, unresolvedProps);
      return mountIncompleteClassComponent(
        current,
        workInProgress,
        Component,
        resolvedProps,
        renderExpirationTime,
      );
    }
    case DehydratedSuspenseComponent: {
      if (enableSuspenseServerRenderer) {
        return updateDehydratedSuspenseComponent(
          current,
          workInProgress,
          renderExpirationTime,
        );
      }
      break;
    }
    case EventComponent: {
      if (enableEventAPI) {
        return updateEventComponent(
          current,
          workInProgress,
          renderExpirationTime,
        );
      }
      break;
    }
    case EventTarget: {
      if (enableEventAPI) {
        return updateEventTarget(current, workInProgress, renderExpirationTime);
      }
      break;
    }
  }
}
```
</details>

### 组件更新

根据`fiber.tag`, 不同的组件进行不同的更新. 那么, 具体是如何更新的? 避免篇幅过长, 将其拆分为多篇笔记:

- [函数组件(FunctionComponent)的更新](https://github.com/ddzy/react-reading-sources/issues/8)
- ...

## 提交(`commit`)

------

待看

## 总结

------

### [2019-7-15]

看到了`beginWork`中对于`FunctionComponent`的更新, 但是`hook`机制较为复杂, 需要花较长时间理解.

简单总结下, `react`中处理一个更新, 大致会经过下列流程:

- 产生更新
- 计算`expirationTime`
- 创建更新`update`
- 更新入队`updateQueue`
- 寻找`FiberRoot`
- `FiberRoot`加入单向循环链表
- 遍历`FiberRoot`链表, 通过`findHighestPriorityRoot`寻找最高优先级的`FiberRoot`, 依次执行`performWorkOnRoot`
- isRendering
- 找到`RootFiber`, 创建`WorkInProgress`
- isWorking
- 进入WorkLoop大循环, 对`WorkInProgress`返回的每个`fiber.child`做处理
- 进入`beginWork`阶段, 根据当前fiber的tag做不同的更新.
- ......