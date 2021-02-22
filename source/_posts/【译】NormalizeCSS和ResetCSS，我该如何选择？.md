---
title: 【译】NormalizeCSS和ResetCSS，我该如何选择？
date: 2021-02-22 10:10:28
tags: [css, web]
categories: [frontend, translation]
---

原文链接：

https://elad.medium.com/normalize-css-or-css-reset-9d75175c5d1e

本文已同步归纳在 [Github](https://github.com/ddzy/translations)。

## 更新

------

### [2021-2-22]

- Initial release

## 概述

------

通常情况下，我们希望某个 HTML 元素在不同浏览器上具有相同的展示效果。但是遗憾的是，每个浏览器处理 HTML 展示的方式都不同。

那么到底是使用 **Normalize CSS** 还是 **CSS Reset** 来消除不同浏览器的样式差异，目前还是存在争议的。

![1.png](https://oos.blog.yyge.top/2021/2/22/【译】NormalizeCSS和ResetCSS，我该如何选择？/images/1.png)

在深入讲解我推荐的使用方式之前，我首先要解释一下几个大家可能不太熟悉的术语。

### CSS 的 User-Agent 样式

在渲染 HTML 页面的时候，浏览器会在应用你自己编写的样式之前，首先应用其自身的基础样式表。举个例子，从 `<h1>` 到 `<h6>` 的 HTML 标签在所有的浏览器里面的展示效果都和其它普通文本不同：一般情况下，诸如 `<h1>` 这类的标签的文字大小会大一些、字体会粗一些（`font-weight: bold`），并且会有向上 & 向下两个方向的外边距。

![2.png](https://oos.blog.yyge.top/2021/2/22/【译】NormalizeCSS和ResetCSS，我该如何选择？/images/2.png)

虽然所有浏览器都应用了其默认样式表，但是每个浏览器的默认样式表彼此之前也是存在差异的，所以会产生一些问题，这也就是本文所探讨。

有两种方式可以解决不同浏览器的样式不一致的问题：**Normalize CSS** 和 **CSS Reset**。你可以把 **Normalize CSS 想象为一个温和的小绵羊**；把 **CSS Reset 想象为一个粗鲁的大怪兽**。下面，让我来举例讲解。

### Normalize CSS

### Reset CSS

## 如何同时使用 Normalize CSS & CSS Reset

------

### 编写你自己的 CSS Reset

### 基础布局样式

### 结合

## 总结

------

## 我的 CSS 系列文章

------