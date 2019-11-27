---
title: wireshark进阶系列之——http报文分析
date: 2019-06-16 13:18:32
tags: [wireshark]
categories: [frontend, backend, freebie, tools]
---

`http`协议, 应该是每个web开发者都经历过的, 那么, 它的报文成分是什么? 这篇文章将会使用`wireshark`来做个简单分析记录.

<!-- more -->


## 更新

### [2019-6-16]

- Initial release

### [2019-6-18]

#### Changed

- 文章格式调整

## 前言

`wireshark`对于前端的重要性不言而喻, 所以还是静下心来, 好好研究一波.

该系列文章都汇集于此处: [wireshark进阶系列](https://blog.yyge.top/blog/2019/04/25/wireshark%E8%BF%9B%E9%98%B6/).

## 源站

由于个人网站抽风, 有可能是阿里云服务器的问题,

所以搜寻了一个较简单的`HTTP`协议的站点: [Xiuno BBS](http://www.hackerduke.com/?tdsourcetag=s_pctim_aiomsg).

## 抓包

### a. 前置准备

总结了一些常用的`wireshark`命令, 便于随时查阅:

[wireshark进阶系列之——常用命令指南](https://blog.yyge.top/blog/2019/04/25/wireshark%E8%BF%9B%E9%98%B6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4%E6%8C%87%E5%8D%97/)

### b. 站点过滤

1. **首先需要过滤掉无用的报文信息:**

![1_filter_special_website](https://oos.blog.yyge.top/2019/6/16/wireshark%E8%BF%9B%E9%98%B6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94http%E6%8A%A5%E6%96%87%E5%88%86%E6%9E%90/images/1_filter_special_website.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

2. **报文信息汇集:**

![2_all_message](https://oos.blog.yyge.top/2019/6/16/wireshark%E8%BF%9B%E9%98%B6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94http%E6%8A%A5%E6%96%87%E5%88%86%E6%9E%90/images/2_all_message.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

## 分析

**1. 物理层信息**

![3_ana_physical_layer](https://oos.blog.yyge.top/2019/6/16/wireshark%E8%BF%9B%E9%98%B6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94http%E6%8A%A5%E6%96%87%E5%88%86%E6%9E%90/images/3_ana_physical_layer.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

**2. 数据链路层信息**

![4_ana_data_link_layer](https://oos.blog.yyge.top/2019/6/16/wireshark%E8%BF%9B%E9%98%B6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94http%E6%8A%A5%E6%96%87%E5%88%86%E6%9E%90/images/4_ana_data_link_layer.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

**3. 网络层信息**

![5_ana_internet_layer](https://oos.blog.yyge.top/2019/6/16/wireshark%E8%BF%9B%E9%98%B6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94http%E6%8A%A5%E6%96%87%E5%88%86%E6%9E%90/images/5_ana_internet_layer.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

**4. 传输层信息**

![6_ana_transport_layer](https://oos.blog.yyge.top/2019/6/16/wireshark%E8%BF%9B%E9%98%B6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94http%E6%8A%A5%E6%96%87%E5%88%86%E6%9E%90/images/6_ana_transport_layer.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

**5. 应用层信息**

![7_ana_application_layer](https://oos.blog.yyge.top/2019/6/16/wireshark%E8%BF%9B%E9%98%B6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94http%E6%8A%A5%E6%96%87%E5%88%86%E6%9E%90/images/7_ana_application_layer.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)