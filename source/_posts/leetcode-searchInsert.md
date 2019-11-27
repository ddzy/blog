---
title: leetcode -searchInsert
date: 2019-02-11 09:14:07
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 搜索插入位置


<!-- more -->


## 思路

------

> 暴力解法

- 高阶API
  - indexOf | includes
  - findIndex

## 题解

------

```ts
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var searchInsert = function(nums, target) {
  return nums.includes(target)
    ? nums.indexOf(target)
    : nums.findIndex((v) => v > target) !== -1
      ? nums.findIndex((v) => v > target)
      : nums.length;
};
```