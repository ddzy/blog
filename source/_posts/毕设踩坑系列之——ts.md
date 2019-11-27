---
title: 毕设踩坑系列之——ts
date: 2019-07-21 11:28:05
tags: [毕设]
categories: [frontend]
---

记录有关`ts`的坑


<!-- more -->


## 更新

------

### [2019-7-21]

- Initial release

## 记录

------

### 1. 配置相关

#### 1.1 `nodemon` 无法直接解析 `.ts` 文件

**解决**: 添加 `ts-node` 模块, 可自动执行编译ts文件

#### 1.2 `package.json` 配置启动命令

```ts
scripts: {
  "server": "nodemon --watch ./server/**/*.ts --ignore ./server/**/*.spec.ts --exec ts-node ./server/server.ts",
}
```

#### 1.3 执行`2`步骤后, 后台报错

**解决**: 将`tsconfig.json`的`"module"`: `"esnext"`改为 `"commentjs"`.

#### 1.4 执行`3`步骤后, `antd` 无法解析

**原因**: `antd` 依赖`esnext`

**解决**: 修改`config-overridge.js`

<details>
<summary>展开代码</summary>

```ts
const tsLoader = getLoader(
  config.module.rules,
  (rule) => (
    rule.loader
      && typeof rule.loader === 'string'
      && rule.loader.includes('ts-loader'),
  ),
);

tsLoader.options = {
  transpileOnly: true,
  getCustomTransforms: () => ({
    before: [tsImportPluginFactory({
      libraryDirectory: 'es',
      libraryName: 'antd',
      style: 'css' | true,
    })],
  }),
  compilerOptions: {
    module: 'es2015',
  },
};
```
</details>

#### 1.5 `tsconfig.json` 中已经开启 `experimentDecorator`, 仍然不能使用 decorator

**解决**:

<details>
<summary>展开代码</summary>

```ts
@(connect() as any)
class Co extends React.PureComponent{}
```
</details>

#### 1.6 配置`baseUrl`选项, 可使用绝对路径导入包, 避免路径过长的问题.

**解决:**

<details>
<summary>展开代码</summary>

```ts
{
  "compilerOptions": {
    "baseUrl": "./src",
  },
}
```
</details>