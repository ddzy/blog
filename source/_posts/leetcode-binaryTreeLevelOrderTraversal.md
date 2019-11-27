---
title: leetcode -binaryTreeLevelOrderTraversal
date: 2019-02-18 11:16:56
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 二叉树的层次遍历 II


<!-- more -->


## 思路

------

> BFS算法

- `queue`队列辅助
- 内部循环分组
- `result`反转

## 题解

------

```ts
/**
 * @param {TreeNode} root
 * @return {number[][]}
 */
var levelOrderBottom = function (root) {
  if (!root) {
    return [];
  }

  const [result, queue] = [[], [root]];

  while (queue.length) {
    // 保存分组的值
    const [group, queueLen] = [[], queue.length];
    let count = 0;

    while (count < queueLen) {
      const currentNode = queue.shift();
      group.push(currentNode.val);
      currentNode.left && (queue.push(currentNode.left));
      currentNode.right && (queue.push(currentNode.right));
      count++;
    }

    result.push(group);
  }

  return result.reverse();
}
```
