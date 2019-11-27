---
title: leetcode -lowestCommonAncestorOfABinarySearchTree
date: 2019-02-28 14:07:19
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 二叉搜索树的最近公共祖先


<!-- more -->


## 思路

------

> 二叉搜索树

利用二叉搜索树的性质 - `左侧节点值必小于父节点, 右侧节点值必大于父节点`

两种情况:

- 分别在左右子树
- 同一侧
  - 递归左子树
  - 递归右子树

## 题解

------

```ts
/**
 * @param {TreeNode} root
 * @param {TreeNode} p
 * @param {TreeNode} q
 * @return {TreeNode}
 */
var lowestCommonAncestor = function(root, p, q) {
  const [rootVal, pVal, qVal] = [root.val, p.val, q.val];

  return pVal < rootVal && qVal < rootVal
    ? lowestCommonAncestor(root.left, p, q)
    : pVal > rootVal && qVal > rootVal
      ? lowestCommonAncestor(root.right, p, q)
      : root;
};
```