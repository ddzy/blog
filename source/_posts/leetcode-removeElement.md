---
title: leetcode -removeElement
date: 2019-02-10 17:20:54
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 移除元素


<!-- more -->


## 思路

------

> 两种解法

- 遍历, 类似[removeDeplicates](https://blog.yyge.top/2019/02/10/leetcode-removeDuplicates/).
- 高阶api, 暴力解决.

## 题解

------

```ts
/**
 * @param {number[]} nums
 * @param {number} val
 * @return {number}
 */
var removeElement = function(nums, val) {
  while (nums.includes(val)) {
    nums.splice(nums.indexOf(val), 1);
  }

  return nums.length;
};
```