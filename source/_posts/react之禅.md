---
title: react之禅
date: 2019-07-17 19:06:36
tags: [react, js]
categories: [frontend]
---

好几天没更新博客了, 期末考完试到现在, 一直在抽空阅读`react`源码. 在此之前, 已经创作了[react-router-dom](https://github.com/ddzy/yyg-react-router-dom)源码布道系列, 处于对`react`的膜拜心理, 还是花些时间看看源码, 故将所有的笔记整理一下.


<!-- more -->

## 更新

------

### [2019-7-17]

- Initial release

## 前言

------

> 博客始终与`github`保持无缝同步:
> https://github.com/ddzy/react-reading-sources

闲暇之时, 精心沉淀, 品一口82年拉菲, 啵一口陈年老酿, 读一首`react`源码, 不失为一大乐事~

这是闲时阅读`react`源码过程中的心得、笔记, 只是单纯的个人理解~

直到读源码之前, 我一直对`react`保持好奇、憧憬的态度.

**何为好奇?** 仅仅一个`setState`, 就能引发整个`react`应用的更新, 仅仅一个`state`, 就能随心所欲的切换状态.

**何为憧憬?** `react@16.8`的`hooks`着实很舒服, 我的`毕设`当中也大量使用, 那么诸如`useState`、`useEffect`是如何实现的? 又让我对`react`内部机理产生强烈憧憬.

所以, 在期末考之余创建了本仓库, 旨在将自己阅读源码过程中的`心得`、`体会`记录下来. 由于`react`源码着实太过庞大和难以理解, 所以仓库笔记可能跳跃度很高, 要完成可能还要很长很长时间吧.

## 目录

------

| Name                                               | Link                                                                                                                                                                                                      | Github                                                                    |
| -------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| react之禅系列之——react几大更新阶段                 | [博客地址](https://blog.yyge.top/blog/2019/07/17/react%E4%B9%8B%E7%A6%85%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94react%E5%87%A0%E5%A4%A7%E6%9B%B4%E6%96%B0%E9%98%B6%E6%AE%B5/)                        | [github同步更新](https://github.com/ddzy/react-reading-sources/issues/3)  |
| react之禅系列之——fiber的几个重要属性               | [博客地址](https://blog.yyge.top/blog/2019/07/17/react%E4%B9%8B%E7%A6%85%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94fiber%E7%9A%84%E5%87%A0%E4%B8%AA%E9%87%8D%E8%A6%81%E5%B1%9E%E6%80%A7/)               | [github同步更新](https://github.com/ddzy/react-reading-sources/issues/4)  |
| react之禅系列之——ReactDOM.render流程简单梳理       | [博客地址](https://blog.yyge.top/blog/2019/07/17/react%E4%B9%8B%E7%A6%85%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94ReactDOM-render%E6%B5%81%E7%A8%8B%E7%AE%80%E5%8D%95%E6%A2%B3%E7%90%86/)              | [github同步更新](https://github.com/ddzy/react-reading-sources/issues/5)  |
| react之禅系列之——fiber的updateQueue的基本结构      | [博客地址](https://blog.yyge.top/blog/2019/07/17/react%E4%B9%8B%E7%A6%85%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94fiber%E7%9A%84updateQueue%E7%9A%84%E5%9F%BA%E6%9C%AC%E7%BB%93%E6%9E%84/)             | [github同步更新](https://github.com/ddzy/react-reading-sources/issues/6)  |
| react之禅系列之——fiber.tag重要类型汇总             | [博客地址](https://blog.yyge.top/blog/2019/07/17/react%E4%B9%8B%E7%A6%85%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94fiber-tag%E9%87%8D%E8%A6%81%E7%B1%BB%E5%9E%8B%E6%B1%87%E6%80%BB/)                    | [github同步更新](https://github.com/ddzy/react-reading-sources/issues/7)  |
| react之禅系列之——函数组件(FunctionComponent)的更新 | [博客地址](https://blog.yyge.top/blog/2019/07/17/react%E4%B9%8B%E7%A6%85%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94%E5%87%BD%E6%95%B0%E7%BB%84%E4%BB%B6-FunctionComponent-%E7%9A%84%E6%9B%B4%E6%96%B0/) | [github同步更新](https://github.com/ddzy/react-reading-sources/issues/8)  |
| react之禅系列之——hooks存储结构                     | [博客地址](https://blog.yyge.top/blog/2019/07/17/react%E4%B9%8B%E7%A6%85%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94hooks%E5%AD%98%E5%82%A8%E7%BB%93%E6%9E%84/)                                          | [github同步更新](https://github.com/ddzy/react-reading-sources/issues/10) |
| react之禅系列之——hooks运行机制                     | [博客地址](https://blog.yyge.top/blog/2019/07/17/react%E4%B9%8B%E7%A6%85%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94hooks%E8%BF%90%E8%A1%8C%E6%9C%BA%E5%88%B6/)                                          | [github同步更新](https://github.com/ddzy/react-reading-sources/issues/11) |