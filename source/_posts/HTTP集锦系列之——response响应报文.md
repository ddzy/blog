---
title: HTTP集锦系列之——response响应报文
date: 2019-04-25 14:36:32
tags: [http]
categories: [frontend, backend]
---

对应上一篇的对于`Request`请求的结构分析, 本篇文章记录一下`Response`响应的结构.


<!-- more -->


## 更新

------

### [2019-4-25]

#### Added

- Initial Release

## 一、组成

- 状态行(status line)
- 响应头(response header)
- 空行
- 响应实体(response body)

## 二、结构分析

---

### 1.1 状态行

> HTTP/1.1 200 OK

| Partial | Meaning |
| --- | --- |
| HTTP/1.1 | 协议版本 |
| 200 | 状态码 |
| OK | 提示信息 |

### 1.2 响应头

- Cache Control
- Etag
- Access-Control-Allow-Origin

### 1.3 空行

- 告诉浏览器响应头到这里结束

### 1.4 响应体

- payload参数