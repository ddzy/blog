---
title: ioredis常用命令集锦
date: 2019-08-14 13:17:12
tags: [redis, ioredis]
categories: [frontend]
---

毕设采用了`Redis`作为缓存中间件, 但是自己对于`Redis`不是很理解, 第一次用, 所以将常用的命令做个记录, 便于后续参考.


<!-- more -->


## 更新

------

### [2019-8-14]

- Initial release

### [2019-8-24]

#### Added

- 新增[中文文档](http://doc.redisfans.com/)链接

## 前言

------

今天刚接触`Redis`, 不知不觉已经混淆了其和`MongoDB`的异同, 当然只是概念上的区别.

看到了一篇文章, 解了心头之惑:

[勿用 redis 的多库](http://blog.kankanan.com/article/52ff7528-redis-7684591a5e93.html)

值得注意的是:

- `Redis`中的数据库是以`0-16`之类的数字来表示, 而普通的数据库则是以`字符串`来标识, 个人觉得有些鸡肋.
- `Redis`中的`Key`等同于`SQL`的`Table`.
- 每个`Key`可以定义不同的数据结构(`Set`、`Map`、`String`...).

## 记录

------

基本都是简单的命令, 所以不做过多记录, 用时参考一下`中文文档`就行了:

http://doc.redisfans.com/