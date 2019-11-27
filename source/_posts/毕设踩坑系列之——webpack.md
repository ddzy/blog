---
title: 毕设踩坑系列之——webpack
date: 2019-07-21 11:27:36
tags: [毕设]
categories: [frontend]
---

记录有关`webpack`的坑


<!-- more -->


## 更新

------

### [2019-7-21]

- Initial release

## 记录

------

### 1. webpack proxy代理配置, 导致所有的请求被限制在`localhost:8888`

为`proxy`配置添加前缀`/api`即可, 但是添加完成后, 后台静态目录无法访问, 静态资源不用添加`/api`前缀.