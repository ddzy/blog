---
title: HTTP集锦系列之——request请求的内容
date: 2019-03-04 13:48:29
tags: [http]
categories: [frontend, backend]
---

对request请求的构成加以记录


<!-- more -->


## 更新

------

### [2019-4-23]

#### Changed

- 更改文章`title`、`tags`、`categories`项

### [2019-4-25]

#### Changed

- 改进文章格式

#### Added

- 新增结构`英文`表示
- 新增`空行`

## 一、引言

------

一个完整的HTTPRequest请求是怎样的? 记录一下

## 二、记录

------

### 2.1 构成

- 请求行(request line)
- 首部(request header)
- 空行
- 主体(request body)

#### 2.1.1 请求行

> GET /images/logo.gif HTTP/1.1

| Partial          | Meaning  |
| ---------------- | -------- |
| GET              | 请求方法 |
| /images/logo.gif | URL      |
| HTTP/1.1         | 协议版本 |

#### 2.1.2 首部

- 请求头(Request Header)
- 响应头(Response Header)

#### 2.1.3 空行

#### 2.1.4 主体

- payload参数