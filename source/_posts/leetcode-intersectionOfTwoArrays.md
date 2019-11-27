---
title: leetcode -intersectionOfTwoArrays
date: 2019-03-10 16:41:08
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 两个数组的交集


<!-- more -->


## 思路

------

### 思路一(耗时`126`ms)

> Map计数法

- 遍历较长的数组, 存储至Map
- 遍历较小的数组, 对应Map的值加一
- 遍历Map寻找为1的值

## 题解

------

- 解法一

```js
/**
 * @param {number[]} nums1
 * @param {number[]} nums2
 * @return {number[]}
 */
var intersection = function(nums1, nums2) {
  // TODO solution 1
  if (nums1.length > nums2.length) {
    return intersection(nums2, nums1);
  }
  const result = [];
  const map = new Map();
  for (const v of nums2) {
    map.set(v, 0);
  }

  for (const v of nums1) {
    map.has(v) && (map.set(v, 1));
  }

  for (const [key, value] of map.entries()) {
    value && (result.push(key));
  }

  return result;
};
```