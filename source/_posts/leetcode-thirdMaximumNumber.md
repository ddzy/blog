---
title: leetcode -thirdMaximumNumber
date: 2019-03-14 14:01:54
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 第三大的数


<!-- more -->


## 思路

------

### 思路一(耗时`132`ms)

> 排序法

- Set去重
- sort排序
- 根据`length`取值

## 题解

------

- 解法一

```js
/**
 * @param {number[]} nums
 * @return {number}
 */
var thirdMax = function(nums) {
  nums = [...new Set(nums)].sort((a, b) => b - a);

  return nums.length >= 3
    ? nums[2]
    : nums[0];
};
```