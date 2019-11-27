---
title: leetcode -removeDuplicatesFromSortedList
date: 2019-02-15 09:31:39
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 删除排序链表中的重复元素


<!-- more -->


## 思路

------

> 两种解法

1. 暴力遍历
2. 数组接管

## 题解

------

```ts
/**
 * @param {ListNode} head
 * @return {ListNode}
 */
var deleteDuplicates = function (head) {
  let cache = [];
  let current = head;

  while (current) {
    cache.push(current.val ? current.val : 0);
    current = current.next;
  }

  if (!cache.length) {
    return null;
  }

  cache = [...new Set(cache)];

  const final = cache
    .map((v) => new ListNode(v))
    .map((v, i, a) => {
      v.next = a[i + 1] ? a[i + 1] : null;
      return v;
    })

  return final[0];
};
```