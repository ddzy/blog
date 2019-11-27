---
title: leetcode -climbStairs
date: 2019-02-13 16:18:21
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 爬楼梯


<!-- more -->


## 思路

------

> 动态规划

- 最后一阶 = 倒数第一 + 1 || 倒数第二 + 2

## 题解

------

```ts
/**
 * @param {number} n
 * @return {number}
 */
var climbStairs = function(n) {
  const cache = [0, 1, 2];
  let count = 3;

  while (count <= n) {
    cache[count] = cache[count - 1] + cache[count - 2];
    count++;
  }

  return cache[n];
};
```