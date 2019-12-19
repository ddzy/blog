---
title: 移动端知识点集锦系列之——iOS12下双击页面放大的问题
date: 2019-12-19 15:36:15
tags: [web, wap, mobile, h5]
categories: [frontend]
---

项目交付测试的时候, 发现在 iOS12 的 Safari 浏览器中, 双击图片, 整个页面会放大, 按理说,  已经设置了 meta 属性, 可还是会出现这样的情况.


<!-- more -->


## 更新

------

### [2019-12-19]

- Initial release

## 记录

------

### 问题背景

iOS12 的 Safari 浏览器中, 双击任意图片(img 标签), 整个页面会被放大.

### 问题原因

在 `index.html` 中是有设置过 `meta` 属性的:

```html
<meta
  name="viewport"
  content="width=device-width, initial-scale=1.0, user-scalable=no, maximum-scale=1, minimum-scale=1"
/>
```

并且在我的 iOS13 系统中测试是完全没有问题的, 其实原因是 iOS12 禁用了 `meta viewport`, 如果使用双指拖动页面, 相信页面也是会放大的.

### 解决办法

禁止用户的双击行为:

```js
function handleDisableDoubleTapToScale(timestamp = 300) {
  // 上一次触摸的时间
  let lastTapTime = 0;

  document.addEventListener('touchend', (e) => {
    let currentTapTime = Date.now();

    if(currentTapTime - lastTapTime <= 300) {
      e.preventDefault();
    }

    lastTapTime = currentTapTime;
  }, false);
}
```

但是这种方式会导致页面所有的双击事件都被禁用, 所以需要针对特定的 DOM 元素来做处理, 代码可以修改为:

```js
function handleDisableDoubleTapToScale(tagName = '', timestamp = 300) {
  let lastTapTime = 0;

  document.addEventListener('touchend', (e) => {
    const $target = e.target;

    // 只对特定的标签加以处理
    if($target.tagName.toLowerCase() === tagName) {
      let currentTapTime = Date.now();

      if(currentTapTime - lastTapTime <= 300) {
        e.preventDefault();
      }

      lastTapTime = currentTapTime;
    }
  }, false);
}
```