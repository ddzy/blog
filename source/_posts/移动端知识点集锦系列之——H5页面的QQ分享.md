---
title: 移动端知识点集锦系列之——H5页面的QQ分享
date: 2020-03-24 10:27:29
tags: [wep, mobile, h5, qq]
categories: [frontend]
---

由于游戏的活动页需要在微信和 `QQ` 内部分享, 并且要求文案可以动态配置; 微信内部的分享没啥大问题, 就是配置安全域名 -> 利用 `wx-js-sdk` 做桥接 -> 配置分享信息即可; 这里将 `QQ` 分享的相关内容记录一下.

<!-- more -->

## 更新

---

### [2020-3-24]

- Initial release

## 方式一

---

### 如何配置

直接在 `index.html` 的 `head` 中添加分享配置, 主要是根据 `meta` 元标签实现的:

```html
<html>
  <head>
    <meta name="description" itemprop="description" content="分享简介" />
    <meta itemprop="name" content="分享标题" />
    <meta itemprop="image" content="分享缩略图" />
    <title></title>
  </head>
</html>
```

### 注意事项

#### (1). QQ 分享不能动态配置信息, 也就是说我们想从后端拉取数据, 然后注入模板中, 是无效的, 示例代码如下

```js
export default {
  methods: {
    async initQQShare() {
      const shareInfo = await ajax("xxx");

      const $head = document.querySelector("head");
      $head.innerHTML += `
				<meta
					name="description"
					itemprop="description"
					content="${shareInfo.content}"
				/>
				<meta itemprop="name" content="${shareInfo.title}" />
				<meta
					itemprop="image"
					content="${shareInfo.img_url}"
				/>
			`;
    }
  },
  mounted() {
    this.initQQShare();
  }
};
```

#### (2). 单页应用只有一个 `index.html` 文件, 如果内部有多个需要分享的组件页面, 就需要动态创建模板, 示例代码如下

```js
export default {
  methods: {
    async initQQShare() {
      const $head = document.querySelector("head");
      $head.innerHTML += `
				<meta
					name="description"
					itemprop="description"
					content="分享简介"
				/>
				<meta itemprop="name" content="分享标题" />
				<meta
					itemprop="image"
					content="缩略图"
				/>
			`;
    }
  },
  mounted() {
    this.initQQShare();
  }
};
```
