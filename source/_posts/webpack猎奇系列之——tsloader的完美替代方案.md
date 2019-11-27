---
title: webpack猎奇系列之——tsloader的完美替代方案
date: 2019-05-09 15:44:32
tags: [webpack]
categories: [frontend]
---

在项目中, 遇到了集成jest单测的需求, 单纯地使用`ts-jest` + `ts-loader`会出现难以估计的错误, 所以决定使用`babel`, 完美摒弃了传统的`ts-loader`方案, 对于主要流程记录下.


<!-- more -->


## 更新

------

### [2019-5-9]

#### Added

- Initial release

## 一、基本配置

------

> **PS**: 先通过简单的配置, 让webpack正常解析`ts`, 并且于测试环境中正常显示.

### 1.1 第一步: 卸载依赖

题目已经说的很清楚了, `babel7`的出现, 使得`webpack`对于`ts`文件的处理, 不再依赖于`ts-loader`等插件, 所以, 第一步就是和`ts-loader`港拜拜.

```bash
npm uninstall --save-dev ts-loader
```

### 1.2 第二步: 安装依赖

> **PS**: 对于依赖项的取舍, 暂时还未摸清, **凡事先用明白, 再去探究其原理or优化.**

暂时只用到了以下几种插件:

```bash
npm install --save-dev
  babel-loader
  @babel/preset-env
  @babel/preset-typescript
  @babel/plugin-proposal-class-properties
  @babel/plugin-proposal-object-rest-spread
```

### 1.3 第三步: babel配置

`.babelrc`的基本配置:

```json
{
  "presets": [
    "@babel/preset-env",
    "@babel/preset-typescript",
  ],
  "plugins": [
    "@babel/plugin-proposal-class-properties",
    "@babel/plugin-proposal-object-rest-spread"
  ],
}
```

### 1.4 第四步: webpack配置

`webpack.config.ts`基本配置:

```ts
module: {
  rules: [
      {
        test: /\.(ts|js)?$/,
        use: 'babel-loader',
        exclude: /node_modules/,
      },
  ],
}
```

### 1.5 第五步: 基本测试

启动测试服务器, 已可正常解析.

### 1.6 第六步: 问题挖掘

上述步骤看似完美, 但是有一个致命的问题:

> 对于ts的类型诊断error, webpack并不会抛出异常.

而我们使用ts的目的不就是为了:

- 代码提示跳转
- 类型检测

所以, 这个问题还是值得解决的, 放到下一区块记录:

## 二、附加配置

------

接着上一小节说的, babel只负责ts的解析转换, 并不会做对应的类型检查, 所以需要自行构建.

### 2.1 第一步: tsconfig.json配置

在原`tsconfig.json`中新增下列配置项:

```json
{
  "compilerOptions": {
+    "allowJs": true,
+    "target": "esnext",
+    "noEmit": true
  }
}
```

### 2.2 第二步: package.json配置

原`package.json`的`scripts`字段中新增:

```json
  "scripts": {
+    "check": "tsc -w"
  },
```

## 三、测试

------

**1. 开启ts类型检查**

```ts
npm run check
```

**2. 启动开发服务器**

```ts
npm run dev
```

## 四、Q&A

------

> **Q**: 上述步骤需要开启至少两个终端, 一个用于`类型检查`, 另一个用于`开发服务器`, 是否有更优的解决方案?

***A***: 可参考下一条问题

> **Q**: 在检测到ts类型错误时, webpack的构建并不会抛出异常?

**A**: 刚好遇到了这个问题, 参考该[issue](https://stackoverflow.com/questions/54675587/babel-typescript-doesnt-throw-errors-while-webpack-build), 找到[fork-ts-checker-webpack-plugin](https://github.com/Realytics/fork-ts-checker-webpack-plugin)插件, 引入即可.

> **Q**: 在ts文件发生变动时, [fork-ts-checker-webpack-plugin](https://github.com/Realytics/fork-ts-checker-webpack-plugin)无法即时反应类型检查的情况(只触发一次)?

***A***: 已知的[issue](https://github.com/Realytics/fork-ts-checker-webpack-plugin/issues/36)

## 五、参考

------

参考ts官方的[demo](https://github.com/microsoft/TypeScript-Babel-Starter)