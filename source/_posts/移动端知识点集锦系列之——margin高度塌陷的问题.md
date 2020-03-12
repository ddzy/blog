---
title: 移动端知识点集锦系列之——margin高度塌陷的问题
date: 2020-03-12 14:44:41
tags: [wep, mobile, h5]
categories: [frontend]
---

今日在移动端布局的时候, 使用正常的流式布局, 但是子元素设置了 `margin-top`, 会导致其父级整个移动.

<!-- more -->

## 更新

---

### [2020-3-12]

- Initial release

## 问题概述

---

```html
<div class="parent">
  <div class="child"></div>
</div>
```

```less
.parent {
  width: 200px;
  height: 200px;
  background-color: #1890ff;
  .child {
    height: 50px;
    margin-top: 50px;
    background-color: #daa520;
  }
}
```

如上所示代码, 我想给 `child` 元素设置一个顶部的距离其父级元素 `parent` 的间距, 并且 `parent` 元素**未设置高度**, 理想的结果是这样的:

<iframe width="100%" height="300" src="//jsfiddle.net/alioe_duan/vpery0qh/2/embedded/html,css,result/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>

但是实际的情况是:

<iframe width="100%" height="300" src="//jsfiddle.net/alioe_duan/vpery0qh/3/embedded/html,css,result/" allowfullscreen="allowfullscreen" allowpaymentrequest frameborder="0"></iframe>

可以看到, `child`, 也就是黄色区域连同其父元素 `parent` 整个向下移动了 `50px`, 这就是 margin 塌陷.

## 解决方案

---

给父元素 `parent` 设置一个 `overflow: auto` 即可.
