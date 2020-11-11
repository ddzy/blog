---
title: HTTP集锦系列之——一个完整的url是怎样的?
date: 2019-03-03 18:19:29
tags: [http, url]
categories: [frontend, backend]
---

每个网页都有其特定的`url`, 或简或繁, 它的组成成分又是啥呢?


<!-- more -->


## 更新

------

### [2019-3-18]

#### Added

- 增加`WWW`标识

#### Changed

- 移除`参考`部分

### [2019-3-23]

#### Added

- 新增url四大构成

### [2019-3-28]

#### Changed

- 更改`url构成`

### [2019-4-23]

#### Changed

- 更改文章`title`

### [2020-11-11]

#### Changed

- 更改 URL 组成

## 引言

------

每天都在和 `web` 打交道, 脑子里充斥着各种业务, 对 `url` 这块也没有很深的了解

## 记录

------

> http(s)://www.yyge.top:80/index.php?name=duan&age=20...

总的来说, 包括五大部分:

- 协议
- 主机(域名)
- 端口号
- 虚拟资源目录
- 查询参数

| Partial           | Meaning              |
| ----------------- | -------------------- |
| http(s)           | 超文本传输(安全)协议 |
| www               | 主机名               |
| yyge.top          | 域名                 |
| :80               | 端口号               |
| /index.php        | 虚拟资源目录         |
| ?name=duan&age=20 | 查询参数             |