---
title: webpack猎奇系列之——错误集锦
date: 2019-01-21 11:32:58
tags: [webpack]
categories: [frontend]
---

自己配置使用webpack过程中遇到的常见错误, 小本子记录下来, 后续也可以做个参考.


<!-- more -->


## 更新

### [2019-1-21]

#### Added

- Initial release

### [2019-4-26]

#### Changed

- 更换文章标题为`webpack猎奇系列`
- 文章格式调整

### [2021-1-25]

#### Changed

- 优化文章格式

## 一、打包ts报错

---

> Module not found: Error: Can't resolve './components/hello' in 'D:\Code\webpack_study\first\src

**问题原因**: import导入`ts`, `tsx`, `js`, `jsx`等模块时, 默认不会携带后缀名, 导致webpack无法解析.

```ts
import { sayHello } from './components/Hello';
```

**解决办法**: webpack.config中增加`resolve`配置项.

```ts
module.exports = {
  ...
  resolve: {
    extensions: ['.tsx', '.ts', '.jsx', '.js',],
  },
  ...
};
```

## 二、webpack构建命令

---

1. package.json中配置

    ```ts
      scripts: {
        "build": "webpack --config webpack.config.js",
      },
    ```

2. npm内置的npx命令直接执行

    ```ts
      npx webpack --config webpack.config.js
    ```


## 三、配置node环境变量

---

> 配置`webpack`过程中需要使用`process.env.NODE_ENV`, 而默认是不存在的

**cross-env包**

```bash
npm install --save-dev cross-env
```

**package.json**

```ts
scripts: {
  "build": "cross-env NODE_ENV=production webpack --config webpack.config.js",
},
```

## 四、webpack-dev-server无法热替换

------

> [HMR] Hot Module Replacement is disabled

未在`webpack.config`中添加 `hot-module-replacement-plugin`.

```ts
{
  plugins: [
    new webpack.HotModuleReplacementPlugin(),
  ],
},
```