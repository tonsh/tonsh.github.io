---
layout: post
title: LeetCode - Climbing Stairs 解题报告
date: 2014-11-04 18:46:33
categories: LeetCode OJ 解题报告
---

#####题目描述: [https://oj.leetcode.com/problems/climbing-stairs/](https://oj.leetcode.com/problems/climbing-stairs/)

```
You are climbing a stair case. It takes n steps to reach to the top.

Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

一个楼梯有 N 个台阶，每步可以爬 1 或 2 个台阶，爬完这个楼梯有多少种不同的方法？
```

##### 解题思路:

  首先需要简化问题，假设爬到第 n-1 阶有 f(n-1) 种方法，爬到第 n-2 阶有 f(n-2) 种方法，则爬到第 n 阶的方法有 f(n-1)+f(n-2) 种方法。即

```
f(n) = f(n-1) + f(n-2)
```

{% highlight python %}
class Solution:
    # @param n, an integer
    # @return an integer
    def climbStairs(self, n):
        if n == 0:
            return 1

        if n == 1:
            return 1

        result = [1, 1]
        i = 2
        while i <= n:
            result.append(result[i - 1] + result[i - 2])
            i += 1

        return result[n]
{% endhighlight %}


<pre class="reference">
练习:

    <a href="https://oj.leetcode.com/problems/unique-paths/" target="_blank">Unique Paths</a>
</pre>
