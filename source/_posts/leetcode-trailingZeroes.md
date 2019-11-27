---
title: leetcode -trailingZeroes
date: 2019-02-25 15:08:02
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 阶乘后的零


<!-- more -->


## 思路

------

很容易想到对`n`作阶乘的算法, 但是会出现爆栈的情况, 看了下[官方的题解](https://leetcode-cn.com/problems/factorial-trailing-zeroes/comments/), 只需要找到`n`中`5`的因子的个数即可

## 题解

------

首先想到的`阶乘法`, 会出现`Maximun stack...`的情况

```ts
/**
 * @param {number} n
 * @return {number}
 */
function factorial(num) {
  return num === 1
    ? 1
    : num * factorial(num--);
}

var trailingZeroes = function(n) {
  const factorialedNum = factorial(n);
  const stringedNum = String(factorialedNum);

  let [count, result] = [stringedNum.length - 1, 0];

  while (count >= 0) {
    if (~~(stringedNum[count])) {
      break;
    }
    result++;
    count--;
  }

  return result;
};
```

最优解

```ts
/**
 * @param {number} n
 * @return {number}
 */
var trailingZeroes = function (n) {
  let result = 0;
  let current = n;

  while (current) {
    result += (n = ~~(n / 5));
    current--;
  }

  return result;
};
```