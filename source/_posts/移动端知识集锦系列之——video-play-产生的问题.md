---
title: 移动端知识集锦系列之——video.play()产生的问题
date: 2019-12-19 11:05:11
tags: [web, h5, mobile, wap]
categories: [frontend]
---

想在移动端通过手动调用 `video.play()` 的方式, 来控制视频的自动播放, 聪效果上来看, 确实没啥大毛病, 但是出现了这样的错误: `Uncaught (in promise) DOMException`.


<!-- more -->


## 更新

------

### [2019-12-19]

- Initial release

## 记录

------

### 产生背景

移动端有一个需求, 就是点击页面内部的按钮, 出现弹层, 其内部的视频需要自动播放, 但是使用 `autoplay` 属性会有各种兼容问题. 故而借助 `video.play()` 方法来控制播放, 本身的效果是没问题的, 但是在部分浏览器上会报错: `Uncaught (in promise) DOMException`, 意思就是: **要手动 catch 返回的 promise 结果**.

### 产生原因

- 移动端 `Chrome`、`Safari`, 会返回一个 `Promise`
- 移动端 `UC` 会返回 `undefined`

### 解决方式

对于两种不同的返回值, 需要分别处理, vue 的伪代码如下:

```html
<video
  ref="videoRef"
  :src="video_url"
></video>
```

```js
const $video = this.$refs.videoRef;
const pendingResult = $video.play();

if(pendingResult instanceof Promise) {
  pendingResult.catch(() => {});
}
```