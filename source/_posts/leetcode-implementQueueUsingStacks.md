---
title: leetcode -implementQueueUsingStacks
date: 2019-02-28 11:00:46
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 用栈实现队列


<!-- more -->


## 思路

------

熟知`队列`的特性, 使用数组模拟栈

## 题解

------

```ts
/**
 * Initialize your data structure here.
 */
var MyQueue = function() {
  this.queue = [];
};

/**
 * Push element x to the back of queue.
 * @param {number} x
 * @return {void}
 */
MyQueue.prototype.push = function(x) {
  this.queue.push(x);
};

/**
 * Removes the element from in front of queue and returns that element.
 * @return {number}
 */
MyQueue.prototype.pop = function() {
  return this.queue.shift();
};

/**
 * Get the front element.
 * @return {number}
 */
MyQueue.prototype.peek = function() {
  return (this.queue)[0];
};

/**
 * Returns whether the queue is empty.
 * @return {boolean}
 */
MyQueue.prototype.empty = function() {
  return this.queue.length === 0;
};
```