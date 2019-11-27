---
title: leetcode -addStrings
date: 2019-03-17 13:23:55
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 字符串相加


<!-- more -->


## 思路

------

### 思路一(耗时`126`ms)

> 双指针法

- 两个指针分别指向对应的数
- 同步遍历, 相加, 计算步数
- 注意最后一位进位

## 题解

------

- 解法一

```js
/**
 * @param {string} num1
 * @param {string} num2
 * @return {string}
 */
var addStrings = function(num1, num2) {
  // TODO solution 1
  let [trav1, trav2, step, result] = [num1.length - 1, num2.length - 1, 0, ''];

  while (trav1 >= 0 || trav2 >= 0) {
    const [v1, v2] = [
      num1[trav1] ? Number(num1[trav1]) : 0,
      num2[trav2] ? Number(num2[trav2]) : 0,
    ];
    const total = v1 + v2 + step;
    const temp = total % 10;
    result = temp + result;
    step = ~~(total / 10);

    trav1--;
    trav2--;
  }

  step && (result = step + result);

  return result;
};
```