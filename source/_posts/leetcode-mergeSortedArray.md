---
title: leetcode -mergeSortedArray
date: 2019-02-15 10:08:46
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 合并两个有序数组


<!-- more -->


## 思路

------

> 投机取巧法

- 合并`nums2`到`nums1`
- 重新`sort`

## 题解

------

```ts
/**
 * @param {number[]} nums1
 * @param {number} m
 * @param {number[]} nums2
 * @param {number} n
 * @return {void} Do not return anything, modify nums1 in-place instead.
 */
var merge = function (nums1, m, nums2, n) {
  let count = 0;

  while (count < n) {
    nums1[m + count] = nums2[count];
    count++;
  }

  nums1.sort((a, b) => a - b);
};
```