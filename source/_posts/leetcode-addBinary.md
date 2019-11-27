---
title: leetcode -addBinary
date: 2019-02-13 15:37:16
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 二进制求和


<!-- more -->


## 思路

------

> 暴力破解

- 结果存数组
- length重置
- 短字符串填充
- 逢2进1

## 求解

------

```ts
/**
 * @param {string} a
 * @param {string} b
 * @return {string}
 */
var addBinary = function(a, b) {
  if (a.length > b.length) {
    return addBinary(b, a);
  }

  const len = b.length;
  let [count, incremental, result] = [len - 1, 0, []];

  [a] = [a.padStart(len, '0')];

  while (count >= 0) {
    const [v1, v2] = [
      a[count] ? +(a[count]) : 0,
      b[count] ? +(b[count]) : 0,
    ];

    const temp = v1 + v2 + incremental;

    result.unshift(
      temp > 2
        ? 1
        : temp === 2
          ? 0
          : temp
    );
    incremental = temp >= 2 ? 1 : 0;

    count--;
  }

  incremental && (result.unshift(incremental));

  return result.join('');
};
```