---
title: HTTP集锦系列之——WebSocket与socket
date: 2019-03-08 14:01:14
tags: [http, websocket, socket]
categories: [frontend, backend]
---

毕设中可能会用到`socket.io`库, 之前也有简单使用过, 但只是停留在用的层面, 对其底层协议模模糊糊, 今天就来总结一下.


<!-- more -->


## 记录

------

### WebSocket

> H5新增的协议

- 以`HTTP`协议为基础(在Header字段中添加了`Upgrade`和`Connection`字段).
- `HTTP/1.0`协议只能一个request, 一个response. `HTTP/1.1`中新增了`Keep-alive`字段, 可以在一个TCP连接中,发送多个request, 接收多个response.
- `WebSocket`实现了双向通信. 也就是说服务器可以主动推送消息给客户端

### Socket

> 抽象接口模型(套接字层)

在网络中, 可以理解为连接的两端. 封装了`传输层`的复杂协议, 供开发者使用