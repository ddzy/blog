---
title: leetcode -isValidParentheses
date: 2019-02-10 13:45:06
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 有效的括号


<!-- more -->


## 思路

------

> 堆栈

- 空字符串情况
- stack, map
- 后进先出

## 题解

------

```ts
/**
 * @param {string} s
 * @return {boolean}
 */
var isValid = function (s) {
  if (s === '') {
    return true;
  }

  const [stack, map, len] = [[], new Map([
    ['(', ')'],
    ['[', ']'],
    ['{', '}'],
  ]), s.length];
  let count = 0;

  while (count < len) {
    if (!stack.length) {
      stack.push(s[count]);
    } else {
      if (s[count] === map.get(stack[stack.length - 1])) {
        stack.pop();
      } else {
        stack.push(s[count]);
      }
    }
    count++;
  }

  return !stack.length;
};
```