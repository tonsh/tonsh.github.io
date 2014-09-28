---
layout: post
title: LeetCode - Single Number 解题报告
date: 2014-07-13 13:46:33
categories: LeetCode OJ 解题报告
---

#####题目描述: [https://oj.leetcode.com/problems/single-number/](https://oj.leetcode.com/problems/single-number/)

```
Given an array of integers, every element appears twice except for one. Find that single one.

Note:
Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?
```

##### 解题思路:

  题中要求要在线性时间内，不开辟额外空间解决问题。考虑到异或运算的特性，恰好有:
	
	```
	a XOR a = 0
	0 XOR b = b
	```
即，原列表中所有出现过偶数次的数值由异或运算连接之后的结果为0; 假设原列表中仅出现一次的数值为 n, 根据 0 XOR n = n 可得 n 的值。
{% highlight python %}
class Solution:
    def singleNumber(self, A):
    	"""
        	@param A a list of integer
        	@return an integer
       	"""
    	result = A[0]
    	for item in A[1:]:
        	result = result^item
        return result
{% endhighlight %}
