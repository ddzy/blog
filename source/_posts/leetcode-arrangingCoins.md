---
title: leetcode -arrangingCoins
date: 2019-03-28 13:42:51
tags: [leetcode]
categories: [algorithm]
---

Leetcod题解之 —— 排列硬币


<!-- more -->


## 思路

------

### 思路一(耗时`156`ms)

> 步跳法

- 循环
- 步数step加一

## 题解

------

解法一:

```javascript
/**
 * @param {number} n
 * @return {number}
 */
var arrangeCoins = function(n) {
  // TODO solution 1
  let [step, result] = [1, 0];
  for (let i = 0; i < n; i += step) {
    step++;
    result++;
  }

  return result;
};
```