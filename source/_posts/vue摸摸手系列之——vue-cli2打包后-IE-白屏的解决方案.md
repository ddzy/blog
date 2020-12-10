---
title: vue摸摸手系列之——vue-cli2打包后 IE 白屏的解决方案
date: 2020-12-10 18:47:20
tags: [ie, vue, vue-cli]
categories: [frontend]
---

继上一篇文章: [vue摸摸手系列之——vue-cli3打包后 IE 白屏的解决方案](https://yyge.top/blog/2020/12/02/vue%E6%91%B8%E6%91%B8%E6%89%8B%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94vue-cli3%E6%89%93%E5%8C%85%E5%90%8E-IE-%E7%99%BD%E5%B1%8F%E7%9A%84%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88/) 之后, 在维护老项目时, 由于项目采用了 `vue-cli2`, 在 `IE` 中也是白屏状态...

<!-- more -->

## 更新

------

### [2020-12-10]

- Initial release

## 出现原因

------

同上一篇 [文章](https://yyge.top/blog/2020/12/02/vue%E6%91%B8%E6%91%B8%E6%89%8B%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94vue-cli3%E6%89%93%E5%8C%85%E5%90%8E-IE-%E7%99%BD%E5%B1%8F%E7%9A%84%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88/), **vue-cli2** 不会使用 babel 编译 `node_modules` 目录下的代码, 当 node_modules 下有 ES6 的代码时, IE 会无法识别, 进而导致报错:

- SCRIPT1001
- SCRIPT1002
- ...

## 解决方案

------

1. 安装 @babel/polyfill

```bash
npm install --save @babel/polyfill
```

2. 项目入口文件(`main.js`)引入 @babel/polyfill

```js
// main.js
import '@babel/polyfill';
```

3. 配置 webpack.base.conf.js

```js
// build/webpack.base.conf.js
module.exports = {
  ...
  entry: {
    app: ["@babel/polyfill", "./src/main.js"]
  },
  ...
}
```