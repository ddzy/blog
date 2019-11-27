---
title: leetcode -bestTimeToBuyAndSellStockII
date: 2019-02-20 11:34:55
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 买卖股票的最佳时机II


<!-- more -->


## 思路

------

> 参考[这里](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/solution/)

## 题解

------

```ts
/**
 * @param {number[]} prices
 * @return {number}
 */
var maxProfit = function(prices) {
  let maxProfit = 0;

  prices.forEach((v, i, self) => {
    (v - self[i - 1]) > 0 && (maxProfit += v - self[i - 1]);
  });

  return maxProfit;
};
```