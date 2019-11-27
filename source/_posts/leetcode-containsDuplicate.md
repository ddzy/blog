---
title: leetcode -containsDuplicate
date: 2019-02-26 16:10:42
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 存在重复元素


<!-- more -->


## 思路

------

> 思路一: `HashMap`

- 存在则直接返回
- 不存在则`map.set`

> 思路二: `Set`

- `Set`处理后的长度是否等于处理前的长度

## 题解

------

HashMap:

```ts
/**
 * @param {number[]} nums
 * @return {boolean}
 */
var containsDuplicate = function(nums) {
  const cache = new Map();

  for (const num of nums) {
    if (cache.has(num)) {
      return true;
    }
    cache.set(num, num);
  }

  return false;
};
```

Set:

```ts
/**
 * @param {number[]} nums
 * @return {boolean}
 */
var containsDuplicate = function(nums) {
  return new Set(nums).size !== nums.length;
};
```