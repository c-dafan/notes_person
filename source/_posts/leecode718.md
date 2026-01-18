---
title: leecode718
date: 2019-06-03 13:55:58
tags: [leecode]
categories: [leecode]
---

![题目](/uploads/leecode718.PNG)


比较简单的动态规划，自己能找到状态转换关系的第一道动态规划题


```python

class Solution:
    def findLength(self, A: List[int], B: List[int]) -> int:
        a_len = len(A)
        b_len = len(B)
        dp = [[0] * a_len for _ in range(b_len)]
        for i in range(a_len):
            if A[i] == B[0]:
                dp[0][i] = 1
        for j in range(b_len):
            if B[j] == A[0]:
                dp[j][0] = 1
        
        for i in range(1, b_len):
            for j in range(1, a_len):
                if A[j] == B[i]:
                    dp[i][j] = dp[i-1][j-1] + 1
        
        return max([max(x) for x in dp])
```