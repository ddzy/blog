---
title: 前端基础重拾系列之——一句话看透cookie和session
date: 2019-03-07 16:03:28
tags: [interview]
categories: [frontend]
---

老生常谈的问题, 其实只是一句话的区别


<!-- more -->


## 更新

### [2019-3-10]

#### Changed

- 修改`关系`相关内容

#### Fixed

- 修复图片只显示链接

### [2019-3-12]

#### Added

- 新增`Cookie`相关特性

### [2019-5-23]

#### Changed

- 更改`Cookie`与`Session`的关系描述

## 记录

------

### session

> 会话控制

- `session`存储于服务器端, 是一种抽象模型
- `session`存储了一种特定的属性和配置信息. 由比如`购物车的用户判别`、`网站的登录验证`等等.
- 一般与cookie配合使用.

### cookie

- `cookie`存在于浏览器端, 保存用户信息
- 每一次请求会携带cookie, session根据cookie识别当前用户信息

### 关系

`session`相当于一个`抽象数据库`, 一个TCP连接相当于`管道`, 而`cookie`作为管道中的信息载体, 通过每一次请求, 将特定信息传递给后台session, session根据对应cookie的信息, 作相应的逻辑处理.

Web应用的会话保持, 需要客户端和服务端协作完成. 服务端`Session`存储会话数据, 客户端`Cookie`存储`Session`的标识ID; 客户端每次的HTTP请求都会携带, 服务端也就能借助`Cookie`中存储的`Session`标识ID找到正确的会话数据.

随便画一张图来梳理一下:

![cookie、session关系脑图](https://oos.blog.yyge.top/2019/3/7/%E5%89%8D%E7%AB%AF%E5%9F%BA%E7%A1%80%E9%87%8D%E6%8B%BE%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94%E4%B8%80%E5%8F%A5%E8%AF%9D%E7%9C%8B%E9%80%8Fcookie%E5%92%8Csession/images/1.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

## 参考

------

- [什么是Cookie? Cookie有什么作用?](http://www.cnblogs.com/Dlonghow/archive/2008/07/10/1240069.html)
- [COOKIE和SESSION有什么区别?](https://www.zhihu.com/question/19786827)