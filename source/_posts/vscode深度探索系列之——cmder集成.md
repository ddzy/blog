---
title: vscode深度探索系列之——cmder集成
date: 2019-03-16 08:54:14
tags: [vscode, cmder]
categories: [frontend, freebie]
---

由于昨天的`win10`更新, 导致系统的`.Net framework`无法正常使用, 一气之下给删了, 结果`powershell`又不能用了, 而`vscode`里面的默认终端是`powershell`, 所以就用`cmder`来替代默认终端.


<!-- more -->


## 前言(废话)

------

使用`cmder`的原因很简单——流畅不卡. 在这之前尝试了`git-bash`、`tmux`这两个终端工具, 但是有个问题: 前者太卡, 后者需要`c++`环境. 所以就折中选择了`cmder`.

## 配置

------

### 版本

![vscode版本](https://oos.blog.yyge.top/2019/3/16/vscode%E6%B7%B1%E5%BA%A6%E6%8E%A2%E7%B4%A2%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94cmder%E9%9B%86%E6%88%90/images/1.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

### 第一步: 配置cmd

在设置中搜索`shell`, 配置`cmd`的路径, `win10`应该是一样的:

![配置cmd](https://oos.blog.yyge.top/2019/3/16/vscode%E6%B7%B1%E5%BA%A6%E6%8E%A2%E7%B4%A2%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94cmder%E9%9B%86%E6%88%90/images/2.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

**(PS: 如果直接在此处填入`cmder`的安装路径, 在vscode中会打开新的`cmder`窗口, 而不是内置终端)**

### 第二步: 拉取cmder

往下拉, 找到(如下图):

![拉取cmder](https://oos.blog.yyge.top/2019/3/16/vscode%E6%B7%B1%E5%BA%A6%E6%8E%A2%E7%B4%A2%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94cmder%E9%9B%86%E6%88%90/images/3.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

进入`settings.json`, 输入(如下图):

![输入config](https://oos.blog.yyge.top/2019/3/16/vscode%E6%B7%B1%E5%BA%A6%E6%8E%A2%E7%B4%A2%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94cmder%E9%9B%86%E6%88%90/images/4.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

### 第三步: 重启vscode

## 后续

------

在使用过程中, 出现了另外一个问题——`光标错位`, [下一篇](https://blog.yyge.top/blog/2019/03/16/vscode%E6%B7%B1%E5%BA%A6%E6%8E%A2%E7%B4%A2%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94cmder%E5%85%89%E6%A0%87%E9%94%99%E4%BD%8D%E9%97%AE%E9%A2%98/)blog详细记录.