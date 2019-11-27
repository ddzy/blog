---
title: webpack猎奇系列之——集成jest单测
date: 2019-05-10 09:24:50
tags: [webpack]
categories: [frontend]
---

继上一篇文章: [使用babel完美替代ts-loader](https://blog.yyge.top/blog/2019/05/09/webpack%E7%8C%8E%E5%A5%87%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94tsloader%E7%9A%84%E5%AE%8C%E7%BE%8E%E6%9B%BF%E4%BB%A3%E6%96%B9%E6%A1%88/)后, 紧接着需要做一件非常重要的事——集成单元测试, 对于一个工具类库来说, 好的单元测试是必须的, 话不多说, 做个记录.


<!-- more -->


## 更新

------

### [2019-5-10]

#### Added

- Initial release

## 一、步骤

------

### 1.1 第一步: 移除依赖

```bash
npm uninstall --save-dev ts-jest
```

### 1.2 第二步: 安装依赖

> **PS**: 得益于`ts-node`的加持, 所以此处不再需要`ts-jest`!

```bash
npm install --save-dev
  jest
  @types/jest
```

### 1.3 第三步: jest配置

> **PS**: 根目录下新建: jest.config.ts

```ts
export default {
  testRegex: "(/ddzy\/__tests__/.*|(\\.|/)(test|spec))\\.(jsx?|tsx?)$",
  moduleFileExtensions: ["ts", "tsx", "js", "jsx", "json", "node"],
};
```

### 1.4 第四步: package.json配置

```json
  "scripts": {
+   "test": "jest",
    "build": "webpack --config webpack.config.ts",
    "start": "webpack-dev-server",
    "check": "tsc -w"
  },
```

### 1.5 第五步: 简单测试

> **PS**: 根据个人喜好建立`*.test.ts`测试即可

```bash
npm run test
```