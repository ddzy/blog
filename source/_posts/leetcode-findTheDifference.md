---
title: leetcode -findTheDifference
date: 2019-03-13 15:54:51
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 找不同


<!-- more -->


## 思路

------

### 思路一(`100`ms)

> 哈希计数法

- 遍历参数二, 更新其map中的数量
- 遍历参数一, map中不存在则直接`return`, 反之更新数量
- 遍历map, 返回`value`为`1`的键.

### 思路二(`88`ms)

> 差值法

- 分别计算两者的`charCodeAt`总和
- 计算两者的差
- 返回该`差`对应的`字符`

## 题解

------

- 解法一

```js
/**
 * @param {string} s
 * @param {string} t
 * @return {character}
 */
var findTheDifference = function(s, t) {
  const cache = new Map();

  for (const ch of t) {
    cache.set(ch, cache.get(ch) ? cache.get(ch) + 1 : 1);
  }
  for (const ch of s) {
    if (!cache.has(ch)) {
      return ch;
    }
    else {
      cache.set(ch, cache.get(ch) - 1 < 0 ? 0 : cache.get(ch) - 1);
    }
  }

  for (const [v, i] of cache.entries()) {
    if(i !== 0) {
      return v;
    }
  }
};
```

- 解法二

```js
/**
 * @param {string} s
 * @param {string} t
 * @return {character}
 */
function computeStringSum(str) {
  let amount = 0;

  for (const ch of str) {
    amount += ch.charCodeAt();
  }

  return amount;
}
var findTheDifference = function(s, t) {
  const sumOfS = computeStringSum(s);
  const sumOfT = computeStringSum(t);

  return String.fromCharCode(sumOfT - sumOfS);
};

```