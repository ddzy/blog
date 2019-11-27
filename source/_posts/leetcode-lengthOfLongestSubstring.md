---
title: leetcode -lengthOfLongestSubstring
date: 2019-02-13 10:03:01
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 无重复字符的最长子串


<!-- more -->


## 思路

------

> 暴力解法

- 空字判断
- 连续字串
- 及时更新新的子串数组
- 更新`max`

## 题解

------

```ts
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLongestSubstring = function (s) {
  const len = s.length;
  let [max, count, cache] = [1, 0, []];

  if (!s) {
    return 0;
  }

  while (count < len) {
    const current = s[count];

    if(cache.includes(current)) {
      const finalIndex = cache.lastIndexOf(current);
      const filteredCache = cache.slice(finalIndex + 1);
      cache = [...filteredCache, current];
    }else {
      cache.push(current);
    }

    max = Math.max(cache.length, max);

    count++;
  }

  return max;
};
```