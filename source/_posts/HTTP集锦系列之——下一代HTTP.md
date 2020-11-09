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

### [2020-11-9]

#### Changed

- 更新 `HTTP/2` 和 `HTTP/3` 的一些特性细节

## 引言

------

以前根本不知道还有这个东西, 知道今天看了下面试题, 发现`HTTP`还是挺重要的, 所以这里加以补充.


## 记录

------

### HTTP/2

#### 流、帧

`HTTP/2` 规定: 同一个域名下的所有 `HTTP` 请求都在同一个 `TCP` 连接下进行. 不同 URL 的 HTTP 连接通过流来标识, 流就相当于一个管道; 而帧则是管道内部流通的数据单元, 帧包括两个部分:

- Head Frame
- Data Frame

每一个帧都有一个特定的标识, 标识它属于哪个流, 这样在多路复用的时候就不会发生数据错乱的情况.

#### 头部压缩

`HTTP/2` 维护了一个静态字典, 在每次发送 `HTTP` 请求的时候, 会首先检查 `HTTP Header` 中的字段是否存在于静态字典中, 比对并找出新的字段, 使用二进制编码后传输.

#### 二进制分帧

`HTTP/1.x` 采用文本的格式来传输数据, `HTTP/2` 则将文本拆分为更小的帧, 并且采用二进制编码后传输.

#### 多路复用

`HTTP/1.x` 中, 每发起一个 `HTTP` 请求, 都会创建一个 `TCP` 连接; 并且不同浏览器会对 `TCP` 连接数有限制 `HTTP/2` 则规定:

- 同一个域名下的所有 `HTTP` 请求, 都在同一个 `TCP` 连接下进行
- 不同 `URL` 的 `HTTP` 连接通过流来标识
- 使用帧来代替传统的文本格式传输数据

#### Server Push

在客户端请求 `HTML` 页面的时候, 服务端会将可能需要的资源一并返回给客户端, 避免再次请求.

### HTTP/3

#### QUIC

传统的 `HTTP` 协议都以 `TCP` 来传输数据, 但是由于 `TCP` 的超时重传机制, 在某个 Fragment 发送出错的时候, 会重新发送, 这时就会阻塞后续 Fragment 的发送. 而 `HTTP/3` 使用 `UDP` 来替代 `TCP`, 并且在 `UDP` 和 `HTTP` 之间增加了 `QUIC` 协议层, 基于 `UDP`, 来解决 `UDP` 的不安全性的问题.

## 参考

------

<!-- 参阅[文章](https://imququ.com/post/header-compression-in-http2.html) -->
- [HTTP/2头部压缩技术介绍](https://imququ.com/post/header-compression-in-http2.html)
- [HTTP/3为什么比HTTP/2靠谱?](http://www.sohu.com/a/299243519_115128)