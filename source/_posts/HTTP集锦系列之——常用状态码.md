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

## 引言

------

写后台的时候, 给前端返回的都是一股脑的`status = 200`, 对于状态码的理解不是很深刻.

## 记录

------

| Status Code | Meaning                                    |
| ----------- | ------------------------------------------ |
| 200         | server端正确处理了client发送的请求         |
| 304         | server端的资源未发生改变                   |
| 401         | client身份认证错误                         |
| 403         | client的访问资源请求被拒绝, server权限问题 |
| 404         | server不存在请求的资源                     |
| 500         | server发生错误                             |
| 503         | server无法处理请求                         |