---
title: 移动端知识集锦系列之——video.play()产生的问题
date: 2019-12-19 11:05:11
tags: [web, h5, mobile, wap]
categories: [frontend]
---

想在移动端通过手动调用 `video.play()` 的方式, 来控制视频的自动播放, 聪效果上来看, 确实没啥大毛病, 但是在不同浏览器上确保了不同的错误: `Uncaught (in promise) DOMException`.


<!-- more -->


