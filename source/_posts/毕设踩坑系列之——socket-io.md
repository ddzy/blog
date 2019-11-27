---
title: 毕设踩坑系列之——socket.io
date: 2019-07-27 08:04:08
tags: [毕设, socket]
categories: [frontend]
---

聊天室模块用到了`socket.io`包, 遇到的坑还是挺多的, 特此记录于此.


<!-- more -->


## 更新

------

### [2019-7-27]

- Initial release

### [2019-7-29]

#### Changed

- 更新代码格式

### [2019-7-30]

#### Added

- 新增问题`刷新服务器后, socket.io会发送多次数据`
- 新增问题`socket.io无法实时接收发送信息, 需要同时刷新服务器和客户端`

### [2019-8-12]

#### Added

- 新增问题`客户端的socket监听事件会触发 2^n 次`
- 新增问题`在componentDidMount中设置的socket.on监听, 只能作用于一个会话, 因为componentDidMount只执行了一次, 变化的只是传递的路由参数.`

### [2019-8-19]

#### Added

- 新增问题`一次单聊会话中, 接收方的所有好友都能收到本次独立会话的消息`

### [2019-8-31]

#### Added

- 新增问题`Firefox下报Websocket无法连接的错误`

### [2019-9-22]

#### Added

- 新增问题`重复创建了多个重复的websocket连接`

### [2019-9-25]

#### Added

- 新增问题`socket.removeAllEventListener引发的惨案`

## 记录

------

### 1. Access to XMLHttpRequest at 'http://localhost:8888/socket.io/?EIO=3&transport=polling&t=MmmlqWJ' from origin 'http://localhost:3000' has been blocked by CORS policy: The value of the 'Access-Control-Allow-Origin' header in the response must not be the wildcard '*' when the request's credentials mode is 'include'. The credentials mode of requests initiated by the XMLHttpRequest is controlled by the withCredentials attribute.

**相关依赖:**

- socket.io@^2.2.0,
- socket.io-client@^2.2.0,
- @types/socket.io@^2.1.2,
- @types/socket.io-client@^1.4.32,

**产生原因:**

- 原因一

使用`koa-cors`库的时候, 如果内部设置了`credentials: true`和`origin: *`, 则会出现此报错.

- 原因二

`server.js`开启服务器并监听端口的方式有误, 如果使用了`socket.io`, 会使用另外一种监听方式.

**解决方式:**

- 解决一

参考`思否`的文章: [axios的cookie跨域以及相关配置](https://segmentfault.com/a/1190000011811117).

<details>
<summary>展开代码</summary>

```ts
app.use(require('koa-cors')({
  maxAge: 60 * 60 * 24 * 4,
  + origin: '*',
}));
```
</details>

- 解决二

参考`脚本之家`的相关文章: [详解如何使用koa实现socket.io官网的例子](https://www.jb51.net/article/150118.htm)

不使用`socket.io`的例子:

<details>
<summary>展开代码</summary>

```ts
import * as Koa from 'koa';

const app: Koa = new Koa();

app.listen(8888);
```
</details>

使用了`socket.io`的例子:

<details>
<summary>展开代码</summary>

```ts
import * as Koa from 'koa';
import * as IO from 'socket.io'
import * as http from 'http';

const app: Koa = new Koa();
const server: http.Server = http.createServer(app.callback());
const io: IO.Server = IO(server);

server.listen(8888);
```
</details>

附上前台的`socket.io-client`的连接方式:

<details>
<summary>展开代码</summary>

```ts
import * as IO from 'socket.io-client';

const socket = IO('ws://localhost:8888');

socket.emit();
socket.on();
```
</details>

### 2. 刷新服务器后, socket.io会发送多次数据(前台会打印多次)

**相关依赖:**

- socket.io@^2.2.0
- socket.io-client@^2.2.0
- @types/socket.io@^2.1.2
- @types/socket.io-client@^1.4.32

**产生原因:**

起初是想将`socket`有关的逻辑抽离成单独的模块, 所以直接按照如下的方式来使用:

- server.ts

<details>
<summary>展开代码</summary>

```ts
import * as IO from 'socket.io';

import { handleChat } from './controller/chat/create/chatCreate';


const app: Koa = new Koa();
const server: Http.Server = Http.createServer(app.callback());
const io: IO.Server = IO(server);

// 自己抽离的模块, 所有聊天室相关的逻辑均放在此处处理
handleChat(io.of('/chat'));
```
</details>

- chatCreate.ts

<details>
<summary>展开代码</summary>

```ts
import * as IO from 'socket.io';

export function handleChat(chatNamespace: IO.Namespace) {
  // 关键点在这里
  chatNamespace.on('connection', () => {
    socket.on('sendChatSingleMessage', async (messageInfo: ISingleChatMessageProps) => {
      // ...
    });
  });
};
```
</details>

这样就导致了一个问题, 那就是每次刷新服务器, 都会追加一个新的`connection`监听事件, 逐步递增, 所以就出现了每次刷新之后, 请求执行多次的问题.

**解决办法:**

> 参考cnode社区的一篇讨论: https://cnodejs.org/topic/518e0a7563e9f8a5427cefa6

很简单, 只需要将`io.on('connection', xxx)`提取到`server.ts`中就行了:

<details>
<summary>展开代码</summary>

```ts
io
  .of('/chat')
  .on('connection', handleChat);
```
</details>

### 3. socket.io无法实时接收发送信息, 需要同时刷新服务器和客户端

**相关依赖:**

- socket.io@^2.2.0
- socket.io-client@^2.2.0
- @types/socket.io@^2.1.2
- @types/socket.io-client@^1.4.32

**产生原因:**

在前台使用`socket.io-client`的方式有问题:

<details>
<summary>展开代码</summary>

```ts
import * as IO from 'socket.io-client';

// 只初始化一次, 就会出现这个问题
const chatSocket = IO('ws://localhost:8888/chat');


function handleClientToSent() {
  chatSocket.on('connect', () => {
    ...
  });
}
```
</details>

**解决办法:**

只需要将`初始化新的连接`相关代码, 放入点击事件中即可:

<details>
<summary>展开代码</summary>

```ts
import * as IO from 'socket.io-client';

function handleClientToSent() {
  const chatSocket = IO('ws://localhost:8888/chat');

  chatSocket.on('connect', () => {
    ...
  });
}
```
</details>

### 4. 客户端的`socket`监听事件会触发`2^n`次

**相关依赖:**

- socket.io@^2.2.0
- socket.io-client@^2.2.0
- @types/socket.io@^2.1.2
- @types/socket.io-client@^1.4.32

**产生原因:**

每一次`socket.on`, 都会添加一个新的`callback`到事件列表, 进而导致执行多次.

**解决办法:**

在`socket.on`监听之前, 使用`socket.removeAllListeners()`移除掉所有的监听事件.

<details>
<summary>展开代码</summary>

```ts
  React.useEffect(() => {
    // ? 先移除所有的监听器, 避免出现指数增长的情况
    chatSocket.removeAllListeners();

    // * 接收聊天信息
    // ? 不能在componentDidMount时监听, 只会监听一个聊天会话
    chatSocket.on('receiveChatSingleMessage', (message: any) => {
      setState({
        ...state,
        singleChatInfo: {
          ...state.singleChatInfo,
          message: state.singleChatInfo.message.concat(message),
        },
      });
    });
  });
```
</details>

### 5. 在`componentDidMount`中设置的`socket.on`监听, 只能作用于一个会话, 因为`componentDidMount`只执行了一次, 变化的只是传递的路由参数

**相关依赖:**

- socket.io@^2.2.0
- socket.io-client@^2.2.0
- @types/socket.io@^2.1.2
- @types/socket.io-client@^1.4.32

**产生原因:**

视图组件(`单聊`或`群聊`)只挂载了一次, 并且在`componentWillUnmount`之时不会卸载, 所以`componentDidMount`只执行一次, 进而监听的事件总是第一个`聊天视图`.

**解决办法:**

在`React.useEffect`中监听, 即在组件每次更新之后监听即可:

<details>
<summary>展开代码</summary>

```ts
  React.useEffect(() => {
    // ? 先移除所有的监听器, 避免出现指数增长的情况
    chatSocket.removeAllListeners();

    // * 接收聊天信息
    // ? 不能在componentDidMount时监听, 只会监听一个聊天会话
    chatSocket.on('receiveChatSingleMessage', (message: any) => {
      setState({
        ...state,
        singleChatInfo: {
          ...state.singleChatInfo,
          message: state.singleChatInfo.message.concat(message),
        },
      });
    });
  });
```
</details>

### 6. 一次单聊会话中, 接收方的所有好友都能收到本次独立会话的消息

**相关依赖:**

- socket.io@^2.2.0
- socket.io-client@^2.2.0
- @types/socket.io@^2.1.2
- @types/socket.io-client@^1.4.32

**出现原因:**

后端使用了`io.emit`, 是向所有连接了(`ws://localhost:8888/chat`)的socket发射消息, 所以导致所有的前台用户都能接收到消息

**解决方式:**

前台根据接收到的消息的`chat_id`, 进行过滤, 并追加到自己的消息列表:

<details>
<summary>展开代码</summary>

```ts
    chatSocket.on('receiveChatSingleMessage', (message: any) => {
      // 过滤当前chatId的会话消息
      const chatId = props.match.params.id;
      const newMessage = message.chat_id === chatId
        ? state.singleChatInfo.message.concat(message)
        : state.singleChatInfo.message;

      setState({
        ...state,
        singleChatInfo: {
          ...state.singleChatInfo,
          message: newMessage,
        },
      });
    });
```
</details>

### 7. `Firefox`下报`Websocket`无法连接的错误

**相关依赖:**

- socket.io@^2.2.0
- socket.io-client@^2.2.0
- @types/socket.io@^2.1.2
- @types/socket.io-client@^1.4.32

**出现原因:**

火狐处于自身的安全策略, 对`Websocket`的连接较为严格, 所以会导致其出现下面的错误:

<details>
<summary>展开代码</summary>

```plain
Firefox 无法建立到 ws://localhost:8888/socket.io/?EIO=3&transport=websocket&sid=XPfob2_McBjC9WniAAEl 服务器的连接。

载入页面时与 ws://localhost:8888/socket.io/?EIO=3&transport=websocket&sid=XPfob2_McBjC9WniAAEl 的连接中断。
```
</details>

**解决方式:**

1. 首先按照网上的步骤来, 通过火狐的`about:config`页, 修改默认的设置
2. 接着需要在代码上修改, 由于我在组件`componentWillUnmount`之时, 手动关闭了`socket`连接, 所以导致了火狐报错, 但是`Chrome`和`IE`显示正常, 很奇怪. 如果想正常使用, 将`componentWillUnmount`的代码删掉即可.

### 8. 重复创建了多个重复的`websocket`连接

**相关依赖:**

- socket.io@^2.2.0
- socket.io-client@^2.2.0
- @types/socket.io@^2.1.2
- @types/socket.io-client@^1.4.32

**问题描述:**

之前一直没注意, 直到今天看了下`Chrome`控制台有关`Ws`的内容, 吓了一跳, 每一次刷新页面, 都会重复地创建多个`websocket`连接, 赶紧看了下文档:

> 正常情况下, 只会创建一个连接, 可以通过`forceNew`配置项来强制创建新的连接.

**出现原因:**

看了下代码, 问题就出在前端代码的连接部分, 我在每个组件的`state`中都初始化了一个`Socket`, 详情可以看下: [commit@6fe6437e146a472dcf398ecc4b919df1fe692b5c](https://github.com/ddzy/ts-web-diary/commit/6fe6437e146a472dcf398ecc4b919df1fe692b5c)

导致每次组件`componentDidMount`都会初始化一个新的`链接`, 可以参考官方文档:

https://socket.io/docs/client-api/#io-url-options

文档有一句话说的很清楚:

> Note: reusing the same namespace will also create two connections

**解决方式:**

很简单, 将所有的创建`socket`相关的逻辑放置于单个文件, 使用时引入即可, 避免创建多个.

详情可查看: [https://github.com/ddzy/ts-web-diary/blob/master/src/services/websocket.tsx](https://github.com/ddzy/ts-web-diary/blob/master/src/services/websocket.tsx)

### 9. `socket.removeAllEventListener`引发的惨案

**相关依赖:**

- socket.io@^2.2.0
- socket.io-client@^2.2.0
- @types/socket.io@^2.1.2
- @types/socket.io-client@^1.4.32

**问题描述:**

今天调试`聊天室`功能的时候, 发现一个问题: 发送单聊消息的时候, 聊天历史列表不能同步更新

**出现原因:**

细心排查了一下, 发现了问题所在:

<details>
<summary>展开代码</summary>

```ts
  React.useEffect(() => {
    _setSingleChatMessageInfo();
  });

  function _setSingleChatMessageInfo() {
    // 先移除所有的监听器, 避免出现指数增长的情况
    chatIOClient.removeEventListener('receiveChatSingleMessage');

    chatIOClient.on('receiveChatSingleMessage', (message: any) => {
      const chatId = props.match.params.id;
      const newMessage = message.chat_id === chatId
        ? state.singleChatInfo.message.concat(message)
        : state.singleChatInfo.message;

      setState({
        ...state,
        singleChatInfo: {
          ...state.singleChatInfo,
          message: newMessage,
        },
        isMessageSend: true,
      });
    });
  }
```
</details>

问题就处在`chatIOClient.removeEventListener`这句代码上, 由于每次`接收`或者`发送`一个单聊消息, 都会监听一个新的`事件回调`, 所以我在每次监听之前, 先清除掉所有旧的处理器. 而由于`聊天历史`列表用的是同一个`Socket`连接, 所以就导致了其中监听的事件被一并清除.

**解决方式:**

使用`removeEventListener()`代替, 只注销掉当前的事件监听器即可.

修改后的代码:

<details>
<summary>展开代码</summary>

```ts
  function _setSingleChatMessageInfo() {
    // 先移除所有的监听器, 避免出现指数增长的情况
    chatIOClient.removeEventListener('receiveChatSingleMessage');

    // * socket处理接收聊天信息
    // ? 不能在componentDidMount时监听, 只会监听一个聊天会话
    chatIOClient.on('receiveChatSingleMessage', (message: any) => {
      // 过滤当前chatId的会话消息
      const chatId = props.match.params.id;
      const newMessage = message.chat_id === chatId
        ? state.singleChatInfo.message.concat(message)
        : state.singleChatInfo.message;

      setState({
        ...state,
        singleChatInfo: {
          ...state.singleChatInfo,
          message: newMessage,
        },
        isMessageSend: true,
      });
    });
  }
```
</details>