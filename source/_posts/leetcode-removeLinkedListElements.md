---
title: leetcode -removeLinkedListElements
date: 2019-02-26 13:28:49
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 移除链表元素


<!-- more -->


## 思路

------

> 方法一: 转化为数组

- 通过`filter`、`map`高阶API解决

> 方法二: 修改`next`指针

- 创建`firstNode`节点, 指向`head`
- 两个指针`lastNode`、`currentNode`
  - 前者指向`firstNode`, 表示上一个节点
  - 后者指向`head`, 当前遍历的节点

## 题解

------

数组解法:

```ts
  const cache = [];
  let current = head;

  while (current) {
    cache.push(new ListNode(current.val));
    current = current.next;
  }

  if (!cache.length) {
    return null;
  }

  const temp = cache
    .filter((v) => v.val !== val)
    .map((v, i, self) => {
      v.next = self[i + 1];

      return v;
    })

  if (!temp.length) {
    return null;
  }

  return temp[0];
```

指针解法:

```ts
/**
 * @param {ListNode} head
 * @param {number} val
 * @return {ListNode}
 */
var removeElements = function (head, val) {
  const firstNode = new ListNode(0);
  firstNode.next = head;

  let [lastNode, currentNode] = [firstNode, head];

  while (currentNode) {
    if (currentNode.val === val) {
      lastNode.next = currentNode.next;
    } else {
      lastNode = currentNode;
    }
    currentNode = currentNode.next;
  }

  return firstNode.next || null;
};
```