---
title: leetcode -countAndSay
date: 2019-02-11 10:45:35
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 报数


<!-- more -->


## 思路

------

> 正则

- 下一次的值依赖于`current`的值
- 统计`current`的出现次数

## 题解

------

```ts
/**
 * @param {number} n
 * @return {string}
 */
var countAndSay = function (n) {
  /**
   * 1. one 1 -> 一个1 -> 1
   * 2. two 1 -> 两个1 -> 11
   * 3. one 2 + one 1 -> 一个2, 一个1 -> 21
   * 4. one 1 + one 2 + two 1 -> 一个1, 一个2, 一个1 -> 1211
   * 5. one 1 + one 2 + two 1 -> 一个1, 一个2, 两个1 -> 111221
   */

  let [count, result, reg] = [1, '1',
    /(?:1)+|(?:2)+|(?:3)+|(?:4)+|(?:5)+|(?:6)+|(?:7)+|(?:8)+|(?:9)+/g,
  ];

  while (count < n) {
    result = result
      .match(reg)
      .map((v) => `${v.length}${v[0]}`)
      .join('');

    count++;
  }

  return result;
};
```