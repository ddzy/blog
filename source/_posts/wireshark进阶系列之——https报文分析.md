---
title: wireshark进阶系列之——https报文分析
date: 2019-06-18 14:55:20
tags: [wireshark]
categories: [frontend, backend, freebie, tools]
---

wireshark抓包分析系列第二篇, [上一篇](https://blog.yyge.top/blog/2019/06/16/wireshark%E8%BF%9B%E9%98%B6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94http%E6%8A%A5%E6%96%87%E5%88%86%E6%9E%90/)记录了`http`报文结构, 本篇blog主要对`https`的报文结构加以分析理解.


<!-- more -->


## 更新

### [2019-6-18]

- Initial release

## 前言

`wireshark`对于前端的重要性不言而喻, 所以还是静下心来, 好好研究一波.

该系列文章都汇集于此处: [wireshark进阶系列](https://blog.yyge.top/blog/2019/04/25/wireshark%E8%BF%9B%E9%98%B6/).

## 源站

`https`的目标站点, 我选择了自己常逛的网站: [掘金](https://juejin.im/timeline).

## 抓包

### a. 前置准备

总结了一些常用的`wireshark`命令, 便于随时查阅:

[wireshark进阶系列之——常用命令指南](https://blog.yyge.top/blog/2019/04/25/wireshark%E8%BF%9B%E9%98%B6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4%E6%8C%87%E5%8D%97/)

### b. 站点过滤

**1. 过滤指定站点IP&追踪SSL流**

![1_filter_special_website_and_track_ssl_stream](https://oos.blog.yyge.top/2019/6/18/wireshark%E8%BF%9B%E9%98%B6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94https%E6%8A%A5%E6%96%87%E5%88%86%E6%9E%90/images/1_filter_special_website_and_track_ssl_stream.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

**2. 后续的所有都依据`步骤一`来实行**

## 分析

**1. 流程概览**

![2_ana_overview](https://oos.blog.yyge.top/2019/6/18/wireshark%E8%BF%9B%E9%98%B6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94https%E6%8A%A5%E6%96%87%E5%88%86%E6%9E%90/images/2_ana_overview.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

**2. TCP三次握手**

![3_ana_tcp_handshake](https://oos.blog.yyge.top/2019/6/18/wireshark%E8%BF%9B%E9%98%B6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94https%E6%8A%A5%E6%96%87%E5%88%86%E6%9E%90/images/3_ana_tcp_handshake.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

**3. SSL四次握手**

![4_ana_ssl_handshake](https://oos.blog.yyge.top/2019/6/18/wireshark%E8%BF%9B%E9%98%B6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94https%E6%8A%A5%E6%96%87%E5%88%86%E6%9E%90/images/4_ana_ssl_handshake.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

**4. 双方开始发送加密数据**

![5_ana_start_transport_encryped_data](https://oos.blog.yyge.top/2019/6/18/wireshark%E8%BF%9B%E9%98%B6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94https%E6%8A%A5%E6%96%87%E5%88%86%E6%9E%90/images/5_ana_start_transport_encryped_message.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

**5. TCP四次分手**

![6_ana_tcp_handleave](https://oos.blog.yyge.top/2019/6/18/wireshark%E8%BF%9B%E9%98%B6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94https%E6%8A%A5%E6%96%87%E5%88%86%E6%9E%90/images/6_ana_tcp_handleave.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

**6. Client Hello结构**

![7_ana_client_hello](https://oos.blog.yyge.top/2019/6/18/wireshark%E8%BF%9B%E9%98%B6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94https%E6%8A%A5%E6%96%87%E5%88%86%E6%9E%90/images/7_ana_client_hello.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

**7. Server Hello结构**

![8_ana_server_hello](https://oos.blog.yyge.top/2019/6/18/wireshark%E8%BF%9B%E9%98%B6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94https%E6%8A%A5%E6%96%87%E5%88%86%E6%9E%90/images/8_ana_server_hello.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)