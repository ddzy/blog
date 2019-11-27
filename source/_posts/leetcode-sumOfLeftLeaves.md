---
title: leetcode -sumOfLeftLeaves
date: 2019-03-14 10:35:04
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 左叶子之和


<!-- more -->


## 思路

------

### 思路一(耗时`92`ms)

> 深度优先遍历

- 标记左叶子
- 根据标记与否添加至数组
- reduce迭代计算最终结果

## 题解

------

- 解法一

```js
/**
 * @param {TreeNode} root
 * @return {number}
 */
function dfs(root, result) {
  if (!root) {
    return null;
  }
  if (root.left) {
    root.left.flag = true;
  }
  if (!root.left && !root.right) {
    root.flag && (result.push(root.val));
  }

  dfs(root.left, result);
  dfs(root.right, result);
}

var sumOfLeftLeaves = function (root) {
  const result = [];

  dfs(root, result);

  return result.reduce((t, c) => {
    t += c;
    return t;
  }, 0);
};
```