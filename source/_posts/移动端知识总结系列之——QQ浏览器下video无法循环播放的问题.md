---
title: 移动端知识总结系列之——QQ浏览器下video无法循环播放的问题
date: 2020-01-02 09:21:41
tags: [web, mobile, h5]
categories: [frontend]
---

QQ 浏览器中使用原生的 video 标签嵌入视频, 开启了 `loop` 属性之后, 发现并不能正常的循环播放, 在第一次播放结束之后会黑屏.


<!-- more -->


## 更新

------

### [2020-1-2]

- Initial release

## 记录

------

### 问题概述

移动端 `H5` 页面需要嵌入一段介绍视频, 想采用原生的 `video` 标签, 来减少源代码的体积, 但是众所周知, `video` 在移动端的行为多样化.

在 `QQ` 浏览器中开启了 `loop = "loop"` 属性之后, 发现并不能循环播放, 在第一次视频播放结束之后, 会出现整个 `video` 黑屏的情况, 所有的事件都无法触发.

### 解决方案

几经周折之后, 找到了一个解决方案, 在每次视频播放完成之后, 手动替换 `video` 的 `src` 属性, 更新其播放源:

```html
<video
  loop="loop"
  controls="controls"
  :src="currentVideoURL"
  @ended="handleVideoEnded($event)"
></video>
```

```js
export default {
  data() {
    return {
      // 上一次的播放源
      prevVideoURL: 'http://www.w3school.com.cn/example/html5/mov_bbb.mp4',
      // 当前的播放源
      currentVideoURL: 'http://www.w3school.com.cn/example/html5/mov_bbb.mp4',
    };
  },
  methods: {
    handleVideoEnded() {
      // 播放结束, 重置视频源
      this.currentVideoURL = this.prevVideoURL;
    },
  },
};
```

### 存在问题

上述的解决方式, 看似解决了需求, 但是会产生一个额外的问题, 那就是在视频播放完成之后, 会自动全屏, 且只有 QQ 浏览器会产生这个问题. 目前还没有想到解决办法...