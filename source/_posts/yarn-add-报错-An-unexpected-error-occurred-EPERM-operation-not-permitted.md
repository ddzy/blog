---
title: 'yarn add 报错: An unexpected error occurred: ''EPERM: operation not permitted...'''
date: 2020-12-01 19:37:55
tags: [yarn, npm, git]
categories: [frontend, freebie]
---

`yarn add` 依赖的时候报错：`"An unexpected error occurred: EPERM: operation not permitted"`

<!-- more -->

## 更新

------

### [2020-12-1]

- Initial release

### [2020-12-2]

- 构建主体

## 概览

------

在 Vue 项目中使用 `npm install xlsx` 和 `yarn add xlsx` 的时候报错:

> An unexpected error occurred: EPERM: operation not permitted

搜索了几个解决方案, 都不奏效:

1. 权限不足, 以管理员权限运行 CMD(×)
2. 项目处于运行状态, <kbd>Ctrl + C</kbd> 终止掉进程(×)
3. 清理缓存, `npm | yarn cache clean`(×)

## 解决方案

------

### 1. 安装 Python

在错误信息中看到了有 `Python` 这个字样, 猜想有可能是未安装 `Python` 导致的, 去官网下载安装即可.