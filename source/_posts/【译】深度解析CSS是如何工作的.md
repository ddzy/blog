---
title: 【译】深度解析CSS是如何工作的
date: 2021-02-05 08:52:50
tags: [css, web]
categories: [frontend, translation]
---

原文链接：

https://elad.medium.com/how-does-css-work-92fe7116916d

<!-- more -->

## 更新

------

### [2021-2-5]

- Initial release

## 概述

------

> 副标题（补充）：理解浏览器默认的样式行为

当你为网站中的某个 DOM 元素编写 CSS 样式之前，浏览器会给这个 DOM 元素添加一个默认的样式。

你有没有想过这些默认样式是由谁来决定的？

在这篇文章中，我将教会你：

- 浏览器中不同级别的 CSS
- 每一级 CSS 是如何工作的
- 我们该如何去控制

![1.png](https://oos.blog.yyge.top/2021/2/5/%E3%80%90%E8%AF%91%E3%80%91%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90CSS%E6%98%AF%E5%A6%82%E4%BD%95%E5%B7%A5%E4%BD%9C%E7%9A%84/images/1.png)

在我刚开始学习 CSS 的时候，我还以为所有的 CSS 默认样式都来自于浏览器自身的 “User-Agent Stylesheet” 文件。但是后来，我发现我错了，这只是冰山一角。

让我们开始吧！

## Level1——CSS 属性的默认样式

------

### 默认值

### 继承

## Level2——UserAgent（`浏览器默认`）样式表

------

### HTML 元素的特殊样式由谁来决定？

## Level3——Normalize CSS

------

### “Normalize CSS” 是什么？它是如何工作的？

### Normalize.css 项目

## Level4——CSS Reset

------

### “CSS Reset” 是什么？它是如何工作的？

### 综合理解 “Normalize CSS” 和 “CSS Reset”

### 总结上述的 4 个 Level

## Part5——CSS 样式重置关键字

------

### “inherit” 和 “initial”

### “initial” 产生的问题

### CSS 样式重置关键字总结

### 定义一个标准的样式

## Part6——“all” 属性

------

## “CSS Reset 的前景”

------

## 总结

------