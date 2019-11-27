---
title: leetcode -reverseBits
date: 2019-02-25 16:30:37
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 颠倒二进制位


<!-- more -->


## 思路

------

- 使用数组的`reverse`反转

## 题解

------

```ts
/**
 * @param {number} n - a positive integer
 * @return {number} - a positive integer
 */
var reverseBits = function(n) {
  const nToTwo = ((n).toString(2)).padStart(32, '0');
  const reversedNToTwo = nToTwo.split('').reverse().join('');

  return Number.parseInt(reversedNToTwo, 2);
};
```