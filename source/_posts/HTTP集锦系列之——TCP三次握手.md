---
title: HTTP集锦系列之——TCP三次握手
date: 2019-03-16 16:49:38
tags: [http, tcp]
categories: [frontend, backend]
---

困扰许久的问题, 虽说知道这个过程, 但是并未深究, 记录一下


<!-- more -->


## 过程

------

### 名词解析

| Name | Meaning                                        |
| ---- | ---------------------------------------------- |
| SYN  | 请求建立连接                                   |
| ACK  | 确认接受连接, 期望对方继续发送的那个数据包的序列号(加一) |
| FIN  | 请求关闭连接                                   |
| seq  | 数据包本身的序列号, 随机选取                   |v

### 握手过程

- 客户端发送`SYN`包、`seq = x`至服务器, 进入`SYN_SEND`状态.
- 服务器发送`SYN`包、`ACK = x + 1`、`seq = y`至客户端, 进入`SYN_RECEIVED`状态.
- 客户端发送`ACK = y + 1`, 确认接受连接, 双方进入`ESTABLISHED`状态.

## 补充

------

1. 明明两次握手可以解决的事, 为何要三次?

client发送了一个请求A, 由于网络延时原因造成了超时, 这时TCP会采用[超时重传机制](https://blog.yyge.top/blog/2019/03/16/HTTP%E9%9B%86%E9%94%A6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94TCP%E8%B6%85%E6%97%B6%E9%87%8D%E4%BC%A0%E6%9C%BA%E5%88%B6/)重新发送请求B, 假设B顺利完成数据传送, 此时A到达server端, server进入`ESTABLISHED`状态, 而此时client还是处于`CLOSED`状态, 造成资源浪费.