---
title: leetcode -isPowerOfTwo
date: 2019-02-28 10:47:03
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 2的幂


<!-- more -->


## 思路

------

> 二进制解法

2的幂次方, 其二进制必定只存在一个`1`

## 题解

------

```ts
/**
 * @param {number} n
 * @return {boolean}
 */
var isPowerOfTwo = function (n) {
  if (n <= 0) {
    return false;
  }

  const temp = '' + (n).toString(2);
  const matched = temp.match(/1{1}/g);

  return matched && matched.length === 1;
};
```