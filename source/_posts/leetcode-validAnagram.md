---
title: leetcode -validAnagram
date: 2019-03-02 16:35:55
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 有效的字母异位词


<!-- more -->


## 思路

------

> 核心就是比较两个字符串是否完全相等

### 方法一:  字符串-数组-字符串compare

> 用时: `480`ms

- 转化为数组
- 排序
- 重新转化为字符串
- 比较

### 方法二:  剔除法

> 用时:  `728`ms

- 较耗时
- 遍历前者字符串, `replace`掉后者的对应项
- 根据是否空串判断

## 题解

------

### 解法一:

```ts
/**
 * @param {string} s
 * @param {string} t
 * @return {boolean}
 */
function aidedCompute(str) {
  return str.split('').sort((a, b) => a.localeCompare(b)).join('');
}
var isAnagram = function(s, t) {
  const [processedS, processedT] = [aidedCompute(s), aidedCompute(t)];

  return processedS === processedT;
};
```

### 解法二:

```ts
/**
 * @param {string} s
 * @param {string} t
 * @return {boolean}
 */
var isAnagram = function (s, t) {
  if (!s && !t) {
    return true;
  }
  if (s.length !== t.length) {
    return false;
  }

  s.split('').forEach((v) => {
    t = t.replace(v, '');
  });

  return !(!!t);
};
```