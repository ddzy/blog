---
title: leetcode -stringCompression
date: 2019-03-30 09:35:50
tags: [leetcode]
categories: [algorithm]
---

Leetcode题解之——压缩字符串


<!-- more -->


## 思路

------

### 思路一(耗时`186ms`)

- 外层循环遍历样本
- 内层循环计数
  - 更新计数器count
- splice截取数组

## 题解

------

解法一:

```js
/**
 * @param {character[]} chars
 * @return {number}
 */
var compress = function (chars) {
  let count;

  for (let i = 0; i < chars.length - 1; i++) {
    if (chars[i + 1] === chars[i]) {
      count = 1;

      for (let j = i + 1; j < chars.length; j++) {
        if (chars[j] !== chars[i]) {
          break;
        }
        count++;
      }

      count !== 1 && chars.splice(i + 1, count - 1, ...('' + count));

      i++;
    }
  }

  return chars.length;
};
```