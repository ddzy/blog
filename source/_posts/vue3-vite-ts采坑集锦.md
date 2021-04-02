---
title: vue3-vite-ts采坑集锦
date: 2021-03-31 14:49:54
tags: [vue, vite, vue3, typescript, ts]
categories: [frontend]
---

记录使用 Vue3 + Vite + Typescript 所踩的坑。

<!-- more -->

## 更新

------

### [2021-3-31]

- Initial release

### [2021-4-1]

#### Added

- 新增 [异步的setup](#异步的setup)

### [2021-4-2]

#### Added

- 新增 [权限路由的重置](#权限路由的重置)
- 新增 [404路由配置](#404路由配置)

## 初始化项目

------

```bash
yarn create @vitejs/app vue3-vite-example-todo --template vue-ts
```

## [vite] Internal server error: Failed to resolve import "@/api/api" from "src/main.ts". Does the file exist?

------

### 问题概述

已经在 `vite.config.ts` 中配置了路径别名：

```typescript
export default defineConfig({
	plugins: [vue()],
	resolve: {
		alias: {
			'@/': path.resolve(__dirname, './src'),
		},
	},
});
```

并且于 `tsconfig.json` 中配置了路径映射：

```json
{
	"compilerOptions": {
		"paths": {
			"@/*": ["src/*"]
		}
	},
}

```

还是出现了无法识别路径别名的错误。

### 解决方案

查阅 [issue#2316](https://github.com/vitejs/vite/issues/2316#issuecomment-789410380) 发现，vite 其实是使用 rollup 作为构建工具的，rollup 配置路径别名的语法与 webpack 有所不同，采用的是如下方式：

```diff
export default defineConfig({
	plugins: [vue()],
	resolve: {
-		alias: {
-			'@/': path.resolve(__dirname, './src'),
-		},
+   alias: [{ find: '@', replacement: path.resolve(__dirname, './src') }],
	},
});
```

## vue3挂载全局属性

------

### 问题概述

在 `vue2.x` 中，我们一般直接在 Vue.prototype 上挂载一个方法或属性：

```ts
import Vue from 'vue';
import * as api from '@/api.js'

Vue.prototype.$api = api
```

之后，为了更好的 ts 代码提示，我们需要声明类型：

```ts
import * as api from '@/api/api';

declare module "vue/types/vue" {
  interface Vue {
    $api: typeof api
  }
}
```

### 解决方案

在 `vue3.x` 中，可以通过如下方式挂载全局属性

```ts
import { createApp } from 'vue';
import App from './App.vue';
import * as api from '@/api/api';

const app = createApp(App);

// 以下两种方式均可
// app.use((vue) => {
// 	vue.config.globalProperties['$api'] = api;
// });
app.config.globalProperties['$api'] = api;

app.mount('#app');
```

接着，如果我们需要编写类型声明文件，`vue3.x` 和 `vue2.x` 所采取的方式不太一样。

`src/@types/` 目录下新建 `properties-vue.d.ts`，接着引入并声明自定义的各种方法和属性：

```ts
import api from '@/api/api';

declare module '@vue/runtime-core' {
	interface ComponentCustomProperties {
		$api: typeof api
	}
}
```

**记住，编写声明文件之后，需要重新启动编辑器！**

最后，我们可以在 Composition API 中这么使用：

```ts
import {
	defineComponent,
	getCurrentInstance,
	ComponentInternalInstance,
} from 'vue';

export default defineComponent({
	async setup() {
		const app = (getCurrentInstance() as ComponentInternalInstance).proxy;

		if (app) {
			const res = await app.$api.fetchUserInfo('xxx');
		}
	},
});
```

如果你依旧想用 Options API 的话，那么你可以这么做，如下图所示：

![1.png](https://oos.blog.yyge.top/2021%2F3%2F31%2Fvue3-vite-ts%E9%87%87%E5%9D%91%E9%9B%86%E9%94%A6%2Fimages%2F1.png)






## 异步的setup

------

### 问题概述

在编写组件的时候，遇到了一个问题，如果在组件中定义了异步的 `setup`，那么该组件就**无法被渲染**，例如 `src/components/Todo/index.vue`

```ts
import { defineComponent } from 'vue';

export default defineComponent({
  name: "Todo",
	// 异步的 setup
  async setup() {
    const app = (getCurrentInstance() as ComponentInternalInstance).proxy;
		const res = await app?.$api.getTodoList();

		return {
			todoList: reactive(res)
		}
  }
});
```

### 解决方案

由于 async 返回的 Promise 使得 setup 函数被挂起，也就是该组件会被异步渲染，解决方式很简单，如果定义了异步的 setup，那么需要在其父组件中包裹一层 `<Suspense>` 组件：

```html
<Suspense>
  <Todo />
</Suspense>
```

## 权限路由的重置

------

### 问题概述

在 vue2 + vue-router3 中，我们想实现权限菜单，就需要后端来动态的返回一个菜单列表，大体格式如下：

```ts
// GET /menus HTTP/1.1
const dynamicRoutes = [
	{
		action: 'PageA',
		permissions: ['按钮1', '按钮2'， ...]
	},
	{
		action: 'PageB',
		permissions: []
	}
]
```

然后我们根据后端返回的权限菜单来与本地的静态路由表作对比，筛选出有权限展现的页面，静态路由表如下格式：

```ts
const staticRoutes = [
	{
		path: '/pageA',
		name: 'PageA'
		meta: {
			hidden: false
		},
		component: PageA,
	},
	{
		path: '/pageB',
		name: 'PageB',
		meta: {
			hidden: false
		},
		component: PageA,
	},
]
```

最终需要重置 `Router`：

```ts
// 比对后端返回的动态路由表中与本地路由表对应的项，设置 hidden 属性，hidden 用来标识当前页面是否显示
const finalRoutes = staticRoutes.map(outerV => {
	const foundMenu = dynamicRoutes.find(innerV => {
		return innerV.action === outerV.name;
	});

	return {
		...outerV,
		meta: {
				..outerV.meta,
			hidden: !foundMenu,
		},
	};
});

// 替换现存的 Router
router.matcher = createRouter(finalRoutes).matcher;
```

### 解决方案

vue-router4 抛弃了 `router.matcher` 属性，所以我们只能通过 `router.addRoute()` 来添加路由，引用 vue-route4 官方文档里的一句话：

![2.png](https://oos.blog.yyge.top/2021%2F3%2F31%2Fvue3-vite-ts%E9%87%87%E5%9D%91%E9%9B%86%E9%94%A6%2Fimages%2F2.png)

所以，最终的代码是这样的：

```diff
+ import { useRouter } from 'vue-router';

// 替换现存的 Router
- router.matcher = createRouter(finalRoutes).matcher;
+ finalRoutes.forEach(v => {
+	 // addRoute() 第一个参数代表要插入的路由的父级路由
+	 router && router.addRoute('Home', v);
+ });
```

## 404路由配置

------

### 问题概述

在 vue2 中，我们通常使用通配符 `*` 来匹配所有不存在的路由页，从而跳转到自定义的 404 页，我们的本地静态路由表可能是这样的：

```ts
import { createRouter, createWebHashHistory } from 'vue-router';

const router = createRouter({
	history: createWebHashHistory(),
	routes: [
		{
			path: '*',
			name: 'NotFound',
			component: () => import('@/views/404/index.vue'),
		},
	],
});
```

### 解决方案

但是在 vue-router4 中，取消了通配符式匹配，须按如下方式来匹配 404 路由：

```diff
const router = createRouter({
	history: createWebHashHistory(),
	routes: [
-		{
-			path: '*',
-			name: 'NotFound',
-			component: () => import('@/views/404/index.vue'),
-		},
+		{
+			path: '/404',
+			name: 'NotFound',
+			component: () => import('@/views/404/index.vue'),
+		},
+		{
+			path: '/:pathMatch(.*)*',
+			redirect: '/404',
+		},
	],
});
```