---
title: webpack猎奇系列之——常用plugins
date: 2019-01-21 16:27:20
tags: [webpack]
categories: [frontend]
---

对webpack常用的plugin作一个整合👌.


<!-- more -->


![webpack](https://oos.yyge.top/blog/2019/1/22/images/webpack_1.jpg?imageView2/0/q/75|watermark/2/text/6Ziz5ZOl5bCP56uZ/font/5b6u6L2v6ZuF6buR/fontsize/440/fill/IzE4OTBGRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10|imageslim)


## 更新

------

### [2019-1-21]

#### Added

- Initial release

### [2019-4-26]

#### Changed

- 更换文章`title`为`猎奇系列`
- 文章格式调整

## 一、html-webpack-plugin

------

> 自动生成并管理 `index.html` 文件

```ts
  {
    plugins: [
      new htmlWebpackPlugin({
        title: '',
      })
    ],
  }
```

## 二、mini-css-extract-plugin

------

> 分离`.css`文件到单独trunk, 代替 `extract-text-webpack-plugin`.

```ts
const envMode = process.env.NODE_ENV !== 'production';

{
  plugins: [
    new miniCssExtractPlugin({
      filename: devMode
        ? '[name].css'
        : '[name.[hash].css]',
      chunkFilename: devMode
        ? '[id].css'
        : '[id].[hash].css',
    }),
  ],

  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: devMode
              ? 'style-loader'
              : miniCssExtractPlugin.loader,
          },
          {
            loader: 'css-loader',
            options: {
              modules: true,
              importLoaders: 2,
            },
          },
          { loader: 'postcss-loader' },
          { loader: 'less|sass-loader' },
        ],
      }
    ],
  },
}
```

## 三、optimize-css-assets-webpack-plugin

------

> 压缩 `.css` 文件, 与 `postcss` 插件功能相同, 都使用了 `cssnano`.

```ts
{
  plugins: [
    new optimizeCssAsstesWebpackPlugin(),
  ],
}
```

## 四、cssnano

------

> 压缩`css`文件


## 五、colors

------

> 给`terminal`的log日志添加颜色

```ts
console.log('colors'.green);
```

## 六、webpack-dev-server

------

> 热部署服务器

package.json

```ts
scripts: {
  "start": "webpack-dev-server"
}
```

webpack.config

```ts
{
  devServer {
    open: true,
    hot: true,
    port: 3333,
    compress: true,
    clientLogLevel: 'none',
    before(app) {
      console.log(
        `App running at http://localhost:${this.port}`.green
      );
    },
  },
}
```