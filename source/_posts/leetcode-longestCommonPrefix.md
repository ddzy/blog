---
title: leetcode -longestCommonPrefix
date: 2019-02-10 12:21:11
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 最长公共前缀


<!-- more -->


## 思路

------

> 暴力遍历解法

- 找最小长度
- `every`判断对应字符

## 题解

------

```ts
/**
 * @param {string[]} strs
 * @return {string}
 */
var longestCommonPrefix = function (strs) {
  let [strsLen, result] = [strs.length, ''];

  if (!strsLen) {
    return result;
  }

  const minStrLen = strs.reduce((total, current) => {
    return Math.min(total, current.length)
  }, Number.MAX_VALUE);

  for (let i = 0; i < minStrLen; i++) {
    const strOne = strs[0][i];
    const flag = strs.every((v) => {
      return v[i] === strOne;
    });

    if (!flag) {
      break;
    }

    result += strOne;
  }

  return result;
};
```