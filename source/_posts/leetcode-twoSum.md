---
title: leetcode -twoSum
date: 2019-02-09 14:03:50
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 两数之和

<!-- more -->


## 思路

------

> 双指针解法

- 两个指针
- `右指针 = 左指针 + 1`时重置
- 返回结果

## 题解

------

```ts
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function(nums, target) {
  let [left, right] = [0, nums.length - 1];

  while (right > 0 && right !== left) {
    if (nums[left] + nums[right] === target) {
      return [left, right];
    }

    if (right === left + 1) {
      left++;
      right = nums.length;
    }

    right--;
  }
};
```