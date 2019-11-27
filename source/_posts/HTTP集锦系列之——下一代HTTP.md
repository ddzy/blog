---
title: HTTP集锦系列之——下一代HTTP
date: 2019-03-04 15:59:10
tags: [http]
categories: [frontend, backend]
---

`HTTP/2`、`HTTP/3`到底有什么新特性? 一起来了解下


<!-- more -->


## 更新

------

### [2019-3-6]

#### Added

- 增加[引言](#引言)部分
- 补充`HTTP/2`、`HTTP/3`的各个特点具体描述
- 增加[参考](#参考)部分

### [2019-3-7]

#### Changed

- 修改`HTTP/2`相关特性

### [2019-3-12]

#### Fixed

- 修改`头部压缩`的HTTP/2为HTTP/1

### [2019-3-25]

#### Added

- 补充`Push Cache`(Server Push)机制

### [2019-4-23]

#### Changed

- 更改文章标题`title`

## 引言

------

以前根本不知道还有这个东西, 知道今天看了下面试题, 发现`HTTP`还是挺重要的, 所以这里加以补充.


## 记录

------

### HTTP/2

> 相对于`HTTP/1`引进了多种技术

- TCP多路复用
  - 解决`HTTP/1`同一域名下的请求并发数限制导致的`队头阻塞`问题
  - 允许同时通过单一的`HTTP/2`连接发起多重的request-response消息
- 头部压缩
  - 解决`HTTP/1`中`首行`、`头部`未压缩的问题.
  - 静态字典
  - 静态哈夫曼表
- 二进制流
  - 应用层 - (+二进制分帧层) - 传输层
- Server Push(Push Cache)
  - 客户端请求资源时, `server`端主动将重要的资源一并返回.
  - 可与`H5`的`SSE`(监听服务端发送事件)配合使用
  - [与WebSocket的比较](https://blog.yyge.top/blog/2019/03/17/HTTP%E9%9B%86%E9%94%A6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94HTTP%E4%B8%8EWebSocket%E4%B9%8B%E5%88%AB/)

### HTTP/3

- QUIC
  - 用于代替`TCP`协议
  - QUIC基于`UDP`实现
  - 原生支持`多路复用`功能
  - 用一个连接中的`stream`相互没有依赖, 避免`HTTP/2`的丢包等待问题

## 参考

------

<!-- 参阅[文章](https://imququ.com/post/header-compression-in-http2.html) -->
- [HTTP/2头部压缩技术介绍](https://imququ.com/post/header-compression-in-http2.html)
- [HTTP/3为什么比HTTP/2靠谱?](http://www.sohu.com/a/299243519_115128)