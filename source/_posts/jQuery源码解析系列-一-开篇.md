---
title: jQuery源码解析系列(一) -开篇
date: 2019-03-28 15:52:22
tags: [jQuery, ts]
categories: [frontend]
---

此为jQuery源码解析系列的第一篇, 对日后的源码阅读工作做一个简单阐述


<!-- more -->


## 一、更新

------

### [2019-4-21]

#### Changed

- 改进文章格式🐷

## 二、前言

上个寒假, 花了些时间阅读了`react-router-dom`源码, 并创作了[react-router-dom源码解析系列](https://blog.yyge.top/blog/tags/react/), 收获颇丰. 虽说不是`专业读源码三十年`的样子, 但是最起码了解了其底层大致机理.

> **PS**: 逼自己忙起来

这不, 最近刚好在恶补基础, 所以机智的想到用`jQuery`源码作为自己的复习大纲, 为什么呢? 通过源码阅读, 可以对

- 数据结构
- 设计模式
- 原生API
- 代码规范和优化
- 基本兼容知识

等做一个简单汇总.

在源码阅读的过程中, 会用ts做简单重构, 加深对ts的了解, 🆗, 话不多说, fighting!

## 三、示例代码

------

自己重构的代码放置在了[Gayhub](https://github.com/ddzy/my-simple-jquery/tree/master)