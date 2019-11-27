---
title: leetcode -hammingWeight
date: 2019-02-25 16:50:04
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 位1的个数


<!-- more -->


## 思路

------

> 使用`match`

## 题解

------

```ts
/**
 * @param {number} n - a positive integer
 * @return {number}
 */
var hammingWeight = function(n) {
  const nToTwo = n.toString(2);
  const reg = /1{1}/g;

  const result = nToTwo.match(reg);

  return result ? result.length : 0;
};
```