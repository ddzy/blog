---
title: leetcode -hasLinkedListCycle
date: 2019-02-22 10:37:07
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 环形链表


<!-- more -->


## 思路

------

> 追及问题

- 快慢指针

## 题解

------

```ts
/**
 * @param {ListNode} head
 * @return {boolean}
 */
var hasCycle = function(head) {
  // TODO 追击问题

  let [slow, fast, flag] = [head, head, false];

  while (fast && fast.next) {
    fast = fast.next.next;
    slow = slow.next;

    if (fast == slow) {
      flag = true;
      break;
    }
  }

  return flag;
};
```