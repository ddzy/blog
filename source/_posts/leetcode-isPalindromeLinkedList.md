---
title: leetcode -isPalindromeLinkedList
date: 2019-02-28 11:27:10
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 回文链表


<!-- more -->


## 思路

------

> O(n)空间复杂度

- 数组存储链表的值
- 转化为`回文数`解法

> O(1)空间复杂度

- 双字符串保存正反向的`val`
- 比对该两者

## 题解

------

O(n):

```ts
/**
 * @param {ListNode} head
 * @return {boolean}
 */
var isPalindrome = function(head) {
  const cache = [];
  let current = head;

  while (current) {
    cache.push(current.val);
    current = current.next;
  }

  if (cache.length === 1) {
    return true;
  }

  return cache.join('') === cache.reverse().join('');
};
```

O(1):

```ts
/**
 * @param {ListNode} head
 * @return {boolean}
 */
var isPalindrome = function(head) {
  let [forwardStr, reverseStr, current] = ['', '', head];

  while (current) {
    forwardStr += current.val;
    reverseStr = '' + current.val + reverseStr;
    current = current.next;
  }

  return forwardStr === reverseStr;
};
```