---
title: leetcode -intersectionOfTwoLinkedLists
date: 2019-02-22 17:03:51
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 相交链表


<!-- more -->


## 思路

------

> 缓存方式

## 题解

------

```ts
/**
 * @param {ListNode} headA
 * @param {ListNode} headB
 * @return {ListNode}
 */
var getIntersectionNode = function(headA, headB) {
  const cache = new Map();
  let [currentNodeA, currentNodeB] = [headA, headB];

  while (currentNodeA) {
    cache.set(currentNodeA, currentNodeA);
    currentNodeA = currentNodeA.next;
  }

  while (currentNodeB) {
    if (cache.has(currentNodeB)) {
      return currentNodeB;
    }
    currentNodeB = currentNodeB.next;
  }

  return null;
};
```