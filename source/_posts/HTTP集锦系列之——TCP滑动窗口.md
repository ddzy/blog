---
title: HTTP集锦系列之——TCP滑动窗口
date: 2019-03-16 16:51:05
tags: [http, tcp]
categories: [frontend, backend]
---

刷`leetcode`的时候有看到`滑动窗口`这道Hard题, 还是挺难的. 它帮助TCP实现`流量控制`功能


<!-- more -->


## 发送端窗口

------

![滑动窗口大致模型](https://oos.blog.yyge.top/2019/3/16/HTTP%E9%9B%86%E9%94%A6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94TCP%E6%BB%91%E5%8A%A8%E7%AA%97%E5%8F%A3/images/1.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

包括两大主要部分:

1. 已发送但未收到应答的数据
2. 暂未发送的数据

## 接收端窗口

------

- 发送端窗口由接收端剩余大小决定
- 接收端会将当前窗口剩余大小, 连同`ACK`一起返回
- 发送端收到`Window Size`之后, 根据该值和`网络拥塞`情况设置发送窗口的大小.

## Zero窗口

------

对端出现`0`窗口, 这时发送端停止发送数据, 启动定时器定时发送请求到接收端, 让对端告知窗口大小, 重试超过一定次数后, 可能会终端连接.