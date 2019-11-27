---
title: leetcode -maxSubArray
date: 2019-02-11 13:08:42
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 最大子序和


<!-- more -->


## 思路

------

> 动态规划

- `Math.max`更新最大值
- 更新`currentSum`, 即当前序列和

## 题解

------

```ts
/**
 * @param {number[]} nums
 * @return {number}
 */
var maxSubArray = function(nums) {
  let [maxSum, currentSum] = [-Number.MAX_VALUE, 0];

  for (const value of nums) {
    currentSum += value;

    maxSum = Math.max(currentSum, maxSum);
    currentSum = currentSum < 0 ? 0 : currentSum;
  }

  return maxSum;
};
```