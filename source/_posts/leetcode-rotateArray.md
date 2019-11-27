---
title: leetcode -rotateArray
date: 2019-02-25 15:55:51
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 旋转数组


<!-- more -->


## 思路

------

> 题目要求至少写出三种方案

- 利用`pop`、`unshift`操作(用时263ms)
- 利用`splice`(用时106ms)

## 题解

------

方法一:

```ts
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var rotate = function (nums, k) {
  const len = nums.length;
  let count = len - 1;

  while (count >= len - k) {
    nums.unshift(nums.pop());
    count--;
  }
};
```

方法二:

```ts
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var rotate = function (nums, k) {
  const len = nums.length;
  const temp = nums.splice(len - k, k);
  nums.unshift(...temp);
};
```