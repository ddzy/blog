---
title: leetcode -isBalancedBinaryTree
date: 2019-02-20 09:43:49
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 判断平衡二叉树


<!-- more -->


## 思路

------

> `DFS`算法
> 先序遍历

## 题解

------

```ts
/**
 * @param {TreeNode} root
 * @return {boolean}
 */
function getHeight(root) {
  if (!root) {
    return 0;
  }
  const leftH = getHeight(root.left);
  const rightH = getHeight(root.right);

  return Math.max(leftH, rightH) + 1;
}

var isBalanced = function (root) {
  if (!root) {
    return true;
  }

  const leftH = getHeight(root.left);
  const rightH = getHeight(root.right);

  if (Math.abs(leftH - rightH) > 1) {
    return false;
  }

  return isBalanced(root.left) && isBalanced(root.right);
};
```