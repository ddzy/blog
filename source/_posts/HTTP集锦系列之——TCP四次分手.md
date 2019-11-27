---
title: HTTP集锦系列之——TCP四次分手
date: 2019-03-16 16:50:14
tags: [http, tcp]
categories: [frontend, backend]
---

既然建立连接有[三次握手](https://blog.yyge.top/blog/2019/03/16/HTTP%E9%9B%86%E9%94%A6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94TCP%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B/), 那么相应的, 断开连接时也会经历四次握手, 而过程也是大致相同的.


<!-- more -->


## 过程

------

- client发送释放连接请求, 发送`FIN`、`seq = x`到server, 进入`FIN-WAIT-1`状态.
- server发送`ACK = x + 1`、`seq = y`至client. 进入`CLOSE-WAIT`状态, 此时仍可以发送资源到client.
- 等待资源发送完成之后, 再次发送`FIN, seq = z`到client, 进入`LAST-ACK`状态, client进入`FIN-WAIT-2`状态
- client发出`ACK`请求应答, 进入`TIME-WAIT`状态

## 补充

------

> 延时确认机制?

通过`延时确认`, 可以将第二、三次握手合并, 延迟ACK包的发送

> 为何要进入`TIME-WAIT`状态, 等待`4min`?

确保server端能正常接受到`ACK`确认关闭请求.