---
title: leetcode -powerOfThree
date: 2019-03-09 15:00:40
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 3的幂


<!-- more -->


## 思路

------

> 二进制法

- 和[2的幂](https://blog.yyge.top/2019/02/28/leetcode-isPowerOfTwo/)一致

## 题解

------

```js
/**
 * @param {number} n
 * @return {boolean}
 */
var isPowerOfThree = function (n) {
  if (n <= 0) {
    return false;
  }

  const [temp, reg] = [(n).toString(3), /^10*$/g];

  return reg.test(temp);
};
```