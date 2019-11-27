---
title: leetcode -isSymmetricTree
date: 2019-02-15 11:47:07
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 对称二叉树


<!-- more -->


## 思路

------

> 参考[上一题](https://blog.yyge.top/2019/02/15/leetcode-isSameTree/)

## 题解

------

```ts
/**
 * @param {TreeNode} root
 * @return {boolean}
 */
var isSymmetric = function (root) {
  if (!root) {
    return true;
  }

  return isSameNode(root.left, root.right) && isSameNode(root.right, root.left);
};

function isSameNode(left, right) {
  if (!left && !right) {
    return true;
  }
  else if (!left || !right) {
    return false;
  }
  else if (left.val === right.val) {
    return isSameNode(left.left, right.right) && isSameNode(right.right, left.left);
  }
  return false;
}
```