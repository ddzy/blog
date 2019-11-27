---
title: HTTP集锦系列之——CDN
date: 2019-03-06 18:16:08
tags: [http, cdn]
categories: [frontend, backend]
---

`CDN`相信web开发的基本都会用到, 当需要一个特定js | css文件的时候, 为了投机取巧, 我们有时会引入一个`cdn`链接来代替


<!-- more -->


## 更新

------

### [2019-3-10]

#### Changed

- 更改`CDN`过程

### [2019-3-19]

#### Changed

- 修改CDN过程

### [2019-4-26]

#### Changed

- 调整文章格式

## 一、记录

------

### 1.1 特点

> **PS**: CDN —— 内容分发网络

### 1.2 过程

> **PS**:

- 提取url中的域名
- 本地DNS服务器解析
- 将域名解析权交给CNAME指向的CDN的DNS负载均衡系统
- DNS负载均衡返回离用户最近的IP地址给本地DNS服务器
- 本地DNS返回IP地址给用户

### 1.3 作用

- 将用户的请求重新导向到离其最近的服务节点上.
- 解决了跨地域访问的问题, 减低访问延时.
- 大部分请求在CDN边缘节点完成, CDN起到了`分流`作用, 减轻了源站负载.
- 对前端来说, 可以将`静态文件` —— .css、.js、image、音视频等放置在CDN上

## 二、参考

------

- [CDN学习笔记](https://www.cnblogs.com/tinywan/p/6067126.html)
- [CDN是什么? 使用CDN开发有什么优势?](https://www.zhihu.com/question/36514327?rf=37353035)