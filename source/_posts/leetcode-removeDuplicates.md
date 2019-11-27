---
title: leetcode -removeDuplicates
date: 2019-02-10 17:06:31
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 删除排序数组中的重复项


<!-- more -->


## 思路

------

> 遍历解法

- 已排序
- `当前项`与`下一项`关系
- 重置`下标`

## 题解

------

```ts
var removeDuplicates = function (nums) {
  let count = 0;

  while (count < nums.length) {
    if (nums[count] === nums[count + 1]) {
      nums.splice(count, 1);
    }else {
      count ++;
    }
  }

  return nums.length;
};
```