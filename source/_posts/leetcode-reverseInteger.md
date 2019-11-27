---
title: leetcode -reverseInteger
date: 2019-02-09 15:21:44
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 整数反转


<!-- more -->


## 思路

------

> 双指针解法

- 左右双指针
- '-'号判断
- 解构赋值
- 边界判断

## 题解

------

```ts
/**
 * @param {number} x
 * @return {number}
 */
var reverse = function (x) {
  x = String(x).split('');

  const [MIN_INT, MAX_INT] = [-(2 ** 31), (2 ** 31) - 1];
  let [left, right] = [
    x[0] === '-' ? 1 : 0,
    x.length - 1,
  ];

  while (left <= right) {
    [x[left], x[right]] = [x[right], x[left]];

    left++;
    right--;
  }

  x = Number.parseInt(x.join(''));

  return x < MIN_INT
    || x > MAX_INT
    ? 0
    : x;
};
```