---
title: leetcode -uglyNumber
date: 2019-03-09 12:23:02
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 丑数


<!-- more -->


## 思路

------

> 暴力解法

- 如果数num能被2整除, 一直除下去
- 如果数num能被3整除, 一直除下去
- 如果数num能被5整除, 一直除下去
- 返回

## 题解

------

```ts
/**
 * @param {number} num
 * @return {boolean}
 */
var isUgly = function(num) {
  if (num === 1) {
    return true;
  }
  else if (num <= 0) {
    return false;
  }

  while (num % 2 === 0) {
    num = num / 2;
  }
  while (num % 3 === 0) {
    num = num / 3;
  }
  while (num % 5 === 0) {
    num = num / 5;
  }

  return num === 1;
};
```