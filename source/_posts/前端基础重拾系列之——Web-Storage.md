---
title: 前端基础重拾系列之——Web Storage
date: 2019-03-07 16:06:38
tags: [browser, interview]
categories: [frontend]
---

作为开发中一个必不可少的`辅助工具`, 存储机制值得深究


<!-- more -->


## 目录

------

### cookie

- 最大4KB
- 维持状态、传递状态的载体
- 不作存储

### webStorage

| 名称           | 存储大小 | 生命周期             |
| -------------- | -------- | -------------------- |
| localStorage   | 5M - 10M | 持久化, 除非手动清理 |
| sessionStorage | 5M - 10M | 页面关闭清理         |

- `webStorage`空间限制与浏览器有关
- `localStorage`与`sessionStorage`区别
  - 前者会永久存储, 除非用户手动清理. 后者在页面关闭后会清理
  - 后者必须在同一个浏览器窗口打开

### indexDB

> 运行在浏览器上的`非关系型`数据库

- 存储博客类网站草稿