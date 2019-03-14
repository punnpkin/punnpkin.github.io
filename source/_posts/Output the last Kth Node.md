---
layout:     post
title:      Output the last Kth Node
category:   LeetCode
tags: LinkedList
---

这可能是一道剑指offer的题目(不是leetcode上的)，今天忘了在哪看的了

思路： 找两只小狗，第一只先跑到 k - 1 处，然后让它们同时跑，第一只跑完后返回第二只的位置

<!-- more -->

```python
#Definition for singly-linked list.
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None

class Solution:
    def lastKNode(self, head: ListNode, k) -> ListNode:
        l = head
        for _ in range(k - 1):
            head = head.next
        while head.next:
            head = head.next
            l = l.next
        return l
```

Test

```python
if __name__ == "__main__":
    l2 = l1 = ListNode(1)
    l1.next = ListNode(2)
    l1 = l1.next
    l1.next = ListNode(3)
    l1 = l1.next
    l1.next = ListNode(4)
    l1 = l1.next
    l1.next = ListNode(5)
    l1 = l1.next
    l1.next = ListNode(6)
    l1 = l1.next
    print(l1.val)
    s = Solution
    L = s.lastKNode(s, l2, 4)
    while L:
        print(L.val," -> ", end = "")
        L = L.next
    print("None")
# 3  -> 4  -> 5  -> 6  -> None
```