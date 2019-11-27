---
title: leetcode -isSameTree
date: 2019-02-15 10:59:57
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 相同的树


<!-- more -->


## 思路

------

> 递归

- 四种情况
  - 都为叶节点
  - 其中一方为叶节点
  - `val`相等
  - traversal

## 题解

------

```ts
/**
 * @param {TreeNode} p
 * @param {TreeNode} q
 * @return {boolean}
 */
var isSameTree = function (p, q) {
  if (!p && !q) {
    return true;
  }
  else if (!p || !q) {
    return false;
  }
  else if (p.val === q.val) {
    return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
  }
  return false;
};
```