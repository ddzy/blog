---
title: leetcode -reverseVowelsOfAString
date: 2019-03-10 16:09:45
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 反转字符串中的元音字母


<!-- more -->


## 思路

------

### 思路一(耗时`136ms`)

> 双指针法

- 左右指针
- 步数调整
- 元音字母字典
- 大小写转换

## 题解

------

- 解法一

```js
/**
 * @param {string} s
 * @return {string}
 */
var reverseVowels = function (s) {
  s = s.split('');

  const baseMap = new Map([
    ['a', 'a'],
    ['e', 'e'],
    ['i', 'i'],
    ['o', 'o'],
    ['u', 'u'],
  ]);
  let [left, right] = [0, s.length - 1];

  while (left <= right) {
    if (baseMap.has(s[left].toLowerCase()) && baseMap.has(s[right].toLowerCase())) {
      [s[left], s[right]] = [s[right], s[left]];
      left++;
      right--;
    }
    else if (baseMap.has(s[left].toLowerCase()) && !baseMap.has(s[right].toLowerCase())) {
      right--;
    }
    else if (!baseMap.has(s[left].toLowerCase()) && baseMap.has(s[right].toLowerCase())) {
      left++;
    }
    else {
      left++;
      right--;
    }
  }

  return s.join('');
};
```