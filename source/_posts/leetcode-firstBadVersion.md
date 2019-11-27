---
title: leetcode -firstBadVersion
date: 2019-03-09 13:55:58
tags: leetcode
categories: algorithm
---

Leetcode题解之 —— 第一个错误的版本


<!-- more -->


## 思路

------

### 思路一

> 二分查找法

- 中间值取整
- middle值符合条件, 找其后半部分
- 反之, 查找前半部分

## 题解

------

```ts
/**
 * Definition for isBadVersion()
 *
 * @param {integer} version number
 * @return {boolean} whether the version is bad
 * isBadVersion = function(version) {
 *     ...
 * };
 */

/**
 * @param {function} isBadVersion()
 * @return {function}
 */
var solution = function (isBadVersion) {
    /**
     * @param {integer} n Total versions
     * @return {integer} The first bad version
     */
    return function (n) {
        let [start, end] = [1, n];
        while (start < end) {
            let middle = ~~((start + end) / 2);

            if (isBadVersion(middle)) {
                end = middle;
            } else {
                start = middle + 1;
            }
        }

        return start;
    };
};
```