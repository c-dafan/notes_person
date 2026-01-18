---
title: leecode/math50
date: 2019-05-24 10:55:52
tags: [leecode]
categories: [leecode]
---

![题](/uploads/leecode50.png)

```python
class Solution:
    def myPow(self, x: float, n: int) -> float:
        if n == 0:
            return 1
        if n > 0:
            return self.core_func(x, n)
        if n < 0:
            return 1 / self.core_func(x, -n)
        
    def core_func(self, x, n):
        if n == 1:
            return x
        if n % 2 == 0:
            a = self.myPow(x, n//2)
            return a*a
        else:
            a = self.myPow(x, n //2)
            return a * a * x
```