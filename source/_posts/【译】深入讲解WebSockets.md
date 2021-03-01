---
title: 【译】深入讲解WebSockets
date: 2021-03-01 09:41:55
tags: [http, websocket]
categories: [frontend, backend, translation]
---

原文链接：

https://blog.bitsrc.io/deep-dive-into-websockets-e6c4c7622423

本文已同步归纳在 [Github](https://github.com/ddzy/translations)。

<!-- more -->

## 概述

------

![1.jpeg](https://oos.blog.yyge.top/2021/3/1/【译】深入讲解WebSockets/images/1.jpeg)

在早期的互联网世界里面，web 应用的构建都基于 HTTP 请求，并且 HTTP 请求由用户的交互触发。随着技术的进步，出现了以实时数据传输和双向通信为要求的**低延时应用**，比如：

- 多人在线游戏
- 聊天应用
- 实时的更新 RSS 订阅
- 体育直播等

WebSockets 就是为解决这些问题而生的。一些三方库简化了 WebSockets，让我们可以方便的使用 WebSockets 而不需要懂太多的底层原理，提高开发效率。

本文致力于讲解 WebSockets 的一些基本特性，为大家知识扫盲。

## WebSockets 架构

------

WebSockets 定义了一个 API，用来在客户端与服务端之间建立一个 socket 连接。使得浏览器可以和服务器双向发送数据，此外，WebSockets 相较于 HTTP，在实时通信方面也做了一些优化。

### 实时通信

### WebSocket 连接

### 断线重连

### 拓展连接

## 数据转换模式

------

### 给 web app 发送通知

### 实时传输数据

## 数据压缩

------

## Web Sockets 安全性

------

## 浏览器兼容性

------

## 总结

------

## 推荐阅读

------