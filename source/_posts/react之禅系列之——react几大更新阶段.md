---
title: react之禅系列之——react几大更新阶段
date: 2019-07-17 19:27:10
tags: [react, js]
categories: [frontend]
---

源码中多次出现诸如`isRendering`、`isWorking`、`isCommitting`此类的全局标志, 特此记录下.


<!-- more -->


## 更新

------

### [2019-7-17]

- Initial release

## 记录

------

看源码的过程中, 多次出现了诸如:

- `isRendering` 在performWorkOnRoot时被设置为true, 其包括isWorking阶段
- `isWorking` 在renderRoot时被设置为true, 此时已经开始从根节点遍历渲染
- `isCommitting`
- ...

此类的全局变量, 用来标志渲染的所处阶段.

> 那么, 它们之间的关系是怎样的?

画了张图, 简单概括下:

![5-react的几大更新阶段](https://user-images.githubusercontent.com/33921398/60557379-efb81b00-9d77-11e9-9f09-46575d454f56.png)

> 问题又来了, 在每个阶段都干了什么事情?

- **reconcile**  计算`expirationTime`、创建`update`
- **schedule**  调度
- **renderrer**  渲染
- **commit**    提交(`DOM diff`)