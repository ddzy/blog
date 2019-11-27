---
title: webpack猎奇系列之——使用ts编写webpack.config
date: 2019-04-27 09:35:27
tags: [webpack]
categories: [frontend]
---

很久之前搭配过webpack的ts环境, 由于没有做笔记记录, 久而久之也记不清了. 现在由于要重构`ts-utility-plugins`项目, 所以还是花点时间做个笔记吧.


<!-- more -->


## 更新

------

### [2019-4-27]

#### Added

- Initial release

## 一、配置

------

### 1.1 安装依赖

```bash
npm install --save-dev ts-node
```

### 1.2 重命名

> **PS**: 指对原`webpack.config.js`重命名为`webpack.config.ts`即可

### 1.3 package.json

> **PS**: 此处**不需要进行任何改动**

## 二、测试

------

执行`npm run build`命令打包即可