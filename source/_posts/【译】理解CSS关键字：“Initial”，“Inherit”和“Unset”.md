---
title: 【译】理解CSS关键字：“Initial”，“Inherit”和“Unset”
date: 2021-02-20 08:33:20
tags: [css, web]
categories: [frontend, translation]
---

原文链接：

https://elad.medium.com/understanding-the-initial-inherit-and-unset-css-keywords-2d70b7121695

本文已归纳在 [Github](https://github.com/ddzy/translations)。

<!-- more -->

## 更新

------

### 2021-2-20

- Initial release

## 概述

------

CSS 中有各种各样的关键字。本文我将着重介绍这几个关键字：`initial`、`inherit` 以及较新的 `unset`。

我确信绝大多数开发者都见过这几个关键字，但是对于它们的原理确知之甚少。

![1.png](https://oos.blog.yyge.top/2021/2/20/%E3%80%90%E8%AF%91%E3%80%91%E7%90%86%E8%A7%A3CSS%E5%85%B3%E9%94%AE%E5%AD%97%EF%BC%9A%E2%80%9CInitial%E2%80%9D%EF%BC%8C%E2%80%9CInherit%E2%80%9D%E5%92%8C%E2%80%9CUnset%E2%80%9D/images/1.png)

在此之前的一段时间，我只知道这些关键字是用来重置 CSS 样式的，但是如果它们都起到了重置的效果，又为什么会有这么多种呢？它们之间的区别又是什么？**于是，我决定深入探索它们的原理**。

## 浏览器的基础样式

------

在开始讲解这些 CSS 关键字之前，我们首先要了解浏览器的基础样式是从哪儿来的。

### CSS 属性的初始值

每个 CSS 属性都有一个初始值，这个值和 HTML 元素没有任何关系。

如下图所示的是从 MDN 上摘取的 `initial` 的例子：

![2.jpeg](https://oos.blog.yyge.top/2021/2/20/%E3%80%90%E8%AF%91%E3%80%91%E7%90%86%E8%A7%A3CSS%E5%85%B3%E9%94%AE%E5%AD%97%EF%BC%9A%E2%80%9CInitial%E2%80%9D%EF%BC%8C%E2%80%9CInherit%E2%80%9D%E5%92%8C%E2%80%9CUnset%E2%80%9D/images/2.jpeg)

### 浏览器默认（User-Agent）样式表

在所有的 CSS 属性都设置完初始样式之后，紧接着浏览器会加载自身的样式表。该样式表不会对 CSS 属性的初始值作任何处理。

**user-agent 样式示例如下：**

![3.png](https://oos.blog.yyge.top/2021/2/20/%E3%80%90%E8%AF%91%E3%80%91%E7%90%86%E8%A7%A3CSS%E5%85%B3%E9%94%AE%E5%AD%97%EF%BC%9A%E2%80%9CInitial%E2%80%9D%EF%BC%8C%E2%80%9CInherit%E2%80%9D%E5%92%8C%E2%80%9CUnset%E2%80%9D/images/3.png)

HTML 元素是没有初始的样式值的！上图中 `<h1>` 标签的基础样式是从 user agent stylesheet 中获取的，而不是 CSS 属性的初始值 `initial`。

**下面，让我们开始讲解这些 CSS 关键字吧！**

## Inherit 关键字

------

## Initial 关键字

------

## Unset 关键字

------

### 为什么要使用 Unset？

## Revert 关键字

------

## 浏览器兼容性

------