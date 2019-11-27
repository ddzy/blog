---
title: leetcode -deleteNodeInALinkedList
date: 2019-03-02 16:18:30
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 删除链表中的节点


<!-- more -->


## 思路

------

- 题目给出的`node`即为要删除的节点

## 题解

------

```ts
/**
 * @param {ListNode} node
 * @return {void} Do not return anything, modify node in-place instead.
 */
var deleteNode = function (node) {
  node.val = node.next.val;
  node.next = node.next.next;
};
```