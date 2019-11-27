---
title: leetcode -implementStackUsingQueues
date: 2019-02-28 09:13:18
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 用队列实现栈


<!-- more -->


## 思路

------

> 常规解法

熟知`Stack`数据解构的特点, 运用数组的特性即可.

## 题解

------

```ts
/**
 * Initialize your data structure here.
 */
var MyStack = function() {
  this.stack = [];
};

/**
 * Push element x onto stack.
 * @param {number} x
 * @return {void}
 */
MyStack.prototype.push = function(x) {
  const stack = this.stack;
  stack.push(x);
};

/**
 * Removes the element on top of the stack and returns that element.
 * @return {number}
 */
MyStack.prototype.pop = function() {
  const stack = this.stack;
  return stack.pop();
};

/**
 * Get the top element.
 * @return {number}
 */
MyStack.prototype.top = function() {
  const stack = this.stack;
  return stack[stack.length - 1];
};

/**
 * Returns whether the stack is empty.
 * @return {boolean}
 */
MyStack.prototype.empty = function() {
  return this.stack.length === 0;
};
```