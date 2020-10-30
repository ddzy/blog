---
title: 前端基础重拾系列之——CSS圣杯&双飞翼
date: 2020-10-29 14:23:05
tags: [frontend]
categories: [frontend]
---

圣杯布局是目前页面常用的一种布局方式, 而双飞翼布局则是对圣杯做了进一步优化.


<!-- more -->


## 更新

------

### [2020-10-29]

- Initial release

### [2020-10-30]

- 撰写文章

## 圣杯布局

------

### 特点

- 页面的主要内容区域分为 `left`、`content`、`right` 三个部分
- `left` 和 `right`, 也就是左侧和右侧区域定宽, 中间内容区 `content` 宽度为 `100%`, 且自适应
- `content` 内容区域要优先渲染

### 实现

> 可以分别通过 `float`、`flex` 的形式实现圣杯和双飞翼布局

#### 实现一: float

> float 形式利用的主要原理是: 子元素的 margin(padding) 百分比是以其父级元素的 width 作参考

<iframe src="https://codesandbox.io/embed/float-shixianshengbeibuju-w2fpt?fontsize=14&hidenavigation=1&theme=light"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="float 实现圣杯布局"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>

#### 实现二: flex

<iframe src="https://codesandbox.io/embed/flex-shixianshengbeibuju-ve0h0?fontsize=14&hidenavigation=1&theme=light"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="flex 实现圣杯布局"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>

## 双飞翼布局

------

### 特点

- 双飞翼布局是对圣杯布局的一个改良

### 实现

#### 实现一: float

<iframe src="https://codesandbox.io/embed/float-shixianshuangfeiyibuju-3gkki?fontsize=14&hidenavigation=1&theme=light"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="float 实现双飞翼布局"
  allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
  sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>