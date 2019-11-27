---
title: leetcode -findAllNumbersDisappearedInAnArray
date: 2019-03-30 10:33:33
tags: [leetcode]
categories: [algorithm]
---

Leetcode题解之——找到所有数组中消失的数字


<!-- more -->


## 思路

------

#### 思路一(耗时`168ms`)

- 使用额外的存储空间
- 存在性检测即可

## 题解

------

#### 解法一

```js
/**
 * @param {number[]} nums
 * @return {number[]}
 */
var findDisappearedNumbers = function(nums) {
  // TODO solution 1
  const temp = new Array(nums.length).fill(0);
  const result = [];

  nums.forEach((v, i) => {
    temp[v - 1] = v;
  });

  temp.forEach((v, i, s) => {
    !v && (result.push(i + 1));
  });

  return result;
};
```