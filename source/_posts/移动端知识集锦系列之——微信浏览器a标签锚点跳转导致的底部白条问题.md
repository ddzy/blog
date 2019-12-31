---
title: 移动端知识集锦系列之——微信浏览器a标签锚点跳转导致的底部白条问题
date: 2019-12-31 16:22:50
tags: [web, h5, mobile, wap]
categories: [frontend]
---

项目中有个需求, 点击头部导航按钮, 跳转到页面中对应的区块中, 当时第一反应想到的是使用 `a` 标签的锚点跳转属性, 在其它浏览器中显示正常, 但是唯独微信浏览器中, 底部会产生白色的导航条, 有时还会遮挡页面的元素.


<!-- more -->


## 更新

------

### [2019-12-31]

- Initial release

## 记录

------

### 结构概述

页面的基本结构是这样的:

```html
<div id="sNav">
  <button>功能区</button>
  <button>信息区</button>
</div>
<div id="sFeature"></div>
<div id="sInfo"></div>
```

`sNav` 固定定位于页面顶部, 点击其内部的两个链接按钮, 分别跳转到页面的大区块(`sFeature`、`sInfo`).

### 初次尝试(`a标签`)

在 vue 中, 刚开始想到的肯定是使用 a 标签自带的锚点跳转来实现, 故:

```diff
<div id="sNav">
-  <button>功能区</button>
+  <a href="#sFeature">功能区</a>
-  <button>信息区</button>
+  <a href="#sInfo">信息区</a>
</div>
<div id="sFeature"></div>
<div id="sInfo"></div>
```

但是问题出现了, 微信浏览器中, 会出现恶心的底部白条, 是因为锚点产生了新的**浏览历史**, 目前微信并没有提供相应的 JS-API 来关闭该设置, 所以只能开发者做相应的适配.

### 再次尝试(`scrollIntoView`)

查了下资料, 发现其实 `DOM` 元素还有一个 `scrollIntoView` 方法:

[https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scrollIntoView](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scrollIntoView)

他的作用是在特定的条件下, 使元素滑到页面可视区域, 可以这么用:

```html
<div id="sNav">
  <button @click="handleJumpBtnClick($event, 'Feature')">功能区</button>
  <button @click="handleJumpBtnClick($event, 'Info')">信息区</button>
</div>
<div id="sFeature"></div>
<div id="sInfo"></div>
```

```js
export default {
  methods: {
    handleJumpBtnClick(_, type) {
      const jumpType = `s${type}`;
      const $element = document.querySelector(`#${jumpType}`);

      if($element && $element.scrollIntoView) {
        $element.scrollIntoView();
      }
    }
  },
};
```

上面的方式, 可以完美的解决问题, 但是新的问题又出现了, **UC 浏览器**并不支持这个特性, 尝试失败.

### 最终措施

绕了一圈, 最终还是回到了起点, 最终还是采用 `a` 标签做跳转, 微信浏览器这个问题确实无解, 但是要记得, 使用 `a` 标签跳转之后, 需要及时将 `URL` 中的锚点历史给干掉, 不然再次刷新页面, 会默认跳到锚点指定的区域.

```html
<div id="sNav">
  <a href="#sFeature" @click="handleJumpBtnClick($event, 'Feature')">功能区</a>
  <a href="#sInfo" @click="handleJumpBtnClick($event, 'Info')">信息区</a>
</div>
<div id="sFeature"></div>
<div id="sInfo"></div>
```

```js
export default {
  methods: {
    handleJumpBtnClick(event, type) {
      // TODO: 清除历史记录
      const parsedType = `s${type}`.toLowerCase();
      const Reg_Exp_SectionId = new RegExp(`#${parsedType}`);
      const currentURL = window.location.href;

      const replacedURL = currentURL.replace(Reg_Exp_SectionId, (_, $1) => {
        if($1) {
          return '';
        }
      });

      window.history.replaceState(null, null, replacedURL);
    },
  },
};
```