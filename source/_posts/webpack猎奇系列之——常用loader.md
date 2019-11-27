---
title: webpack猎奇系列之——常用loader
date: 2019-01-21 13:39:12
tags: [webpack]
categories: [frontend]
---

对使用webpack过程中常用的loader做一个总结


<!-- more -->


![webpack](https://oos.yyge.top/blog/2019/1/22/images/webpack_1.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)


## 更新

------

### [2019-1-21]

#### Added

- Initial release

### [2019-4-26]

#### Changed

- 更改文章`title`
- 文章格式调整

## 一、css-loader

---

> 处理`.css`文件

```ts
  {
    test: /\.css$/,
    use: [
      { loader: 'style-loader' },
      {
        loader: 'css-loader',
        options: {
          modules: true,
          importLoaders: 0 | 1 | 2,  // postcss:1, postcss + less: 2,
        },
      },
    ],
  },
```


## 二、style-loader

---

> 与`css-loader`结合使用, 将生成的css融合到`style`标签, 嵌入到页面上

```ts
  ...
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          { loader: 'style-loader', },
          {
            loader: 'css-loader',
            options: {
              modules: true,   // 启动css模块化
            },
          },
        ],
      }
    ],
  },
  ...
```

## 三、postcss-loader

---

> 与`css-loader`, `style-loader`结合使用, 其`autoprefixer`组件用来添加浏览器兼容前缀

```ts
  {
    test: /\.css$/,
    use: [
      {
        loader: 'postcss-loader',
        options: {
          ...
        },
      }
    ],
  }
```

## 四、autoprefixer

---

> 自动添加浏览器厂商前缀, 与`postcss-loader`结合使用

```ts
  {
    test: /\.css$/,
    use: [
      {
        loader: 'postcss-loader',
        options: {
          plugins: [
            require('autoprefixer')({
              browsers: [xxx],
            }),
          ],
        },
      }
    ],
  }
```