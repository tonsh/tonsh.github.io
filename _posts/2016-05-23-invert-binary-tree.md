---
layout: post
title: LeetCode - Invert Binary Tree 解题报告
date: 2016-05-23 18:46:33
categories: LeetCode OJ 解题报告
---

#####题目描述: [https://leetcode.com/problems/invert-binary-tree/](https://leetcode.com/problems/invert-binary-tree/)

```
Invert a binary tree.

     4
   /   \
  2     7
 / \   / \
1   3 6   9
to
     4
   /   \
  7     2
 / \   / \
9   6 3   1

Trivia:
This problem was inspired by this original tweet by Max Howell:
    Google: 90% of our engineers use the software you wrote (Homebrew), but you can’t invert a binary tree on a whiteboard so fuck off.

  Google 说我们90%的工程师都在用你写的 homebrew，但是你在白板上连反转二叉树的代码都写不出来。
```

##### 解题思路:

看到答案的瞬间，请容许我去厕所哭会。。。


{% highlight python %}
class Solution(object):
    def invertTree(self, root):
        if root is not None:
            root.left, root.right = root.right, root.left
            self.invertTree(root.left)
            self.invertTree(root.right)
        return root
{% endhighlight %}

