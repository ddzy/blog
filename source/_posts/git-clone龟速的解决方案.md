---
title: git clone龟速的解决方案
date: 2020-11-25 09:00:03
tags: [hexo, git, github]
categories: [frontend, freebie]
---

在国内, `git clone` 项目太慢了, 无论开不开 VPN 都一个样, 所以这里列出我认为有效的解决方案.


<!-- more -->


## 更新

------

### [2020-11-25]

- Initial release

## 工具要求

------

- win10
- clash for windows
- 稳定的 VPN 服务

## 步骤一: 查看 VPN 端口号

![VPN 端口号查询图例](https://oos.blog.yyge.top/2020/11/25/git%20clone%E9%BE%9F%E9%80%9F%E7%9A%84%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88/images/1.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

## 步骤二: 配置全局 git

```bash
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy https://127.0.0.1:7890
```

## 步骤三: Clash 开启 Rule(Pac) 模式