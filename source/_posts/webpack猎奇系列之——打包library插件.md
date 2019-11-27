---
title: webpack猎奇系列之——打包library插件
date: 2019-05-02 09:07:21
tags: [webpack]
categories: [frontend]
---

由于`五一`放假这两天在重构之前写的`ts`插件类库(附上[地址](https://github.com/ddzy/ts-utility-plugins)), 遇到了一个需求——需要在浏览器端能用script引入, 正好用的是webpack, 所以花点时间来记录一下.


<!-- more -->


## 更新

------

### [2019-5-2]

#### Added

- Initial release

## 一、需求

------

- 构建的纯原生类库
- 需要能直接通过`script`导入

## 二、环境

------

- webpack@4.x
- ts

## 三、构建

------

### 2.1 第一步: 查文档

webpack官方文档中对于[library](https://webpack.docschina.org/guides/author-libraries/)的描述已经很清晰了.

### 2.2 第二步: webpack配置

> **PS**: 配置webpack.config.ts

```ts
...
export default {
  output: {
    filename: '[一般用插件名].js',
    path: path.resolve(__dirname, 'lib'),
    library: '[一般用插件名]',
    libraryTarget: 'var',   // 多个可选值, 具体参考[官方文档](https://webpack.docschina.org/guides/author-libraries/)
  },
};
...
```

### 2.3 第三步: index导出

> **PS**: 于入口文件中导入&导出所需的插件

```ts
// src/index.ts
import {
  ColorfulBubble,
} from './ddzy/canvas/colorful-bubble/index';

export default ColorfulBubble;
```

### 2.4 第四步: build

> PS: 执行`npm run build`打包

```bash
npm run build
```

### 2.5 第五步: 引入测试

- 通过script引入`插件.js`
- console.log调试

可以看到, 打印出了结果:

![引入插件](https://oos.blog.yyge.top/2019/5/2/webpack%E7%8C%8E%E5%A5%87%E7%B3%BB%E5%88%97%E4%B9%8B%E2%80%94%E2%80%94%E6%89%93%E5%8C%85library%E6%8F%92%E4%BB%B6/images/1.png?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)

但是, 请注意, 这里并不是我们预期的结果, 我想的是直接通过`new 插件名`来实例化插件, 但是这里好像多了一层`default`对象嵌套, 找了一下文档, 浏览器并不识别通过`export default`, 所以增加`libraryExport`字段即可:

```ts
// webpack.config.ts
export default {
  output: {
    filename: '[一般用插件名].js',
    path: path.resolve(__dirname, 'lib'),
    library: '[一般用插件名]',
    libraryTarget: 'var',   // 多个可选值, 具体参考[官方文档](https://webpack.docschina.org/guides/author-libraries/)
    libraryExport: 'default',
  },
};
```