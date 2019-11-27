---
title: leetcode -reverseString
date: 2019-03-10 15:35:44
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 反转字符串


<!-- more -->


## 思路

------

### 思路一(耗时196ms)

> 原生API

- 调用原生`reverse`即可

### 思路二(耗时300ms)

> 双指针法

- 头尾指针
- temp交换值

## 题解

------

### 解法一

```js
/**
 * @param {character[]} s
 * @return {void} Do not return anything, modify s in-place instead.
 */
var reverseString = function (s) {
  s.reverse();
};
```

### 解法二

```js
/**
 * @param {character[]} s
 * @return {void} Do not return anything, modify s in-place instead.
 */
var reverseString = function (s) {
  let [left, right] = [0, s.length - 1];

  while (left <= right) {
    const temp = s[left];
    s[left] = s[right];
    s[right] = temp;
    left ++;
    right --;
  }
};
```