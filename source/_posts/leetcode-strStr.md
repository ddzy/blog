---
title: leetcode -strStr
date: 2019-02-11 08:28:27
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 实现strStr()


<!-- more -->


## 思路

------

> 基础

- 原生`indexOf`.

## 题解

------

```ts
/**
 * @param {string} haystack
 * @param {string} needle
 * @return {number}
 */
var strStr = function(haystack, needle) {
  return needle ? haystack.indexOf(needle) : 0;
};
```