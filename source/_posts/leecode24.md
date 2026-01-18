---
title: leecode24
date: 2019-06-03 14:03:23
tags: [leecode]
categories: [leecode]
---

![题目](/uploads/leecode24.PNG)


```

class Solution:
    def swapPairs(self, head: ListNode) -> ListNode:
        
        newhead = ListNode(0)
        
        newhead.next = head
        
        l1 = newhead
        if l1.next is None:
            return head
        l2 = newhead.next.next
        if l2 is None:
            return head
        while True:
            tmp = l1.next
            l1.next = l2
            tmp.next = l2.next
            l2.next = tmp
            l1 = tmp
            if l1.next is None:
                break
            l2 = l1.next.next
            try:
                l2.next
            except:
                break
        
        return newhead.next
```
