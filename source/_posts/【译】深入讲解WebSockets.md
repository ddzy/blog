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

## 更新

------

### [2021-3-1]

- Initial release

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

[WebSockets](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API) 定义了一个 API，用来在客户端与服务端之间建立一个 socket 连接。使得浏览器可以和服务器双向发送数据，此外，WebSockets 相较于 HTTP，在实时通信方面也做了一些优化。

### 实时通信

HTTP 请求会耗费几百个字节来发送 cookie 和其它的请求头，这无异于会增加实时通信的性能开销。

> 然而 WebSockets 仅仅只消耗 6 个字节（请求头占 2 个，传输的值占 4 个）。

所以 WebSockets 更适合实时的数据传输，并且由于性能开销比较小，用来实现低延时的应用也再好不过了。

### WebSocket 连接

直接给 WebSocket 构造函数传一个 URL 就可以开启一个 WebSocket 连接了。如果你要指定子协议，可以在第二个参数传入。

```js
// 创建一个新的 WebSocket 连接
let socketConnection = new WebSocket('ws://websocket.mysite.com');

// 创建一个带有子协议的 WebSocket 连接
let socketConnection = new WebSocket('ws://websocket.mysite.com', ['soap', 'xmpp']);
```

创建连接之后，你可以添加一些额外的事件句柄，分别当连接开启时、传输数据时、连接出错时触发：

```js
// WebSocket 连接开启之后，会发送一些数据给服务器
socketConnection.onopen = function () {
  connection.send('Hello, the socket connection is open!'); // 发送信息给服务器
};

// 打印错误
socketConnection.onerror = function (error) {
  console.log('WebSocket Error ' + error);
};

// 打印服务器返回的数据
socketConnection.onmessage = function (e) {
  console.log('Server: ' + e.data);
};
```

建立连接之后，会触发 WebSocket 实例上的 `onopen` 事件，此时就意味着完成了握手，之后就可以在任意时刻互相发送数据了；当客户端的 WebSocket 接收到了数据，就会触发 `onmessage` 事件；而 `onerror` 事件则可以用来处理异常。

**你可能会问：“创建一个连接，监听发送消息的事件”，这个操作不是很常见吗？有什么新意吗？**

事实上，在 WebSockets 中针对连接做处理是很重要的。

> 我们处理 WebSocket 连接的方式以及当连接异常的时候进行重连的操作，决定了会话的整体容错程度。

### 断线重连的容错机制

目前使用 WebSockets 普遍遇到的问题是：当客户端或者服务器没有响应的时候如何断开连接，为了解决这个问题，你应当实现一套优雅的机制。特别是如果某个 WebSocket 连接的生命周期比较长的话，就需要定义一个方法，周期性的刷新连接（本质上就是重复的关闭/开启连接）以确保当前连接的有效性，通过这种方式来实现一个平滑的会话。

### 可伸缩的连接

因为服务器需要保持高可用性来让 WebSockets 进行持久化连接，所以需要实现可伸缩的服务器，用来应对大量的 WebSocket 连接。然而，在一个 `ws` 连接开启之后的大多数时间，该连接都是闲置的。

**因此你可能会问：后端怎么实现可伸缩的 WebSockets？**

实现可伸缩的 WebSocket 是一项复杂的任务，需要用一个类似背板的东西来记录出现故障的服务器节点的连接和已发送的信息。

> 此外，实现可伸缩的 WebSocket 的最佳实践可能就是：结合开启的连接数量，实现一个向外拓展的策略。

因为用户可能并不会频繁的进行重连操作，所以基于已开启的 WebSockets 连接来改善可伸缩性是很有意义的。

## 数据传输模式

------

Web Sockets 有多种不同的模式来传输数据。你可以直接通过 WebSockets 发送消息，也可以向客户端推送消息。

### 给 web app 发送通知

在 APP 内部发送通知是 WebSockets 的一个很常见的用途，WebSockets 仅仅只在新的消息可用时通知浏览器。

每当用户收到消息通知，并且进入通知详情页的时候，应用程序就会发起一个 HTTP 请求来获取该消息的内容。

> 根据上面的用途，WebSocket 并不会把消息的具体内容发给前端，而是传递一个“信号”，告诉前端：“这个消息可用了”。

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