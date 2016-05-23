---
layout: post
title: LeetCode - Counting Bits 解题报告
date: 2016-05-20 15:46:33
categories: LeetCode OJ 解题报告
---

#####题目描述: [https://leetcode.com/problems/counting-bits/](https://leetcode.com/problems/counting-bits/)

```
Given a non negative integer number num. For every numbers i in the range 0 ≤ i ≤ num calculate the number of 1's in their binary representation and return them as an array.

Example:
For num = 5 you should return [0,1,1,2,1,2].

Follow up:

It is very easy to come up with a solution with run time O(n*sizeof(integer)). But can you do it in linear time O(n) /possibly in a single pass?
Space complexity should be O(n).
Can you do it like a boss? Do it without using any builtin function like __builtin_popcount in c++ or in any other language.

输入正整数 num, 输出0至num 之间所有的整数二进制表示 1 的个数；
如 num = 5, 输出列表 [0,1,1,2,1,2]
要求：
1. 时间复杂度为 O(n)
2. 空间负责度为 O(n)
3. 不使用内置函数？没看懂，所以一下实现没有使用 math.log
```

##### 解题思路:

n^2 ~ (n + 1)^2 的二进制 1 的个数分别对应 0 ~ n^2-1 的个数 + 1

```
0~1: 0, 1
2~3: 1, 2
4~7: 1, 2, 2, 3
8~15: 1, 2, 2, 3, 2, 3, 3, 4
```

{% highlight python %}
class Solution(object):
    def countBits(self, num):
        result = [0] * (num + 1)

        power = 1
        next_power = power * 2
        for i in xrange(1, num + 1):
            if i == next_power:
                power = power * 2
                next_power = next_power * 2
            result[i] = result[i - power] + 1

        return result
{% endhighlight %}

