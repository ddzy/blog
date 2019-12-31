---
title: 移动端知识点集锦系列之——iOS无法自动触发input.focus方法的解决方案
date: 2019-12-31 18:47:44
tags: [web, h5, mobile, wap]
categories: [frontend]
---

移动端项目中有个需求, 需要在**弹层**中做手机验证码功能, 本来以为很简单的一件事, 但是在输入框上花了不少时间.


<!-- more -->


## 更新

------

### [2019-12-31]

- Initial release

## 记录

------

### 需求概述

背景很简单, 为了用户体验, 需要在用户点击 `获取验证码` 按钮之后, 自动让 `验证码输入框` 获取焦点.

```html
<div id="sDialogVcode">
  <!-- 验证码输入框 -->
  <input ref="vcodeInputRef" />
  <!-- 获取验证码按钮 -->
  <button ref="getVcodeBtnRef" @click="handleGetVcodeBtnClick">获取验证码</button>
</div>
```

### 尝试解决

最初想到的, 也是最容易想到的方案, 就是监听 `button` 的点击事件, 手动聚焦输入框:

```js
export default {
  methods: {
    handleGetVcodeBtnClick() {
      const $vcodeInput = this.$refs.vcodeInputRef;

      // 自动聚焦
      $vcodeInput.focus();
    },
  },
};
```

不幸的是, 这种方式, 在 `iOS` 所有的浏览器中都没有效果, 故需另辟蹊径.

### 最终方案

上面的方案不可取的原因在于, `iOS` 规定了只能由用户主动去触发 `input` 等元素, 调起键盘, 但是并没有限制元素的点击触发, 所以可以采取以下的方式解决:

```diff
<div id="sDialogVcode">
  <!-- 验证码输入框 -->
  <input ref="vcodeInputRef" />
  <!-- 获取验证码按钮 -->
  <button ref="getVcodeBtnRef" @click="handleGetVcodeBtnClick">获取验证码</button>
</div>

<!-- 虚拟按钮 -->
+ <button ref="dummyBtnRef"></button>
```

```js
export default {
  methods: {
    handleGetVcodeBtnClick() {
      const $vcodeInput = this.$refs.vcodeInputRef;
      const $dummyBtn = this.$refs.dummyBtnRef;

      // 自动聚焦
      $dummyBtn && $dummyBtn.click();
    },
  },
  mounted() {
    const $vcodeInput = this.$refs.vcodeInputRef;
    const $dummyBtn = this.$refs.dummyBtnRef;

    // 通过 click() 事件没有被限制的点, 可以用它来触发 focus()
    $dummyBtn && $dummyBtn.addEventListener('click', () => {
      $vcodeInput && $vcodeInput.focus();
    });
  },
};
```