---
title: leetcode -numberOfSegmentsInAString
date: 2019-03-17 16:51:47
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 字符串中的单词数


<!-- more -->


## 思路

------

### 思路一

> 文本替代法

- 空串、首位空格处理
- 正则replace掉字符串的空格
- 使用任意标志替换
- 计算split后的数组长度

## 题解

------

- 解法一

```js
/**
 * @param {string} s
 * @return {number}
 */
var countSegments = function (s) {
  s = s.trim();

  return !s
    ? 0
    : s
      .replace(/\s+/g, 'duan')
      .split('duan').length;
};
```