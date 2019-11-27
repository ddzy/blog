---
title: leetcode -plusOne
date: 2019-02-11 15:54:42
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 加一


<!-- more -->


## 思路

------

> 解构

- 两种情况
  - 等于9
  - 小于9

## 题解

------

```ts
/**
 * @param {number[]} digits
 * @return {number[]}
 */
var plusOne = function (digits) {
  const len = digits.length;
  let count = len - 1;

  while (count >= 0) {
    if(digits[count] < 9) {
      digits[count] ++;

      return digits;
    }
    digits[count] = 0;
    count --;
  }

  return [1, ...digits];
};
```