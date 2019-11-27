---
title: leetcode -addTwoNumbers
date: 2019-02-13 08:49:08
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 两数相加


<!-- more -->


## 思路

------

- 多指针
- 对应相加
- 注意增量

## 题解

------

```ts
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 */
var addTwoNumbers = function(l1, l2) {
  const result = new ListNode(0);
  let [current, incremental] = [result, 0];

  while (l1 || l2) {
    const [v1, v2] = [
      l1 ? l1.val : 0,
      l2 ? l2.val : 0,
    ];

    const sum = v1 + v2 + incremental;
    current = current.next = new ListNode(sum % 10);
    incremental = ~~(sum / 10);

    [l1, l2] = [l1 ? l1.next: null, l2 ? l2.next : null];
  }

  if (incremental) {
    current.next = new ListNode(incremental);
  }

  return result.next;
};
```