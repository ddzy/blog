---
title: 【译】NormalizeCSS和ResetCSS，我该如何选择？
date: 2021-02-22 10:10:28
tags: [css, web]
categories: [frontend, translation]
---

原文链接：

https://elad.medium.com/normalize-css-or-css-reset-9d75175c5d1e

本文已同步归纳在 [Github](https://github.com/ddzy/translations)。

<!-- more -->

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

**`Normalize.css`** 是一个小型 **CSS** 文件，为 HTML 元素的默认样式提供了跨浏览器的一致性。

这就意味着如果我们在 W3C 上查阅浏览器所应用的样式的规范的时候，不同浏览器的规范是不一样的，而 `normalize.css` 会修复不同浏览器产生的样式差异。

但是对于 IE 和 EDGE 这样的浏览器，`normalize.css` 并不能按照规范来抹平差异，因此 `normalize.css` 会把 IE 和 EDGE 中特定的样式应用到其它浏览器中。

**举个实际的例子**：对于类似 `<article>` / `<aside>` / `<nav>` / `<section>` 标签内部的 `<h1>` 标签，Chrome、Safari 和 Firefox 渲染得到的最终 font-size 会小于单独的 `<h1>` 标签，并且 margin 大小也有所不同。`<article>` / `<aside>` / `<nav>` / `<section>` 标签内部的 `<h1>` 标签在 Chrome、Safari、Firefox 的默认样式表（User-Agent-Stylesheet）里的样式如下：

```css
:-webkit-any(article,aside,nav,section) h1 {
  font-size: 1.5em;
  margin-block-start: 0.83em;
  margin-block-end: 0.83em;
}
```

而 IE 和 EDGE 则很少出现上述这种情况，所以理论上我们可以把同样的样式直接复用到 IE 和 EDGE 浏览器上，**但是由于 IE/EDGE 并没有 “any” 选择器**，所以直接复用是不可取的。因此，Normalize CSS 索性直接采用 IE/EDGE 中的 `<h1>` 样式，作为基准样式。

示例代码如下：

```css
/*
  Chrome、Firefox 和 Safari 浏览器中的 `<article>` / `<aside>` / `<nav>` / `<section>` 标签内部的 `<h1>` 标签的正确的文字大小和外边距
*/
h1 {  font-size: 2em;  margin: 0.67em 0; }
```

[Normalize.css](https://github.com/necolas/normalize.css/blob/master/normalize.css) 是由 [Nicolas Gallagher](https://github.com/necolas) 在 Github 上开源的一款项目：

https://github.com/necolas/normalize.css/blob/master/normalize.css

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