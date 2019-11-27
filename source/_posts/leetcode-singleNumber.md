---
title: leetcode -singleNumber
date: 2019-02-22 10:10:01
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 只出现一次的数字


<!-- more -->


## 思路

------

> 位运算

- 异或运算符`^`
- 参考[这里](https://blog.csdn.net/Garrettzxd/article/details/82390653)

## 题解

------

```ts
/**
 * @param {number[]} nums
 * @return {number}
 */
var singleNumber = function(nums) {
  let result = 0;

  for (const val of nums) {
    result ^= val;
  }

  return result;
};
```