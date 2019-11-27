---
title: leetcode -invertBinaryTree
date: 2019-02-28 09:54:29
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 翻转二叉树


<!-- more -->


## 思路

------

> 先序遍历

先序遍历, 交换左右节点, 注意`叶子节点`、`空节点`、`单个根节点`特殊情况的处理

## 题解

------

```ts
/**
 * @param {TreeNode} root
 * @return {TreeNode}
 */
var invertTree = function(root) {
  if (!root) {
    return null;
  }
  if (!root.left && !root.right) {
    return root;
  }

  [root.left, root.right] = [root.right, root.left];
  invertTree(root.left);
  invertTree(root.right);

  return root;
};
```