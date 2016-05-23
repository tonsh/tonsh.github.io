---
layout: post
title: LeetCode - Invert Binary Tree 解题报告
date: 2016-05-23 18:46:30
categories: LeetCode OJ 解题报告
---

#####题目描述: [https://leetcode.com/problems/add-digits/](https://leetcode.com/problems/add-digits/)

```
Given a non-negative integer num, repeatedly add all its digits until the result has only one digit.

For example:

Given num = 38, the process is like: 3 + 8 = 11, 1 + 1 = 2. Since 2 has only one digit, return it.

Follow up:
Could you do it without any loop/recursion in O(1) runtime?

输入一个非负正整数，将每一位的数值相加，重复这一操作，知道结果为一位整数；
如：
输入 num=38, 处理过程如： 3 + 8 = 11, 1 + 1 = 2. 结果为 2；

要求:
不是使用循环和递归，时间复杂度为 O(1)
```

##### 解题思路:

已知 10 % 9 =1, 100 % 9 = 1, 10^N % 9 =1, 8 % 9 = 8 不难证明
```
N = a0 * 10^n + a1 * 10^(n-1) + ... + an
N % 9 = a0 + a1 + .. + an % 9
由于 an <=9 所以有: sum(a0, a1, .., an) = N % 9

但是，以上公式对与 N 为 9 的倍数时不成立，所以需要注意这种异常情况；


f(x) = 0   if x = 0
f(x) = 9   if x % 9 == 0
f(x) = n % 9  if n !=0 and n % 9 != 0
```

{% highlight python %}
class Solution(object):
    def addDigits(self, num):
        if num == 0:
            return 0
            
        result = num % 9
        if result == 0:
            return 9
        return result
{% endhighlight %}

