---
title: leetcode -moveZeroes
date: 2019-03-09 14:29:03
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 移动零


<!-- more -->


## 思路

------

### 思路一

> 迭代法

- 尾遍历
- 如果等于0
  - `splice`剔除本数
  - `push`0

## 题解

------

```ts
/**
 * @param {number[]} nums
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var moveZeroes = function(nums) {
  // TODO solution 1
  for (let i = nums.length - 1; i >= 0; i--) {
    if (nums[i] === 0) {
      nums.splice(i, 1);
      nums.push(0);
    }
  }
};
```