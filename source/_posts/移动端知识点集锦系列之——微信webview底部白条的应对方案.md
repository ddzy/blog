---
title: 移动端知识点集锦系列之——微信webview底部白条的应对方案
date: 2020-03-24 14:49:03
tags: [wep, mobile, h5, wechat]
categories: [frontend]
---

微信 `Webview` 内部跳链或者路由变化的时候, 页面底部会产生白色的导航条, 遮挡页面; 并且如果是单页应用的话, 利用该导航条返回上一级路由页面, 会导致该页面内部的视频无法自动播放以及一系列问题, 所以得根据不同的场景来解决它.

<!-- more -->

## 更新

---

### [2020-3-24]

- Initial release

## 场景一: 页面内纯外链跳转

---

### 场景概述

页面内部的按钮, 点击之后需要跳转到外链, 如下所示代码:

```html
<template>
  <a @click="handleLinkClick">跳转百度</a>
</template>

<script>
  export default {
    methods: {
      handleLinkClick() {
        location.href = "https://www.baidu.com";
      }
    }
  };
</script>
```

上述直接使用 `location.href` 来跳转, 但是会导致微信浏览器出现白条, 因为产生了新的浏览历史.

### 解决方案

解决方式很简单, 使用 `location.replace` 代替 `location.href` 即可:

```html
<template>
  <a @click="handleLinkClick">跳转百度</a>
</template>

<script>
  export default {
    methods: {
      handleLinkClick() {
        location.replace("https://www.baidu.com");
      }
    }
  };
</script>
```

## 场景二: 单页应用路由跳转

---

### 场景概述

`SPA` 应用进行路由切换时, 也会产生浏览历史, 进而导致底部白条的出现, 如下代码所示:

```html
<template>
  <router-link to="/A">跳转A页面</router-link>
  <router-link to="/B">跳转B页面</router-link>
  <router-link to="/C">跳转C页面</router-link>

  <router-view></router-view>
</template>

<script>
  export default {};
</script>
```

上述是比较常见的用法, 直接使用 `router-link` 作跳转, 会产生上面说的问题.

### 解决方案

解决方案是使用 `$router.replace` 来代替 `router-link` 和 `$router.push`:

```html
<template>
  <a @click="handleLinkClick($event, '/A')">跳转A页面</a>
  <a @click="handleLinkClick($event, '/B')">跳转B页面</a>
  <a @click="handleLinkClick($event, '/C')">跳转C页面</a>

  <router-view></router-view>
</template>

<script>
  export default {
    methods: {
      handleLinkClick(event, type) {
        this.$router.replace(type);
      }
    }
  };
</script>
```
