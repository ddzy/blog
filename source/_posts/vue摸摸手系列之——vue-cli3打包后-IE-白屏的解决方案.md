---
title: vue摸摸手系列之——vue-cli3打包后 IE 白屏的解决方案
date: 2020-12-02 19:54:18
tags: [ie, vue, vue-cli]
categories: [frontend]
---

vue-cli3 打包后的项目在本地 & 测试服预览时, Chrome、Firefox 等其它主流浏览器均显示正常, 但是 IE 整个白屏, 记录一下解决方案.

<!-- more -->

## 更新

------

### [2020-12-2]

- Initial release

### [2020-12-3]

#### Added

- 构建文章主体部分

## 出现原因

------

vue-cli3 不会使用 babel 编译 `node_modules` 目录下的代码, 当 node_modules 下有 ES6 的代码时, IE 会无法识别, 进而导致报错:

- SCRIPT1001
- SCRIPT1002
- ...

## 解决方案

------

配置 `vue.config.js`, 将 `element-ui` 加入到编译队列:

```js
// vue.config.js
module.exports = {
  ...
  transpileDependencies: ["element-ui/src"]
  ...
}
```