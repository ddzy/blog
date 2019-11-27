---
title: wireshark进阶系列之——常用命令指南
date: 2019-04-25 11:21:56
tags: [wireshark]
categories: [frontend, backend, freebie, tools]
---

`wireshark`系列的处女篇, 对于`wireshark`的常用过滤命令做一个记录


<!-- more -->


## 更新

------

### [2019-4-25]

#### Added

- Initial release

## 一、目录

------

### 1.1 单命令

#### 1.1.1 过滤HTTP协议

```bash
http
```

#### 1.1.2 过滤IP地址

```bash
ip.addr == xxx
```

#### 1.1.3 过滤传输层协议

```bash
[tcp | udp]
```

#### 1.1.4 过滤安全套接字层协议

```bash
ssl
```

#### 1.1.5 过滤端口

```bash
tcp.port == xxx
```

#### 1.1.6 并列

```bash
[&& | and]
```

#### 1.1.7 或

```bash
[|| | or]
```

### 1.2 组合命令

#### 1.2.1 过滤指定网站的HTTP请求

```bash
http && ip.addr == xxx && tcp
```