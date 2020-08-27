---
title: Vue 双向绑定的两种实现
date: 2020-08-27 15:42:56
tags: [vue, js]
categories: [frontend]
---

写过一小段时间的 Vue, 发现在特定的场景下, 其可以灵活地操控 JS 对象、输入框、展示视图三者的关系. 所以使用 ES5 的 defineProperty 和 ES6 的 Proxy 分别简单的实现这种特性.


<!-- more -->


## 更新

------

### [2020-8-27]

- Initital release

##