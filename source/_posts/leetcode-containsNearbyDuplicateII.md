---
title: leetcode -containsNearbyDuplicateII
date: 2019-02-28 08:43:43
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 存在重复元素ii


<!-- more -->


## 思路

------

> 暴力破解法

双层循环暴力遍历, 符合条件`return true`. 耗时但是易懂

> 缓存法

`Map`缓存, 依据情况更新键值

## 题解

------

暴力破解法:

```ts
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {boolean}
 */
var containsNearbyDuplicate = function(nums, k) {
  for (let i = 0; i < nums.length; i++) {
    for (let j = i + 1; j < nums.length; j++) {
      if (nums[j] === nums[i] && Math.abs(j - i) <= k) {
        return true;
      }
    }
  }

  return false;
};
```

缓存法:

```ts
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {boolean}
 */
var containsNearbyDuplicate = function (nums, k) {
  const cache = new Map();

  for (const [key, value] of nums.entries()) {
    if (cache.has(value)) {
      const temp = cache.get(value);
      if (Math.abs(key - temp) <= k) {
        return true;
      }
      cache.set(value, key);
    } else {
      cache.set(value, key);
    }
  }

  return false;
};
```