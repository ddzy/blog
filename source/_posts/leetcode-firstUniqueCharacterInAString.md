---
title: leetcode -firstUniqueCharacterInAString
date: 2019-03-13 15:22:32
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 字符串中的第一个唯一字符


<!-- more -->


## 思路

------

### 思路一(`150`ms)

> 哈希计数法

- 遍历字符串
- 更新哈希计数
- 遍历哈希map, 找到第一个为0的项

## 题解

------

```js
/**
 * @param {string} s
 * @return {number}
 */
var firstUniqChar = function(s) {
  // TODO solution 1
  const cache = new Map();

  for (const ch of s) {
    cache.set(ch, cache.has(ch) ? cache.get(ch) + 1 : 0);
  }
  for (const [i, v] of cache.entries()) {
    if (!v) {
      return s.indexOf(i);
    }
  }

  return -1;
};
```