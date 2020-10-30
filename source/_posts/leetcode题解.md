---
title: leetcode题解
date: 2019-02-09 13:58:15
tags: leetcode
categories: algorithm
---

闲时玩玩算法, 不失为一个好的选择.


<!-- more -->


## 更新

------

### [2020-10-30]

#### Changed

- 更新文章链接

## 目录

------

- [1-两数之和](https://yyge.top/2019/02/09/leetcode-twoSum/)
- [2-两数相加](https://yyge.top/2019/02/13/leetcode-addTwoNumbers/)
- [3-无重复字符的最长子串](https://yyge.top/2019/02/13/leetcode-lengthOfLongestSubstring/)
- [4-寻找两个有序数组的中位数](https://yyge.top/blog/2019/04/06/leetcode-medianOfTwoSortedArrays/)
- [7-整数反转](https://yyge.top/2019/02/09/leetcode-reverseInteger/)
- [9-判断回文数](https://yyge.top/2019/02/09/leetcode-isPalindrome/)
- [13-罗马数字转整数](https://yyge.top/2019/02/10/leetcode-romanToInt/)
- [14-最长公共前缀](https://yyge.top/2019/02/10/leetcode-longestCommonPrefix/)
- [20-有效的括号](https://yyge.top/2019/02/10/leetcode-isValidParentheses/)
- [21-合并两个有序链表](https://yyge.top/2019/02/10/leetcode-mergeTwoLinkLists/)
- [26-删除排序数组中的重复项](https://yyge.top/2019/02/10/leetcode-removeDuplicates/)
- [27-移除元素](https://yyge.top/2019/02/10/leetcode-removeElement/)
- [28-实现strStr()](https://yyge.top/2019/02/11/leetcode-strStr/)
- [35-搜索插入位置](https://yyge.top/2019/02/11/leetcode-searchInsert/)
- [38-报数](https://yyge.top/2019/02/11/leetcode-countAndSay/)
- [53-最大子序和](https://yyge.top/2019/02/11/leetcode-maxSubArray/)
- [58-最后一个单词的长度](https://yyge.top/2019/02/11/leetcode-lengthOfLastWord/)
- [66-加一](https://yyge.top/2019/02/11/leetcode-plusOne/)
- [67-二进制求和](https://yyge.top/2019/02/13/leetcode-addBinary/)
- [69-x的平方根](https://yyge.top/2019/02/13/leetcode-mySqrt/)
- [70-爬楼梯](https://yyge.top/2019/02/13/leetcode-climbStairs/)
- [83-删除排序链表中的重复元素](https://yyge.top/2019/02/15/leetcode-removeDuplicatesFromSortedList/)
- [88-合并两个有序数组](https://yyge.top/2019/02/15/leetcode-mergeSortedArray/)
- [100-相同的树](https://yyge.top/2019/02/15/leetcode-isSameTree/)
- [101-对称二叉树](https://yyge.top/2019/02/15/leetcode-isSymmetricTree/)
- [104-二叉树的最大深度](https://yyge.top/2019/02/16/leetcode-maximunDepthOfBinaryTree/)
- [107-二叉树的层次遍历 II](https://yyge.top/2019/02/18/leetcode-binaryTreeLevelOrderTraversal/)
- [108-将有序数组转换为二叉搜索树](https://yyge.top/2019/02/19/leetcode-convertSortedArrayToBinarySearchTree/)
- [110-判断平衡二叉树](https://yyge.top/2019/02/20/leetcode-isBalancedBinaryTree/)
- [121-买卖股票的最佳时机](https://yyge.top/2019/02/20/leetcode-bestTimeToBuyAndSellStock/)
- [122-买卖股票的最佳时机II](https://yyge.top/2019/02/20/leetcode-bestTimeToBuyAndSellStockII/)
- [125-验证回文串](https://yyge.top/2019/02/22/leetcode-validPalindrome/)
- [136-只出现一次的数字](https://yyge.top/2019/02/22/leetcode-singleNumber/)
- [141-环形链表](https://yyge.top/2019/02/22/leetcode-hasLinkedListCycle/)
- [155-最小栈](https://yyge.top/2019/02/22/leetcode-minStack/)
- [160-相交链表](https://yyge.top/2019/02/22/leetcode-intersectionOfTwoLinkedLists/)
- [167-两数之和 II - 输入有序数组](https://yyge.top/2019/02/25/leetcode-twoSumIIInputArrayIsSorted/)
- [169-求众数](https://yyge.top/2019/02/25/leetcode-majorityElement/)
- [172-阶乘后的零](https://yyge.top/2019/02/25/leetcode-trailingZeroes/)
- [189-旋转数组](https://yyge.top/2019/02/25/leetcode-rotateArray/)
- [190-颠倒二进制位](https://yyge.top/2019/02/25/leetcode-reverseBits/)
- [191-位1的个数](https://yyge.top/2019/02/25/leetcode-hammingWeight/)
- [198-打家劫舍](https://yyge.top/2019/02/26/leetcode-houseRobber/)
- [203-移除链表元素](https://yyge.top/2019/02/26/leetcode-removeLinkedListElements/)
- [206-反转链表](https://yyge.top/2019/02/26/leetcode-reverseLinkedList/)
- [217-存在重复元素](https://yyge.top/2019/02/26/leetcode-containsDuplicate/)
- [219-存在重复元素ii](https://yyge.top/2019/02/28/leetcode-containsNearbyDuplicateII/)
- [225-用队列实现栈](https://yyge.top/2019/02/28/leetcode-implementStackUsingQueues/)
- [226-翻转二叉树](https://yyge.top/2019/02/28/leetcode-invertBinaryTree/)
- [231-2的幂](https://yyge.top/2019/02/28/leetcode-isPowerOfTwo/)
- [232-用栈实现队列](https://yyge.top/2019/02/28/leetcode-implementQueueUsingStacks/)
- [234-回文链表](https://yyge.top/2019/02/28/leetcode-isPalindromeLinkedList/)
- [235-二叉搜索树的最近公共祖先](https://yyge.top/2019/02/28/leetcode-lowestCommonAncestorOfABinarySearchTree/)
- [237-删除链表中的节点](https://yyge.top/blog/2019/03/02/leetcode-deleteNodeInALinkedList/)
- [242-有效的字母异位词](https://yyge.top/blog/2019/03/02/leetcode-validAnagram/)
- [258-各位相加](https://yyge.top/blog/2019/03/09/leetcode-addDigits/)
- [263-丑数](https://yyge.top/blog/2019/03/09/leetcode-uglyNumber/)
- [268-缺失数字](https://yyge.top/blog/2019/03/09/leetcode-missingNumber/)
- [278-第一个错误的版本](https://yyge.top/blog/2019/03/09/leetcode-missingNumber/)
- [283-移动零](https://yyge.top/blog/2019/03/09/leetcode-moveZeroes/)
- [326-3的幂](https://yyge.top/blog/2019/03/09/leetcode-powerOfThree/)
- [342-4的幂](https://yyge.top/blog/2019/03/09/leetcode-powerOfFour/)
- [344-反转字符串](https://yyge.top/blog/2019/03/10/leetcode-reverseString/)
- [345-反转字符串中的元音字母](https://yyge.top/blog/2019/03/10/leetcode-reverseVowelsOfAString/)
- [249-两个数组的交集](https://yyge.top/blog/2019/03/10/leetcode-intersectionOfTwoArrays/)
- [250-两个数组的交集II](https://yyge.top/blog/2019/03/10/leetcode-intersectionOfTwoArraysII/)
- [367-有效的完全平方数](https://yyge.top/blog/2019/03/12/leetcode-validPerfectSquare/)
- [371-两整数之和](https://yyge.top/blog/2019/03/13/leetcode-sumOfTwoIntegers/)
- [383-赎金信](https://yyge.top/blog/2019/03/13/leetcode-ransomNote/)
- [387-字符串中的第一个唯一字符](https://yyge.top/blog/2019/03/13/leetcode-firstUniqueCharacterInAString/)
- [389-找不同](https://yyge.top/blog/2019/03/13/leetcode-findTheDifference/)
- [404-左叶子之和](https://yyge.top/blog/2019/03/14/leetcode-sumOfLeftLeaves/)
- [412-fizz-buzz](https://yyge.top/blog/2019/03/14/leetcode-fizzBuzz/)
- [414-第三大的数](https://yyge.top/blog/2019/03/14/leetcode-thirdMaximumNumber/)
- [415-字符串相加](https://yyge.top/blog/2019/03/17/leetcode-addStrings/)
- [429-n-ary-tree-level-order](https://yyge.top/blog/2019/03/17/leetcode-nAryTreeLevelOrderTraversal/)
- [434-字符串中的单词数](https://yyge.top/blog/2019/03/17/leetcode-numberOfSegmentsInAString/)
- [441-排列硬币](https://yyge.top/blog/2019/03/28/leetcode-arrangingCoins/)
- [443-压缩字符串](https://yyge.top/blog/2019/03/30/leetcode-stringCompression/)
- [448-找到所有数组中消失的数字](https://yyge.top/blog/2019/03/30/leetcode-findAllNumbersDisappearedInAnArray/)