---
title: leetcode -reverseLinkedList
date: 2019-02-26 15:41:02
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 反转链表


<!-- more -->


## 思路

------

> 思路一: 转化为数组

- 利用`reverse`反转

> 思路二: 双指针迭代

- `prev`指针存储上一个节点
- `current`迭代循环

## 题解

------

数组法:

```ts
/**
 * @param {ListNode} head
 * @return {ListNode}
 */
var reverseList = function(head) {
  // TODO solution 1
  const cache = [];
  let current = head;

  while (current) {
    cache.push(new ListNode(current.val));
    current = current.next;
  }

  if (!cache.length) {
    return null;
  }

  cache.reverse();

  for (const [index, node] of cache.entries()) {
    node.next = cache[index + 1];
  }

  return cache[0];
};
```

双指针法:

```ts
/**
 * @param {ListNode} head
 * @return {ListNode}
 */
var reverseList = function(head) {
  let prev = null;
  let current = head;

  while (current) {
    const next = current.next;
    current.next = prev;
    prev = current;
    current = next;
  }

  return prev;
};
```