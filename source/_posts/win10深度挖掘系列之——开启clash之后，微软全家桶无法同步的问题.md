---
title: win10深度挖掘系列之——开启clash之后，微软全家桶无法同步的问题
date: 2021-03-02 10:21:00
tags: [windows, clash]
categories: [freebie, tools]
---

经常在 win10 上使用 clash，但是有个棘手的问题：开启 clash 之后，微软全家桶（`OneNote`、`OneDrive`、`Sticky Notes` 等）均无法同步。

<!-- more -->

## 更新

------

### [2021-3-2]

- Initial release

## 方法一：关闭代理

------

1. 首先关闭 clash
2. 接着打开微软全家桶软件
3. 等待同步完成之后，最后重新打开 clash

## 方法二：打开顺序

------

1. 先打开微软全家桶软件
2. 接着打开 clash
3. 仍然是可以同步的，但是后续所做的更改并不会同步

## 方法三：配置 clash

------

![1.png](https://oos.blog.yyge.top/2021/3/2/win10深度挖掘系列之——开启clash之后，微软全家桶无法同步的问题/images/1.png)

![2.png](https://oos.blog.yyge.top/2021/3/2/win10深度挖掘系列之——开启clash之后，微软全家桶无法同步的问题/images/2.png)

> 参考：https://github.com/Fndroid/clash_for_windows_pkg/issues/123