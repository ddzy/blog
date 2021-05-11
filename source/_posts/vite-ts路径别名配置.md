---
title: vite-ts路径别名配置
date: 2021-05-11 10:08:27
tags: [vue3, vue, vite, ts]
categories: [frontend]
---

在开发组件库的过程中，由于项目目录层级嵌套太深，往往引入一个文件太过繁琐，所以需要配置路径别名。

<!-- more -->

## 更新

------

### [2021-5-11]

- Initial release

## 简单说明

------

| 依赖名     | 版本  |
| ---------- | ----- |
| vite       | 2.1.5 |
| vue        | 3.0.5 |
| typescript | 4.1.3 |

往往，如果项目结构过深的话，我们导入一个文件，会出现下面这种情况：

```ts
import xxx from '../../../../../xxx';
```

## 步骤一：项目结构预览

------

```txt
.
├── docs                // 组件库文档
│   ├── components
│   ├── guide
│   └── README.md
├── packages            // 源码目录
│   ├── common          // - 通用方法、样式等
│   └── components      // - 各 ui 组件
├── public
│   └── favicon.ico
├── types
│   └── shims-vue.d.ts
├── LICENSE
├── README.md
├── index.html
├── jest.config.js
├── lerna-debug.log
├── lerna.json
├── package.json
├── prettier.config.js
├── tree.txt
├── tsconfig.json
├── vite.config.ts
└── yarn.lock
```

## 步骤二：配置vite.config.ts

------

注：`vite.config.ts` 所在目录默认为根目录

```ts
export default defineConfig({
  ...
	resolve: {
		alias: [
			{
				find: '@common',
				replacement: path.resolve(__dirname, '/packages/common'),
			},
			{
				find: '@components',
				replacement: path.resolve(__dirname, '/packages/components'),
			},
		],
	},
  ...
});
```

## 步骤三：配置tsconfig.json

------

```diff
{
  ...
	"compilerOptions": {
		"paths": {
+			"@common/*": ["./packages/common/*"],
+			"@components/*": ["./packages/components/*"],
+			"@/*": ["./*"]
		},
+		"baseUrl": "."
	}
  ...
}
```

## 步骤四：引入任意组件

------

```ts
import xxx from '@common/xxx';
import xxx from '@/packages/common/xxx';
...
```

## 坑点

------

- vite（`2.1.5`）引入文件不再需要增加 `/` 前缀，例如：

```diff
-import xxx from '@common/xxx';
+import xxx from '/@common/xxx';
```

- `vite` 配置路径别名和 `webpack` 略有不同：

```diff
resolve: {
-	alias: {
-  	'@common/': path.resolve(__dirname, '/packages/common'),
-	},

+	alias: [
+		{
+			find: '@common',
+			replacement: path.resolve(__dirname, '/packages/common'),
+		},
+	],
},
```