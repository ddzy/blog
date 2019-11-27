---
title: leetcode -missingNumber
date: 2019-03-09 13:23:02
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 缺失数字


<!-- more -->


## 思路

------

### 思路一

> 缺失比对法

题目要求O(1)空间复杂度, 所以使用字符串来解决

- 字符串填充至数列长度加1
- 将数列插入字符串
- 遍历字符串
- 得出答案

## 题解

------

- 解法一

```ts
/**
 * @param {number[]} nums
 * @return {number}
 */
var missingNumber = function(nums) {
  const cache = new Array(nums.length + 1).fill('$');

  for (const [index, num] of nums.entries()) {
    cache[num] = num;
  }

  return cache.findIndex((v) => v === '$');
};
```