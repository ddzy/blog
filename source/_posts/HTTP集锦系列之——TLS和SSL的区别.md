---
title: HTTP集锦系列之 —— TLS和SSL的区别
date: 2019-03-04 14:41:47
tags: [http, SSL, TLS]
categories: [frontend, backend]
---

作为`HTTPS`的两个加密协议, `TLS`与`SSL`互补且密不可分


<!-- more -->


## 更新

------

### [2019-3-9]

#### Added

- 新增`SSL`协议脑图

#### Changed

- 更改`title`

### [2019-5-8]

#### Added

- 新增`对称加密`&`非对称加密`

## 记录

------

### TLS & SSL

- 非对称加密
- 同为`HTTPS`提供安全保障
- 互为`并列`关系
- `TLS`是`SSL`的增强版
- 同使用`RSA`加密算法, 目前出现`AES`、`ECC`等算法.

![TLS/SSL协议图](https://oos.blog.yyge.top/2019/3/4/HTTP%E9%9B%86%E9%94%A6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94TLS%E5%92%8CSSL%E7%9A%84%E5%8C%BA%E5%88%AB/images/1.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

### 对称 & 非对称加密

- 非对称加密
  - 私钥加密

- 对称加密
  - 公钥加密

## 参考

------

参阅[Here](https://kb.cnblogs.com/page/197396/)