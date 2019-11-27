---
title: leetcode -intersectionOfTwoArraysII
date: 2019-03-10 17:29:57
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 两个数组的交集II


<!-- more -->


## 思路

------

### 思路一(耗时`120`ms)

> 双Map取min法

- 两个Map分别记录两个数组的值
- 遍历其中之一map, 取较小值
- 遍历较小值push进result数组

## 题解

------

- 解法一

```js
/**
 * @param {number[]} nums1
 * @param {number[]} nums2
 * @return {number[]}
 */
var intersect = function(nums1, nums2) {
  const [map1, map2, result] = [new Map(), new Map(), []];

  for (const v of nums1) {
    map1.get(v)
      ? map1.set(v, map1.get(v) + 1)
      : map1.set(v, 1);
  }
  for (const v of nums2) {
    map2.get(v)
      ? map2.set(v, map2.get(v) + 1)
      : map2.set(v, 1);
  }

  for (const [key, value] of map1) {
    const count = Math.min(value, map2.get(key) || 0);

    for (let i = 0; i < count; i++) {
      result.push(key);
    }
  }

  return result;
};
```