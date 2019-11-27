---
title: HTTP集锦系列之——HTTP与WebSocket之别
date: 2019-03-17 17:18:38
tags: [http, websocket, socket]
categories: [frontend, backend]
---


`WebSocket`作为`HTTP`的补充和延申, 它们的关系又是怎样的?


<!-- more -->


## 更新

------

### [2019-3-17]

- Initial Release

### [2019-10-30]

#### Changed

- 更改文章结构

#### Added

- 新增`Websocket`、`Socket`、`HTTP`三者的介绍及异同

## Socket

------

`Socket`是为了方便操作`传输层`的协议, 抽象出来的一层, 位于`应用层`与`传输层`之间, 可以理解为一个`API`接口

## Websocket

------

### 是什么?

`Websocket`是`H5`提出的一个新的协议, 与`HTTP`协议同属于应用层, 也是基于`TCP`来进行三次握手. 但是与`HTTP`不同的是, `Websocket`则采用了`全双工通信`通信机制, 那么:

> 何谓全双工通信?

比如说有 A 和 B 两个端传输数据, 那么数据流可以从 A 到 B, 也可以从 B 到 A, 并且可以同时进行.

### 怎么做?

`Websocket`也是基于`TCP`来传输数据, 同样它的连接过程也是基于`HTTP`, 在首次进行`HTTP`请求的时候, 客户端通过`Request Header`来携带`Connection: Upgrade`以及`Upgrade: websocket`字段, 来告知服务端将此次的连接升级为`Webscoket`连接, 服务端收到标识字段, 同样在自己的`Response Header`添加相应的字段

## HTTP

------

众所周知, `HTTP`是`无连接、无状态的`, 并且是`半双工通信`机制, 也就是说, 同一时刻, 只能允许单向的数据流, 并且服务端不能主动发送数据

## 发展历程

- `HTTP/1.0`采用了`短链接`的方式, 也就是说一个完整的请求之后, 会断开连接
- `HTTP/1.1`通过`Keep-Alive`来进行`长连接`, 可以在一个连接通道内发送多次请求
- `Websocket`解决了`HTTP`的服务端不能主动推送数据的问题, 属于全双工通信