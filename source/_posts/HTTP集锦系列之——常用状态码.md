---
title: HTTP集锦系列之——常用状态码
date: 2019-03-04 14:20:41
tags: [http]
categories: [frontend, backend]
---

对遇到的状态码做一个简单总结


<!-- more -->


## 更新

------

### [2019-4-23]

#### Changed

- 修改文章相关配置项
  - title
  - tags
  - categories

### [2020-11-4]

#### Added

- 新增状态码对应的英文描述

### [2021-2-24]

#### Added

- 新增 `405` 状态码

## 引言

------

写后台的时候, 给前端返回的都是一股脑的`status = 200`, 对于状态码的理解不是很深刻.

## 记录

------

| Status Code | Description           | Meaning                                    |
| ----------- | --------------------- | ------------------------------------------ |
| 200         | OK                    | server端正确处理了client发送的请求         |
| 304         | Not Modified          | server端的资源未发生改变                   |
| 401         | Unauthorized          | client身份认证错误                         |
| 403         | Forbbiden             | client的访问资源请求被拒绝, server权限问题 |
| 404         | Not Found             | server不存在请求的资源                     |
| 405         | Method Not Allowed    | 请求方式错误                               |
| 500         | Internal Server Error | server发生错误                             |
| 503         | Service Unavailable   | server无法处理请求                         |