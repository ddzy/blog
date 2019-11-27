---
title: 前端基础重拾系列之——CSS-BFC规范
date: 2019-03-14 18:38:52
tags: [css, interview]
categories: [frontend]
---

所谓的BFC, 就是一个布局规范, 之前也有简单了解过, 今晚有空, 还是记录下来.


<!-- more -->


## 更新

------

### [2019-3-23]

#### Changed

- 更改`触发条件`相关内容

## 记录

------

> 块级格式化上下文

### 布局规则

- 内部的`block`元素在垂直方向上依次放置
- 两个相邻的`block`元素的margin会发生重叠
- `BFC`是页面上的一个隔离的独立容器, 容器里面的子元素不会影响到外面的元素

- 相邻的BFC元素依次放置
- `overflow: hidden`实现多栏布局

### 触发条件

- float !== none
- overflow !== visible
- position !== static | relative
- display
  - inline-block
  - flex