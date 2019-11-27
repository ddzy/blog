---
title: leetcode -minStack
date: 2019-02-22 16:35:27
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 最小栈


<!-- more -->


## 思路

------

> 数组思想

## 题解

------

```ts
/**
 * initialize your data structure here.
 */
var MinStack = function() {
  this.stack = [];

  function createNew() {
    return new MinStack();
  }
};

/**
 * @param {number} x
 * @return {void}
 */
MinStack.prototype.push = function(x) {
  this.stack.push(x);
};

/**
 * @return {void}
 */
MinStack.prototype.pop = function() {
  this.stack.pop();
};

/**
 * @return {number}
 */
MinStack.prototype.top = function() {
  return this.stack[this.stack.length - 1];
};

/**
 * @return {number}
 */
MinStack.prototype.getMin = function() {
  return this.stack.reduce((total, current) => {
    total = current < total ? current : total;
    return total;
  }, Number.MAX_VALUE);
};
```