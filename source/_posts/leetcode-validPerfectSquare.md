---
title: leetcode -validPerfectSquare
date: 2019-03-12 21:06:59
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 有效的完全平方数


<!-- more -->


## 思路

------

### 思路一

> 二叉搜索法

## 题解

------

- 解法一

```js
/**
 * @param {number} num
 * @return {boolean}
 */
var isPerfectSquare = function (num) {
  if (num === 1) {
    return true;
  }

  let [start, end] = [0, num - 1];

  while (start <= end) {
    const middle = ~~((start + end) / 2);
    const pow = middle ** 2;

    if (pow > num) {
      end = middle - 1;
    }
    else if (pow < num) {
      start = middle + 1;
    }
    else {
      return true;
    }
  }

  return false;
};
```