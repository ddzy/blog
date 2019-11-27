---
title: leetcode -maximunDepthOfBinaryTree
date: 2019-02-16 09:37:15
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 二叉树的最大深度


<!-- more -->


## 思路

------

> `BFS`深度优先搜索

- 全局变量`max_depth`记录最大值
- 依次遍历左右子树, 更新`current_deep`

## 题解

------

```ts
/**
 * @param {TreeNode} root
 * @return {number}
 */
let max_depth = 0;

function dfs(root, count) {
  if (!root) {
    max_depth = Math.max(max_depth, count);
    return;
  }

  dfs(root.left, count + 1);
  dfs(root.right, count + 1);
}

var maxDepth = function (root) {
  if (!root) {
    return 0;
  }

  max_depth = -1;
  dfs(root, 0);

  return max_depth;
};
```