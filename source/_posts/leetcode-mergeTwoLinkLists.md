---
title: leetcode -mergeTwoLinkLists
date: 2019-02-10 15:32:37
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 合并两个有序列表


<!-- more -->


## 思路

------

> js原生不具有`LinkList`, 使用`Array`实现

- push至数组
- 排序
- 重置指针
- 参数为空情况

## 题解

------

```ts
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 */
var mergeTwoLists = function(l1, l2) {
  const cache = [];

  while (l1) {
    cache.push(new ListNode(l1.val));
    l1 = l1.next;
  }
  while (l2) {
    cache.push(new ListNode(l2.val));
    l2 = l2.next;
  }

  cache.sort((a, b) => a.val - b.val);

  let [count, len] = [0, cache.length];

  if (len) {
    while(count < len - 1){
      cache[count].next = cache[count + 1];
      count++;
    }
    return cache[0];
  }

  return null;
};
```