---
title: 毕设踩坑系列之——mongoose
date: 2019-07-21 11:27:22
tags: [毕设]
categories: [frontend]
---

记录有关`mongoose`的坑


<!-- more -->


## 更新

------

### [2019-7-21]

- Initial release

### [2019-8-31]

#### Added

- 新增问题`集合(Collection)新增了一个文档(Document)字段, 在之后的查找中获取不到`

## 记录

------

### 1. 存储相关

#### 1.1 关联存储问题

**解决办法**: 互相依赖的字段, 例如 `comments` & `replys`, 创建新的 `reply` 时, 要将其push同步到 `comments`中.

### 2. 业务相关

#### 2.1 mongoose中的数组不能用 arr.includes(id) 判断是否存在

**解决办法**: 使用 `v.equals(id)`.

#### 2.2 集合(Collection)新增了一个文档(Document)字段, 在之后的查找中获取不到

**解决办法:**

需要在当前`Model`调用`create()`的时候初始化字段.