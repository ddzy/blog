---
title: leetcode -majorityElement
date: 2019-02-25 12:59:58
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 求众数


<!-- more -->


## 思路

------

> 暴力计数法

- `Map`记录值
- `map.entries()`遍历符合条件的值

## 题解

------

```ts
/**
 * @param {number[]} nums
 * @return {number}
 */
var majorityElement = function(nums) {
  const [map, compare] = [new Map(), nums.length / 2];

  for (const num of nums) {
    map.set(num, map.has(num) ? map.get(num) + 1 : 1);
  }

  for (const [key, value] of map.entries()) {
    if (value > compare) {
      return key;
    }
  }
};
```