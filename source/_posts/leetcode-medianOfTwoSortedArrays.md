---
title: leetcode -medianOfTwoSortedArrays
date: 2019-04-06 11:47:32
tags: [leetcode]
categories: [algorithm]
---

Leetcode题解之——寻找两个有序数组的中位数


<!-- more -->


## 思路

------

#### 思路一(耗时`240ms`)

> 暴力排序法

- 合并两个数组
- 重新排序
- 根据新数组长度的奇偶性计算中位数

## 题解

------

#### 解法一

```js
/**
 * @param {number[]} nums1
 * @param {number[]} nums2
 * @return {number}
 */
var findMedianSortedArrays = function(nums1, nums2) {
  const result = [...nums1, ...nums2];
  const len = result.length;
  const middle = (len - 1) / 2;

  result.sort((a, b) => a - b);

  return len % 2 === 0
    ? ((result[~~middle] + result[Math.ceil(middle)]) / 2)
    : result[middle];
};
```