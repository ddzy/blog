---
title: leetcode -lengthOfLastWord
date: 2019-02-11 13:54:02
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 最后一个单词的长度


<!-- more -->


## 思路

------

> 正则

- `(?!x)`
- 正则断言
- `match`分组

## 题解

------

```ts
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLastWord = function(s) {
  const reg = /\w+(?!\w+)/g;
  const matched = s.match(reg);

  return matched
    ? (matched[matched.length - 1]).length
    : 0;
};
```