---
title: webapck猎奇系列之——构建纯ts开发环境
date: 2019-04-26 10:14:36
tags: [webpack]
categories: [frontend]
---

个人比较喜欢使用`ts`, 所以今天就依据`webpack`搭配一个比较常用的开发环境.


<!-- more -->


## 更新

------

### [2019-4-26]

#### Added

- Initial release

### [2019-4-27]

#### Added

- 添加`增强`区块

### [2019-5-10]

#### Added

- `增强`区块新增文章`想通过script导入并使用我的类库?`
- `增强`区块新增文章`babel@7完美替代ts-loader?`
- `增强`区块新增文章`集成jest单测?`

## 一、前言

------

> **PS**: 之前一直使用的`ts`自身的模块机制——`namespace` & `reference`以及`tsc`打包, 但是其对于浏览器的支持并不友好, 需要自行引入`模块加载器`, 参考[这篇文章](https://segmentfault.com/a/1190000005118886), 已经解释的非常清楚了.

## 二、构建

------

### 2.1 准备工作

> **PS**: 需要安装必要的组件

#### 2.1.1 安装webpack4

```bash
npm install --save-dev webpack webpack-cli
```

#### 2.1.2 安装ts解析相关

```bash
npm install --save-dev typescript ts-loader
```

#### 2.1.3 安装development环境组件

```bash
npm install --save-dev webpack-dev-server
```

#### 2.1.4 有用的插件

```bash
# 自动处理html文件
npm install --save-dev html-webpack-plugin

# 清楚`lib`目录下重复的文件, 避免冗余
npm install --save-dev clean-webpack-plugin
```

### 2.2 基本配置

> **PS**: 配置`webpack.config.js`

```js
cons fs = require('fs');

const cleanWebpackPlugin = require('clean-webpack-plugin');
const htmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.ts',
  output: {
    filename: 'index.js',
    path: path.resolve(__dirname, 'lib'),
  },
  resolve: {
    extensions: ['.ts', '.js'],
  },
  module: {
    rules: [
      {
        test: /\.ts?$/,
        use: 'ts-loader',
        exclude: /node_modules/,
      }
    ],
  },
  devServer: {
    contentBase: './lib',
    hot: true,
    port: 3000,
  },
  plugins: [
    new htmlWebpackPlugin({
      title: '插件库测试'
    }),
    new cleanWebpackPlugin(),
  ],
  devtool: 'inline-source-map',
};
```

## 三、增强

------

> **PS**: 增加了一些增强性的配置, 避免篇幅过长, 拆分为多篇文章.

| Desc | Link |
| --- | --- |
| 关于使用`ts`编写webpack配置文件? | [看这里](https://blog.yyge.top/blog/2019/04/27/webpack%E7%8C%8E%E5%A5%87%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94%E4%BD%BF%E7%94%A8ts%E7%BC%96%E5%86%99webpack-config/) |
| 想通过`script`导入并使用我的类库? | [看这里](https://blog.yyge.top/blog/2019/05/02/webpack%E7%8C%8E%E5%A5%87%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94%E6%89%93%E5%8C%85library%E6%8F%92%E4%BB%B6/) |
| `babel@7`完美替代`ts-loader`? | [看这里](https://blog.yyge.top/blog/2019/05/09/webpack%E7%8C%8E%E5%A5%87%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94tsloader%E7%9A%84%E5%AE%8C%E7%BE%8E%E6%9B%BF%E4%BB%A3%E6%96%B9%E6%A1%88/) |
| 集成`jest`单测? | [看这里](https://blog.yyge.top/blog/2019/05/10/webpack%E7%8C%8E%E5%A5%87%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94%E9%9B%86%E6%88%90jest%E5%8D%95%E6%B5%8B/) |