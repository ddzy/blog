---
title: leetcode -addDigits
date: 2019-03-09 10:58:22
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 各位相加


<!-- more -->


## 思路

------

> 暴力遍历解法

## 题解

------

```ts
/**
 * @param {number} num
 * @return {number}
 */
var addDigits = function (num) {
  while (String(num).length !== 1) {
    let total = 0;

    while (num) {
      total += num % 10;
      num = ~~(num / 10);
    }

    num = total;
  }

  return num;
};
```