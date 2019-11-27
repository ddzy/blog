---
title: leetcode -twoSumIIInputArrayIsSorted
date: 2019-02-25 10:42:46
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 两数之和 II - 输入有序数组


<!-- more -->


## 思路

------

> 双指针

- 左右指针
- 相同的数可直接跳过

## 题解

------

```ts
/**
 * @param {number[]} numbers
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function (numbers, target) {
  let [left, right] = [0, numbers.length - 1];

  while (right >= left) {
    const sum = numbers[left] + numbers[right];
    if (sum === target) {
      return [left + 1, right + 1];
    }
    else if (right === left) {
      left++;
      right = numbers.length - 1;
    } else {
      right--;
    }
  }

  return [];
};
```