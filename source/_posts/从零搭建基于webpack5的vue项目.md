---
title: 从零搭建基于webpack5的vue项目
date: 2020-12-24 18:44:00
tags: [webpack, webpack5, vue, vue2x, cli]
categories: [frontend, freebie]
---

基于最新发布的 webpack5.x 以及 vue2.x 从零搭建项目.

<!-- more -->

## 更新

---

### [2020-12-24]

- Initial release

## 概述

---

> 项目地址: https://github.com/ddzy/vue2-webpack5-template

webpack5.x 发布至今已经将近一个月了, v5 版本内置了一些常用的插件, 比如 `HotModuleReplacementPlugin`、`ProvidePlugin`、`DefinePlugin` 等..., 较 v4 版本有很大的变化.

## 主依赖

---

| Name        | Version | Link                                   |
| ----------- | ------- | -------------------------------------- |
| vue         | 2.6.12  | https://github.com/vuejs/vue           |
| webpack     | 5.10.3  | https://github.com/webpack/webpack     |
| webpack-cli | 4.2.0   | https://github.com/webpack/webpack-cli |

## 项目结构

---

```
|-- dist  // 打包输出目录
|-- src   // 源码目录
|   |-- @types    // ts 全局声明文件(*.d.ts)
|   |-- assets
|   |-- components
|   |-- utils
|   |-- views
|   |-- app.vue
|   |-- index.html
|   -- main.ts
|-- LICENSE
|-- README.md
|-- .browserslistrc
|-- .eslintrc.js
|-- .gitignore
|-- .prettierrc.js
|-- babel.config.json
|-- package.json
|-- tsconfig.json
|-- webpack.config.ts
|-- yarn.lock
```

## 集成本地开发环境

---

### 所需依赖

| Name               | Version | Link                                          |
| ------------------ | ------- | --------------------------------------------- |
| webpack-dev-server | 3.11.0  | https://github.com/webpack/webpack-dev-server |

### 配置流程

1. 安装依赖

```bash
yarn add --dev webpack-dev-server
```

2. 配置 `webpack.config.ts`

```ts
import * as Webpack from "webpack";

export default {
  ...
	devServer: {
		contentBase: path.resolve(__dirname, 'dist'),
		open: true,
		port: 8888,
		compress: true,
		clientLogLevel: 'silent',
		noInfo: true,
	},
  ...
} as Webpack.Configuration;;
```

3. 配置 `package.json`

```json
{
  ...
  "scripts": {
    "build": "cross-env NODE_ENV=production webpack --config ./webpack.config.ts",
    "start": "cross-env NODE_ENV=development webpack serve",
    "serve": "yarn start",
  }
  ...
}
```

## 集成模块热替换

---

### 所需依赖

略

### 配置流程

1. 配置 `webpack.config.ts`

```ts
import * as Webpack from "webpack";
```

```diff
export default {
  ...
  plugins: [
+   new Webpack.HotModuleReplacementPlugin(),
  ],
	devServer: {
+   hot: true,
	},
  ...
};

```

## 集成 HTML

---

### 所需依赖

| Name                | Version       | Link                                            |
| ------------------- | ------------- | ----------------------------------------------- |
| html-webpack-plugin | 5.0.0-alpha.3 | https://github.com/jantimon/html-webpack-plugin |

### 配置流程

1. 安装依赖

```bash
yarn add --dev html-webpack-plugin
```

2. 配置 `webpack.config.ts`

```diff
import * as HtmlWebpackPlugin from 'html-webpack-plugin';

export default {
  ...
  plugins: [
+   new HtmlWebpackPlugin({
+			template: 'src/index.html', // 自定义 HTML 模板
+		}),
  ],
  ...
} as Webpack.Configuration;
```

## 集成 SCSS

---

### 所需依赖

| Name                    | Version | Link                                                       |
| ----------------------- | ------- | ---------------------------------------------------------- |
| sass                    | 1.30.0  | https://github.com/sass/sass                               |
| sass-loader             | 10.1.0  | https://github.com/webpack-contrib/sass-loader             |
| node-sass               | 5.0.0   | https://github.com/sass/node-sass                          |
| mini-css-extract-plugin | 1.3.3   | https://github.com/webpack-contrib/mini-css-extract-plugin |
| postcss                 | 8.2.1   | https://github.com/postcss/postcss                         |
| postcss-loader          | 4.1.0   | https://github.com/webpack-contrib/postcss-loader          |
| postcss-preset-env      | 6.7.0   | https://github.com/csstools/postcss-preset-env             |

### 配置流程

1. 安装依赖

```bash
yarn add --dev sass sass-loader node-sass postcss mini-css-extract-plugin
```

2. 配置 `webpack.config.ts`

```diff
+ import * as MiniCssExtractPlugin from 'mini-css-extract-plugin';

export default {
  ...
+	module: {
+		rules: [
+			{
+				test: /\.css|sass|scss$/,
+				use: [
+					{
+						loader: MiniCssExtractPlugin.loader,
+					},
+					{
+						loader: 'css-loader',
+					},
+					{
+						loader: 'postcss-loader',
+						options: {
+							postcssOptions: {
+								plugins: [['postcss-preset-env', {}]],
+							},
+						},
+					},
+					{
+						loader: 'sass-loader',
+					},
+				],
+			},
+		],
+	},
  plugins: [
+    new MiniCssExtractPlugin(),
  ],
  ...
};
```

## 集成 TS + Babel

---

### 所需依赖

| Name                                    | Version | Link                                                                                       |
| --------------------------------------- | ------- | ------------------------------------------------------------------------------------------ |
| @babel/core                             | 7.12.10 | https://github.com/babel/babel/tree/master/packages/babel-core                             |
| @babel/plugin-proposal-class-properties | 7.12.1  | https://github.com/babel/babel/tree/master/packages/babel-plugin-proposal-class-properties |
| @babel/plugin-proposal-decorators       | 7.12.1  | https://github.com/babel/babel/tree/master/packages/babel-plugin-proposal-decorators       |
| @babel/plugin-transform-runtime         | 7.12.10 | https://github.com/babel/babel/tree/master/packages/babel-plugin-transform-runtime         |
| @babel/preset-env                       | 7.12.11 | https://github.com/babel/babel-preset-env                                                  |
| @babel/preset-typescript                | 7.12.7  | https://github.com/babel/babel/tree/master/packages/babel-preset-typescript                |
| babel-loader                            | 8.2.2   | https://github.com/babel/babel-loader                                                      |
| typescript                              | 4.1.3   | https://github.com/microsoft/TypeScript                                                    |
| tsconfig-paths-webpack-plugin           | 3.3.0   | https://github.com/dividab/tsconfig-paths-webpack-plugin/issues                            |
| @babel/polyfill                         | 7.12.1  | https://github.com/babel/babel/tree/master/packages/babel-polyfill                         |
| @babel/runtime                          | 7.12.5  | https://github.com/babel/babel/tree/master/packages/babel-runtime                          |

### 配置流程

1. 安装依赖

```bash
yarn add --dev @babel/core @babel/plugin-proposal-class-properties @babel/plugin-proposal-decorators @babel/plugin-transform-runtime @babel/preset-env @babel/preset-typescript babel-loader typescript tsconfig-paths-webpack-plugin

yarn add @babel/polyfill @babel/runtime
```

2. 配置 `webpack.config.ts`

```diff
import * as Webpack from 'webpack';

export default {
  ...
- entry: './src/main.ts',
+ entry: ['@babel/polyfill', './src/main.ts'],
  module: {
		rules: [
      ...
+			{
+				test: /\.ts|js$/,
+				exclude: /node_modules/,
+				use: [
+					{
+						loader: 'babel-loader',
+					},
+				],
+			},
      ...
		],
	},
+  resolve: {
+		extensions: ['.ts', '.js'],
+		plugins: [
+			// 将 tsconfig 中配置的路径别名映射到 webpack.resolve.alias 上
+     // 在 .vue 文件中可以通过诸如 @/components/xxx.vue 的形式来引入组件
+			new TsconfigPathsPlugin(),
+		],
+	},
  ...
} as Webpack.Configuration;
```

3. 配置 `babel.config.json`

```json
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "useBuiltIns": false
      }
    ],
    [
      "@babel/preset-typescript",
      {
        "allExtensions": true
      }
    ]
  ],
  "plugins": [
    [
      "@babel/plugin-proposal-decorators",
      {
        "legacy": true
      }
    ],
    ["@babel/plugin-proposal-class-properties"],
    ["@babel/plugin-transform-runtime"]
  ]
}
```

4. 配置 `tsconfig.json`

```json
{
  "compilerOptions": {
    "sourceMap": true,
    "module": "CommonJS",
    "target": "ES5",
    "baseUrl": ".",
    "rootDir": ".",
    "allowJs": true,
    "experimentalDecorators": true,
    // 路径别名
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

## 集成 Vue

---

### 所需依赖

| Name                   | Version | Link                                                                 |
| ---------------------- | ------- | -------------------------------------------------------------------- |
| vue                    | 2.6.12  | https://github.com/vuejs/vue                                         |
| vue-class-component    | 7.2.6   | https://github.com/vuejs/vue-class-component                         |
| vue-property-decorator | 9.1.2   | https://github.com/kaorun343/vue-property-decorator                  |
| vue-loader             | 15.9.6  | https://github.com/vuejs/vue-loader                                  |
| vue-template-compiler  | 2.6.12  | https://github.com/vuejs/vue/tree/dev/packages/vue-template-compiler |

### 配置流程

1. 安装依赖

```bash
yarn add vue vue-class-component vue-property-decorator

yarn add --dev vue-loader vue-template-compiler
```

2. 配置 `webpack.config.ts`

```diff
import * as Webpack from 'webpack';
+ const VueLoaderPlugin = require('vue-loader/lib/plugin-webpack5');

export default {
  ...
	module: {
		rules: [
      ...
+			{
+				test: /\.vue$/,
+				use: [
+					{
+						loader: 'vue-loader',
+					},
+				],
+			},
      ...
		],
	},
  plugins: [
    ...
+   new VueLoaderPlugin(),
+   // 全局注入 Vue, 避免在每个 .vue 文件中重复引入
+		new Webpack.ProvidePlugin({
+			Vue: ['vue/dist/vue.esm.js', 'default'],
+		}),
    ...
  ],
  resolve: {
-   extensions: ['.ts', '.js'],
+   extensions: ['.ts', '.js', '.vue'],
  },
  ...
} as Webpack.Configuration;
```

3. 配置全局的 `TS` 声明文件

在 `src/@types/` 目录下存放全局的 TS 声明文件(`*.d.ts`):

```
|-- src
|   |-- @types
|   |   |-- files.d.ts
|   |   |-- global.d.ts
|   |   |-- images.d.ts
|   |   |-- vue.d.ts
```

```ts
// ----------files.d.ts-----------
// 声明一些原始格式的文件
declare module "*.txt";
declare module "*.xlsx";

// ---------images.d.ts-----------
declare module "*.png";
declare module "*.jpg";
declare module "*.jpeg";
declare module "*.gif";
declare module "*.svg";

// ---------global.d.ts-----------
// 配合 Webpack.ProvidePlugin 使用, 前面已配置好了
import Vue from "vue";

declare global {
  const Vue: typeof Vue;
}

// --------vue.d.ts---------------
declare module "*.vue" {
  import Vue from "vue";
  export default Vue;
}
declare module "vue/types/vue" {
  interface Vue {}
}
```

## 集成图片

---

### 所需依赖

webpack5 内置了 `asset` 模块, 用来代替 `file-loader` & `url-loader` & `raw-loader` 处理静态资源

### 配置流程

1. 配置 `webpack.config.ts`

```diff
import * as Webpack from 'webpack';

export default {
  ...
	module: {
		rules: [
      ...
+			{
+				test: /\.png|jpg|gif|jpeg|svg/,
+				type: 'asset',
+				parser: {
+					dataUrlCondition: {
+						maxSize: 10 * 1024,
+					},
+				},
+				generator: {
+					filename: 'images/[base]',
+				},
+			},
      ...
		],
	},
  ...
} as Webpack.Configuration;
```

## 集成其它文件

---

### 所需依赖

webpack5 内置了 `asset` 模块, 用来代替 `file-loader` & `url-loader` & `raw-loader` 处理静态资源

### 配置流程

1. 配置 `webpack.config.ts`

```diff
import * as Webpack from 'webpack';

export default {
  ...
	module: {
		rules: [
      ...
+			{
+				test: /\.txt|xlsx/,
+				type: 'asset',
+				generator: {
+					filename: 'files/[base]',
+				},
+			},
      ...
		],
	},
  ...
} as Webpack.Configuration;
```

## 集成 ESlint

---

## 集成 prettier

---

## 集成 Husky

## 集成 VS Code
