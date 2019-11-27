---
title: leetcode -validPalindrome
date: 2019-02-22 09:41:56
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 验证回文串


<!-- more -->


## 思路

------

> 相关高阶`API`

- 利用正则匹配字母数字
- 转化为`字符串`比较
- 特殊情况处理

## 题解

------

```ts
/**
 * @param {string} s
 * @return {boolean}
 */
var isPalindrome = function (s) {
  if (!s) {
    return true;
  }

  const matchedStr = s.match(/\w{1}/g);

  if (!matchedStr) {
    return true;
  }

  const filteredStr = matchedStr.map((v) => v.toLowerCase());

  return filteredStr.join('') === filteredStr.reverse().join('');
};
```