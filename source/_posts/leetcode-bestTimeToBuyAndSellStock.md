---
title: leetcode -bestTimeToBuyAndSellStock
date: 2019-02-20 10:31:16
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 买卖股票的最佳时机


<!-- more -->


## 思路

------

> 两种方式

## 题解

------

- 暴力枚举

```ts
/**
 * @param {number[]} prices
 * @return {number}
 */
var maxProfit = function(prices) {
  let result = 0;

  for (let i = 0; i < prices.length; i++) {
    for (let j = i + 1; j < prices.length; j++) {
      result = Math.max(result, prices[j] - prices[i]);
    }
  }

  return result;
};
```

- 动态规划

> 参考[这里](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/solution/)

- 一次遍历
- 最小谷值
  - minPrice = Math.min(minPrice, prices[i]);
- 最大峰值
  - maxProfit = Math.max(maxProfit, prices[i] - minPrice);

```ts
/**
 * @param {number[]} prices
 * @return {number}
 */
var maxProfit = function(prices) {
  let [minPrice, maxProfit] = [Number.MAX_VALUE, 0];

  for (const price of prices) {
    minPrice = Math.min(minPrice, price);
    maxProfit = Math.max(maxProfit, price - minPrice);
  }

  return maxProfit;
};
```