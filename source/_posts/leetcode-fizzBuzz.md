---
title: leetcode -fizzBuzz
date: 2019-03-14 13:42:16
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— fizz-buzz


<!-- more -->


## 思路

------

### 思路一

> 暴力遍历法(耗时`127`ms)

- 循环
- 判断每个数是否符合条件
- 逐步push

## 题解

------

- 解法一

```js
/**
 * @param {number} n
 * @return {string[]}
 */
var fizzBuzz = function (n) {
  // TODO solution 1
  const result = [];

  for (let i = 1; i <= n; i++) {
    const [mol3, mol5] = [i % 3, i % 5];

    result.push(
      !mol3 && !mol5
        ? 'FizzBuzz'
        : !mol3
          ? 'Fizz'
          : !mol5
            ? 'Buzz'
            : '' + i
    );
  }

  return result;
};
```