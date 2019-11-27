---
title: leetcode -houseRobber
date: 2019-02-26 12:50:09
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 打家劫舍


<!-- more -->


## 思路

------

> 动态规划

```ts
如果arr.length = 1, 直接返回数组唯一项
如果arr.length === 2, 返回Math.max(第一项, 第二项)
如果arr.length === 3, 返回Math.max(第一项的结果 + 第三项, 第二项的结果)
如果arr.length === 4, 返回Math.max(第二项的结果 + 第四项, 第三项的结果);
如果arr.length === n, 返回Math.max(第n - 2项的结果 + 第n项, 第n - 1项的结果);

f(0) = arr[0]
f(1) = Math.max(arr[0], arr[1]);
f(2) = Math.max(f(0) + arr[2], f(1));
f(n) = Math.max(f(n - 2) + arr[n], f(n - 1]);

maxAmount - 上一次的总金额 - 第n - 1项的结果 - now
lastLastMoney - 上上一次的金额 - last
currentMoney - 本次的金额 - nums[i]
```

## 题解

------

```ts
/**
 * @param {number[]} nums
 * @return {number}
 */
var rob = function (nums) {
  let [lastLastMoney, maxAmount] = [0, 0];

  for (const currentMoney of nums) {
    const temp = lastLastMoney;
    lastLastMoney = maxAmount;
    maxAmount = Math.max(temp + currentMoney, maxAmount);
  }

  return maxAmount;
};
```