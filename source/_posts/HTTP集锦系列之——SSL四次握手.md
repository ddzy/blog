---
title: HTTP集锦系列之——SSL四次握手
date: 2019-03-18 09:32:03
tags: [http]
categories: [frontent, backend]
---

位于`安全套阶层`, 作为`HTTPS`的安全保障, 在TCP三次握手之前进行, 同样十分重要.


<!-- more -->


## 更新

### [2019-3-18]

#### Added

- Initial release

### [2019-4-26]

#### Changed

- 更改文章`tags`
- 文章格式调整

## 一、过程

------

![SSL四次握手图解](https://oos.blog.yyge.top/2019/3/18/HTTP%E9%9B%86%E9%94%A6%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94SSL%E5%9B%9B%E6%AC%A1%E6%8F%A1%E6%89%8B/images/1.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

1. Client Hello(随机数(client-random)、客户端支持的加密方法)
2. Server Hello(挑选一种客户端支持的加密方法、随机数(server-random)、服务器证书、rsa公钥)
3. 客户端生成随机数(secret), 使用公钥加密该随机数
4. 服务器通过私钥解密, 利用`client-random`、`server-random`、`secret`生成**session key**