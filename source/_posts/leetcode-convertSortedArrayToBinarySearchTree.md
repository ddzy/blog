---
title: leetcode -convertSortedArrayToBinarySearchTree
date: 2019-02-19 14:55:05
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 将有序数组转换为二叉搜索树


<!-- more -->


## 思路

------

> 二分法

- 递归
- 中间的值为当前节点
- 左边数组为左子树
- 右边数组为右子树
- `nums`为空, `length`为一, 边界判断, 终止递归

## 题解

------

```ts
/**
 * @param {number[]} nums
 * @return {TreeNode}
 */
var sortedArrayToBST = function(nums) {
  if (!nums.length) {
    return null;
  }
  else if (nums.length === 1) {
    return new TreeNode(nums[0]);
  }
  const [middle] = [~~((nums.length) / 2)];
  const node = new TreeNode(nums[middle]);

  node.left = sortedArrayToBST(nums.slice(0, middle));
  node.right = sortedArrayToBST(nums.slice(middle + 1));

  return node;
};
```