---
layout: post
title: LeetCode - Pow(x, n) 解题报告
date: 2014-11-02 11:46:33
categories: LeetCode OJ 解题报告
---

#####题目描述: [https://oj.leetcode.com/problems/powx-n/](https://oj.leetcode.com/problems/powx-n/)

```
Implement pow(x, n).
```

##### 解题思路:

  题意很简单，计算一个浮点数的整数次幂。即若 n 大于 0，pow(x, n) 为 n 个 x 相乘；若 n 小于 0， pow(x, n) 为 n 个 1/n 相乘；按定义实现的算法时间复杂度为 O(n); 倘若 n 为一个非常大的数值时，如 100 亿甚至更大，让计算机进行 100 亿次乘法还是很耗时的。此时需要一种能减少相乘次数的算法。

  以 pow(x, 8) 为例, 倘若已知 pow(x, 4)，只需计算 1 次乘法；已知 pow(x, 2) 需计算 2 次乘法，即只需要 3 次相乘即可计算出 pow(x, 8) 的值。

	```
    pow(x, 8) = pow(x, 4) * pow(x, 4)
    pow(x, 4) = pow(x, 2) * pow(x, 2)
    pow(x, 2) = pow(x, 1) * pow(x, 1) = x * x
	```

  最终 pow 的问题简化为 lgn 次乘法运算的问题。考虑到 n 为奇数时，pow(x, n) = pow(x, n - 1) * x， 即最坏情况下，算法时间复杂度为 O(2lgn)。


  N |运算次数
  --- | ---
  2 | (1)
  3 | (2)
  4 | (2)
  8 | (3)
  11 | (6)
  1000,000,000 | (37)

{% highlight python %}
class Solution:

    # @param x, a float
    # @param n, a integer
    # @return a float
    def pow(self, x, n):
        self._pow_mapper = dict()
        return self.pow0(x, n)
        
    def pow0(self, x, n):
       if n in self._pow_mapper:
           return self._pow_mapper[n]
           
       if n == 0:
           return 1
           
       if n > 0:
           if n % 2 == 0:
               r = self.pow0(x, n /2) * self.pow0(x, n / 2)
           else:
               r = self.pow0(x, n / 2) * self.pow0(x, n /2) * x
       else:
           m = -n
           if n % 2 == 0:
               r = 1 / (self.pow0(x, m / 2) * self.pow0(x, m / 2))
           else: 
               r = 1 / (self.pow0(x, m / 2) * self.pow0(x, m / 2) * x)
       self._pow_mapper[n] = r
       return r
{% endhighlight %}
