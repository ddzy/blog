---
title: 前端基础重拾系列之——CSS定位解惑
date: 2019-03-13 20:06:17
tags: [css, interview]
categories: [frontend]
---

一直在用position, 殊不知其中竟然有这么多的`歪门邪道`, 话不多说, 作个总结.


<!-- more -->


## 记录

------

### static

- 遵循正常文档流
- 默认值

### inherit

- 继承自父级元素

### relative

- 遵循正常文档流
- 可设置`left`等属性

### absolute

- 脱离文档流
- 相对于离其最近的具有position, 且值不为`static`的父元素

### fixed

- 脱离文档流
- 相对于窗口

### sticky

- [relative](#absolute) + [fixed](#fixed)