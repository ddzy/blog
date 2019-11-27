---
title: leetcode -powerOfFour
date: 2019-03-09 15:07:10
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 4的幂


<!-- more -->


## 思路

------

> 二进制解法

- 与[2的幂](https://blog.yyge.top/2019/02/28/leetcode-isPowerOfTwo/)一致

## 题解

------

```js
/**
 * @param {number} num
 * @return {boolean}
 */
var isPowerOfFour = function (num) {
  if (num <= 0) {
    return false;
  }

  const [temp, reg] = [(num).toString(4), /^10*$/g];

  return reg.test(temp);
};
```