---
title: vscode深度探索系列之——cmder光标错位问题
date: 2019-03-16 09:18:37
tags: [vscode, cmder]
categories: [frontend, freebie]
---

接着[在vscode中集成cmder](https://blog.yyge.top/blog/2019/03/16/vscode%E6%B7%B1%E5%BA%A6%E6%8E%A2%E7%B4%A2%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94cmder%E9%9B%86%E6%88%90/)的后续部分, 在使用中出现了`光标错位`的问题, 这里记录一下解决方式.


<!-- more -->


## 前言

------

目前看来, 有两个已知`光标错位bug`:

1. 不折行显示(已解决)
2. 折行显示(暂未解决)

## 解决

------

### 不折行

找到`cmder/vendor/`下的`clink.lua`文件, 找到(如下图):

![编辑clink.lua](https://oos.blog.yyge.top/2019/3/16/vscode%E6%B7%B1%E5%BA%A6%E6%8E%A2%E7%B4%A2%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94cmder%E5%85%89%E6%A0%87%E9%94%99%E4%BD%8D%E9%97%AE%E9%A2%98/images/1.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

将默认符号替换为`任意其他符号`即可.

### 折行

...暂未解决