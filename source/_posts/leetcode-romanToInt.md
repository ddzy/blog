---
title: leetcode -romanToInt
date: 2019-02-10 10:01:30
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 罗马数字转整数


<!-- more -->


## 思路

------

> 暴力解法 + Map

- 两个`Map`
- 指针移动距离

## 题解

------

```ts
/**
 * @param {string} s
 * @return {number}
 */
var romanToInt = function(s) {
  const romanToIntMap = new Map([
    ['I', 1],
    ['V', 5],
    ['X', 10],
    ['L', 50],
    ['C', 100],
    ['D', 500],
    ['M', 1000],
  ]);
  const specialIntMap = new Map([
    ['IV', 4],
    ['IX', 9],
    ['XL', 40],
    ['XC', 90],
    ['CD', 400],
    ['CM', 900],
  ]);

  if (s.length === 1) {
    return romanToIntMap.get(s);
  }

  let [count, result] = [0, 0];

  while (count < s.length) {
    if (specialIntMap.has(s[count] + s[count + 1])) {
      result += specialIntMap.get(s[count] + s[count + 1]);
      count += 2;
    } else {
      result += romanToIntMap.get(s[count]);
      count += 1;
    }
  }

  return result;
};
```