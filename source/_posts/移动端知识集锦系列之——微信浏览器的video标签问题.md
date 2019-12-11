---
title: 移动端知识集锦系列之——微信浏览器的video标签问题
date: 2019-12-11 14:15:11
tags: [web, h5, mobile]
categories: [frontend]
---

在移动端的 vue 项目中使用 video.js, 导致整个项目显得非常笨重, 索性使用原生的 video 标签, 记录一下几个重要的属性.


<!-- more -->


## 更新

------

### [2019-12-11]

- Initial release

## 坑点

------

### iOS 默认会全屏播放

添加 `playsinline` 和 `webkit-playsinline` 属性

```html
<video
	preload="auto"
	webkit-playsinline="true"
	playsinline="true"
	x5-playsinline="true"
	x5-video-player-type="h5"
	x5-video-player-fullscreen="true"
	x5-video-orientation="portraint"
	ref="videoPlayer"
	src="https://www.w3school.com.cn/example/html5/mov_bbb.mp4"
	@ended="onVideoEnded($event)"
></video>
```