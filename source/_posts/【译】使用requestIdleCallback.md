---
title: 【译】使用requestIdleCallback
date: 2020-12-05 09:29:13
tags: [react, requestIdleCallback, google]
categories: [frontend, translation]
---

目前的许多站点和 app 都要执行大量脚本. 同时 JavaScript 需要尽可能的快速执行, 并且不影响用户交互. 假如用户滚动页面的时候发送一些分析数据, 或者你往页面上插入 DOM 元素的过程中用户恰好点击了某个按钮, 你的 app 反应会变得迟缓, 严重影响用户体验...

<!-- more -->

## 更新

------

### [2020-12-5]

- Initial release

### [2020-12-28]

#### Changed

- 更改文章标题, 避免 hexo 部署的问题

### [2021-2-5]

#### Changed

- 更改文章标题

## 概述

------

原文链接:

https://developers.google.com/web/updates/2015/08/using-requestidlecallback

译者前注:

React 核心调度算法模拟实现了 requestIdleCallback, 具体源码可参考 [这里](https://github.com/ddzy/react/blob/master/packages/react-reconciler/src/ReactFiberScheduler.old.js#L2138-L2164)

## 使用 requestIdleCallback

------

目前的许多站点和 app 都要执行大量脚本. 同时 JavaScript 需要尽可能的快速执行, 并且不影响用户交互. 假如用户滚动页面的时候发送一些分析数据, 或者你往页面上插入 DOM 元素的过程中用户恰好点击了某个按钮, 你的 app 反应会变得迟缓, 严重影响用户体验.

![img](https://developers.google.com/web/updates/images/2015-08-27-using-requestidlecallback/main.png)

好消息是现在 `requestIdleCallback` API 可以解决这个问题. 和 `requestAnimationFrame` 一样的是, 它会尽可能地调度动画以达到 60fps, `requestIdleCallback` 会在每一帧的空闲时间或者没有用户交互的情况下调度工作. 这意味着可以在不妨碍用户操作的情况下执行 JavaScript 脚本. `requestIdleCallback` 是一个试验性的 API, 标准也正在完善中, 目前在 Chrome47 版本可用, 所以你可以下载 Chrome Canary 来尝试一下!

## 为什么要使用 requestIdleCallback?

------

手动调度不重要的工作是很困难的. 你不可能确切的知道浏览器在每一帧进行:

- `requestAnimationFrame` 回调
- 计算样式
- 布局
- 绘制
- 其它内部操作

之后还剩下多少时间; 为了确定用户是否处于交互状态, 你需要给诸如 `scroll`、`touch`、`click` 的事件添加监听器. 但是浏览器可以, 它可以精确地知道每一帧的剩余可用时间以及用户是否处于交互状态, 使用 `requestIdleCallback` API 可以让空闲时间得到充分利用.

接下来让我们看一下如何去使用它.

## 审视 requestIdleCallback

------

由于 `requestIdleCallback` 还处于初级阶段, 所以在使用之前最好判断浏览器是否支持它:

```javascript
if ('requestIdleCallback' in window) {
  // 使用 requestIdleCallback 调度任务
} else {
  // 立即执行任务
}
```

对于不支持 `requestIdleCallback` 的浏览器, 可以优雅降级, 使用 `setTimeout` 模拟:

```javascript
window.requestIdleCallback =
  window.requestIdleCallback ||
  function (cb) {
    var start = Date.now();
    return setTimeout(function () {
      cb({
        didTimeout: false,
        timeRemaining: function () {
          return Math.max(0, 50 - (Date.now() - start));
        }
      });
    }, 1);
  }

window.cancelIdleCallback =
  window.cancelIdleCallback ||
  function (id) {
    clearTimeout(id);
  }
```

使用 `setTimeout` 并不好, 因为它不知道每一帧的空闲时间, 但是在 `requestIdleCallback` 不被浏览器支持的情况下或许是最好的选择.

当然, 现在我们假定 `requestIdleCallback` 是存在的.

## 使用 requestIdleCallback

------

调用 `requestIdleCallback` 的方式和 `requestAnimationFrame` 差不多, 它的第一个参数接收一个回调函数:

```javascript
requestIdleCallback(myNonEssentialWork);
```

`myNonEssentialWork` 函数在被调用的时候会接收一个 `deadline` 参数, 它是一个对象, 其中一个字段是一个函数, 这个函数返回一个数字, 代表当前渲染帧的剩余时间:

```javascript
function myNonEssentialWork (deadline) {
  while (deadline.timeRemaining() > 0)
    doWorkIfNeeded();
}
```

`timeRemaining()` 方法会返回最新的值. 当返回 0 时可以继续调度其它工作:

```javascript
function myNonEssentialWork (deadline) {
  while (deadline.timeRemaining() > 0 && tasks.length > 0)
    doWorkIfNeeded();

  if (tasks.length > 0)
    requestIdleCallback(myNonEssentialWork);
}
```

## 确保你的函数被调用

------

当有大量的任务需要执行时, 某个 callback 回调永远不会执行怎么办? 好吧, 尽管 `requestIdleCallback` 和 `requestAnimationFrame` 很像, 但是也有不同点. `requestIdleCallback` 接收第二个参数, 它是一个包含 **timeout 属性**的对象. 如果指定了 timeout 的值, 就意味着告诉浏览器在这个时间段后, 某个 callback 回调要强制执行:

```javascript
// 执行任务前最多等待 2 秒
requestIdleCallback(processPendingAnalyticsEvents, { timeout: 2000 });
```

如果 callback 回调函数是因为超时而被触发的, 此时会出现以下两种情况:

- `timeRemaining()` 会返回 0.
- `deadline` 对象的 `didTimeout` 属性会变为 true.

如果你发现 `didTimeout` 的值变为 true, 那么表明某个任务将要被强制执行了:

```javascript
function myNonEssentialWork (deadline) {

  // 在还有空闲时间或者超时的情况下执行任务
  while ((deadline.timeRemaining() > 0 || deadline.didTimeout) &&
         tasks.length > 0)
    doWorkIfNeeded();

  if (tasks.length > 0)
    requestIdleCallback(myNonEssentialWork);
}
```

手动设置 `timeout` 参数可能会导致 app 无响应, 影响用户体验, 所以尽量让浏览器自行来计算决定.

## 使用 requestIdleCallback 发送统计数据

------

让我们看一下使用 `requestIdleCallback` 发送统计数据, 我们将跟踪一个导航菜单的触摸事件. 由于导航菜单通常会执行一些动画, 所以我们想避免同步的触发统计事件. 我们会创建一个事件队列, 事件会在将来的某个时间段被触发:

```javascript
var eventsToSend = [];

function onNavOpenClick () {

  // 给菜单添加动画.
  menu.classList.add('open');

  // 将 JS 事件保存下来, 后续再执行.
  eventsToSend.push(
    {
      category: 'button',
      action: 'click',
      label: 'nav',
      value: 'open'
    });

  schedulePendingEvents();
}
```

现在我们使用 `requestIdleCallback` 去处理挂起的事件:

```javascript
function schedulePendingEvents() {

  // 只在没有任务调度的情况下调度新的任务
  if (isRequestIdleCallbackScheduled)
    return;

  isRequestIdleCallbackScheduled = true;

  if ('requestIdleCallback' in window) {
    // 执行任务前最多等待 2 秒
    requestIdleCallback(processPendingAnalyticsEvents, { timeout: 2000 });
  } else {
    processPendingAnalyticsEvents();
  }
}
```

这里你可以看到我设置了 2 秒的超时时间, 事实上这个值的取值取决于你的应用. 对于统计数据, 需要指定一个具体的超时时间, 以此来确保数据可以在某个帧被发送.

最后, 编写 `requestIdleCallback` 要执行的具体函数:

```javascript
function processPendingAnalyticsEvents (deadline) {

  // 重置调度状态, 让新的任务可以调度
  isRequestIdleCallbackScheduled = false;

  // 如果 requestIdleCallback 不存在, deadline 值就为空, 此时只要有必要, 就执行任务
  if (typeof deadline === 'undefined')
    deadline = { timeRemaining: function () { return Number.MAX_VALUE } };

  // 只要还有剩余时间并且事件队列不为空, 就一直执行任务
  while (deadline.timeRemaining() > 0 && eventsToSend.length > 0) {
    var evt = eventsToSend.pop();

    ga('send', 'event',
        evt.category,
        evt.action,
        evt.label,
        evt.value);
  }

  // 再次检查事件队列是否还有剩余的任务
  if (eventsToSend.length > 0)
    schedulePendingEvents();
}
```

在上面的示例中, 我假设如果 `requestIdleCallback` 不存在则立即发送统计数据. 然而, 在真正的应用中, 最好还是延迟发送, 确保不会影响其它操作.

## 使用 requestIdleCallback 更改 DOM

------

另一个使用 `requestIdleCallback` 可以明显提高性能的场景是——对于不是那么重要的 DOM 更新, 比如:

- 给无限自增的列表添加元素
- 懒加载列表

下图展示了 `requestIdleCallback` 如何利用一个关键帧:

![img](https://developers.google.com/web/updates/images/2015-08-27-using-requestidlecallback/frame.jpg)

可以看到浏览器在每一帧可能会执行很多回调函数, 所以你不要想着在每一帧执行太多的任务.

这种机制刚好与 `setImmediate` 是相反的, `setImmediate` 会在每一帧开始的时候执行回调任务.

如果在某一帧快结束的时候触发了回调函数, 那么该回调会在当前帧被提交之后再调度, 这意味着即将应用修改后的样式, 同样也会计算布局. 如果在 `requestIdleCallback` 内部更改 DOM, 那么此时所做的计算布局等操作是无效的. 假如 `getBoundingClientRect`、`clientWidth` 此类的方法在下一帧读取样式, 浏览器会 [强制重绘和重排](https://developers.google.com/web/fundamentals/performance/rendering/avoid-large-complex-layouts-and-layout-thrashing#avoid-forced-synchronous-layouts), 这是一个潜在的性能瓶颈.

不要在 `requestIdleCallback` 内部进行 DOM 更改操作的另一个原因是: 这个更改操作需要的时间是不可预测的, 可能会超出浏览器提供的 deadline.

最好的实践是将 DOM 更改的相关操作放在 `requestAnimationFrame` 回调函数内部, 交给浏览器去安排. 这意味着你需要使用 `documentFragment` 来追加 DOM 元素. 如果你使用了虚拟 DOM 库, 你可以使用 `requestIdleCallback` 去记录 DOM 更新, 但是最后应用到真正的 DOM 上还是要交给 `requestAnimationFrame` 回调函数.

基于上面所述, 再来看一下代码:

```javascript
function processPendingElements (deadline) {

  // 如果 deadline 不存在, 只要有必要就执行任务
  if (typeof deadline === 'undefined')
    deadline = { timeRemaining: function () { return Number.MAX_VALUE } };

  if (!documentFragment)
    documentFragment = document.createDocumentFragment();

  // 只要还有剩余时间并且任务队列不为空, 就执行任务
  while (deadline.timeRemaining() > 0 && elementsToAdd.length > 0) {

    // 创建 DOM 元素
    var elToAdd = elementsToAdd.pop();
    var el = document.createElement(elToAdd.tag);
    el.textContent = elToAdd.content;

    // 追加到文档片段里面
    documentFragment.appendChild(el);

    // 不要立即将 fragment 片段插入到页面上, 等下一个渲染帧的 requestAnimationFrame 回调函数执行的时候, 再去插入
    scheduleVisualUpdateIfNeeded();
  }

  // 检查任务队列是否还有剩余任务
  if (elementsToAdd.length > 0)
    scheduleElementCreation();
}
```

在上面的代码中, 我创建了一个 DOM 元素, 并且使用 `textContent` 来填充它的值, 事实上真正的操作可能会有很多! 接着调用 `scheduleVisualUpdateIfNeeded` 方法, 在该方法内部定义了一个 `requestAnimationFrame` 回调, 将 `documentFragment` 插入到页面上:

```javascript
function scheduleVisualUpdateIfNeeded() {

  if (isVisualUpdateScheduled)
    return;

  isVisualUpdateScheduled = true;

  requestAnimationFrame(appendDocumentFragment);
}

function appendDocumentFragment() {
  // 将 fragment 片段插入到页面上, 并重置 fragment 的值
  document.body.appendChild(documentFragment);
  documentFragment = null;
}
```