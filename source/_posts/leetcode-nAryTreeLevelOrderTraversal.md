---
title: leetcode -nAryTreeLevelOrderTraversal
date: 2019-03-17 14:44:36
tags: [leetcode]
categories: [algorithm]
---

Leetcode题解之 —— N叉树bfs


<!-- more -->


## 思路

------

### 思路一(耗时`856`ms)

> 广度优先

- 分组

## 题解

------

- 解法一

```js
/**
 * @param {Node} root
 * @return {number[][]}
 */
var levelOrder = function (root) {
  if (!root) {
    return [];
  }

  const result = [];
  const queue = [root];

  while (queue.length) {
    const [group, len] = [[], queue.length];

    for (let i = 0; i < len; i++) {
      const node = queue.shift();
      node && (group.push(node.val));
      node && queue.push(...node.children);
    }
    result.push(group);
  }

  return result;
};
```