---
title: leetcode -isPalindrome
date: 2019-02-09 16:32:40
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 判断回文数


<!-- more -->


## 思路

------

> 双指针解法

- 双指针遍历
- 存在`left !== right`, 直接`break`
- 注意`单个整数`情况

## 题解

------

```ts
/**
 * @param {number} x
 * @return {boolean}
 */
var isPalindrome = function(x) {
  x = String(x)

  let [flag, left, right] = [x.length === 1, 0, x.length - 1];

  while (left <= right) {
    if (x[left] !== x[right]) {
      flag = false;
      break;
    }

    flag = true;

    left++;
    right--;
  }

  return flag;
};
```