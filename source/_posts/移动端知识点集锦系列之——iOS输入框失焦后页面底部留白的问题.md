---
title: 移动端知识点集锦系列之——iOS输入框失焦后页面底部留白的问题
date: 2020-03-24 16:03:06
tags: [wep, mobile, h5, iOS]
categories: [frontend]
---

iOS 系统下的 `input` 输入框, 在失去焦点后, 键盘收起, 页面底部会出现空白, 导致整个页面被顶起, 是一个非常严重的问题.

<!-- more -->

## 更新

---

### [2020-3-24]

- Initial release

## 方式一

---

### 采用技术

- Vue.mixin
- window.scrollTo
- setTimeout

### 解决方案

利用网上比较常见的 `window.scrollTo`, 让输入框失去焦点后, 整个页面滚动至初始位置, 代码如下:

```html
<template>
  <input type="text" @focus="handleFocus" @blur="handleBlur" />
</template>

<script>
  export default {
    methods: {
      handleFocus() {},
      handleBlur() {
        window.scrollTo(0, 0);
      }
    }
  };
</script>
```

上述的方式只能解决单个输入框的问题, 但是很多情况下, 一个模块是有好几个输入框的, 比如手机验证码的弹窗, 可能会同时存在以下几个输入框:

- 手机号
- 验证码
- 抽奖码
- ...

这就导致一个问题——当 `手机号` 输入框失去焦点, 接着点击 `验证码` 输入框时, 页面会跳动, 影响用户体验.

### 优雅实现

最优雅的解决方式是, 利用 `setTimeout` 做防抖处理, 可以解决多输入框之间跳转的问题; 利用 `Vue.mixin` 注入统一的兼容代码, 减少代码体积:

**Main.js**

```js
Vue.mixin({
  data() {
    return {
      inputTimer: 0
    };
  },
  methods: {
    $_inputFocus() {
      window.clearTimeout(this.inputTimer);
    },
    $_inputBlur() {
      this.inputTimer = setTimeout(() => {
        window.scrollTo(0, 0);
      }, 200);
    },
    beforeDestroy() {
      window.clearTimeout(this.inputTimer);
      this.inputTimer = 0;
    }
  }
});
```

然后分别在组件中这样使用:

**Binder.vue**

```html
<template>
  <input type="text" @focus="handleFocus" @blur="handleBlur" />
</template>

<script>
  export default {
    methods: {
      handleFocus() {
        this.$_inputFocus();
        // 其它逻辑
      },
      handleBlur() {
        this.$_inputBlur();
        // 其它逻辑
      }
    }
  };
</script>
```
