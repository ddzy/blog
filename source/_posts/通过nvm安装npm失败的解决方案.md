---
title: 通过nvm安装npm失败的解决方案
date: 2021-05-03 08:29:07
tags: [nvm, npm, nodejs]
categories: [frontend, freebie]
---

由于在本机（Win10）上需要使用 nvm 来管理不同的 node.js 版本，但是在使用 nvm 安装 node 的过程中一直因为 `网络原因` 导致 npm 安装失败。并且尝试了科学上网开启【代理】和【全局】模式，都不起作用。

<!-- more -->

## 更新

------

### [2021-5-3]

- Initial release

## 解决方案

------

- 找到 nvm 的安装目录

  ![查找nvm安装目录](https://oos.blog.yyge.top/2021/5/3/%E9%80%9A%E8%BF%87nvm%E5%AE%89%E8%A3%85npm%E5%A4%B1%E8%B4%A5%E7%9A%84%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88/images/1.png)

- 编辑 nvm 根目录下的 `settings.txt`：

  ```diff
    root: E:\tools\work_tools\nvm\nvm
    path: E:\tools\work_tools\nodejs
  +  node_mirror: https://npm.taobao.org/mirrors/node/
  +  npm_mirror: https://npm.taobao.org/mirrors/npm/
  ```

- 重新安装 node 即可

  ```bash
  nvm install v11.0.0
  ```