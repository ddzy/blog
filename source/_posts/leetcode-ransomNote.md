---
title: leetcode -ransomNote
date: 2019-03-13 14:35:34
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 赎金信


<!-- more -->


## 思路

------

### 思路一(`172`ms)

> 暴力解法

- 遍历`magazine`
- 如果`ransomNote`中存在对应的值, 则删除
- 返回`ransomNote`的剩余长度

### 思路二(`120`ms)

> 哈希计数法

- `cache`计数`ransomNote`的每个字符的出现次数
- 遍历`magazine`, 抵消值
  - 小于0判断
- 遍历`values()`查看是否有为抵消的数

## 题解

------

- 解法一

```js
/**
 * @param {string} ransomNote
 * @param {string} magazine
 * @return {boolean}
 */
var canConstruct = function(ransomNote, magazine) {
  // TODO solution 1
  ransomNote = ransomNote.split('');
  magazine = magazine.split('');

  for (const ch of magazine) {
    const where = ransomNote.indexOf(ch);

    where !== -1 && (ransomNote.splice(where, 1));
  }

  return ransomNote.length === 0;
};
```

- 解法二

```js
/**
 * @param {string} ransomNote
 * @param {string} magazine
 * @return {boolean}
 */
var canConstruct = function(ransomNote, magazine) {
  const cache = new Map();

  for (const ch of ransomNote) {
    cache.set(ch, cache.has(ch) ? cache.get(ch) + 1 : 1);
  }
  for (const ch of magazine) {
    cache.has(ch) && (cache.get(ch) > 0 && (cache.set(ch, cache.get(ch) - 1)));
  }

  for (const v of cache.values()) {
    if (v !== 0) {
      return false;
    }
  }

  return true;
};
```