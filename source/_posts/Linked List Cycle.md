---
layout:     post
title:      Linked List Cycle
category:   LeetCode
tags: LinkedList
---

**Linked List Cycle**

> Given a linked list, determine if it has a cycle in it.
>
> To represent a cycle in the given linked list, we use an integer `pos` which represents the position (0-indexed) in the linked list where tail connects to. If `pos` is `-1`, then there is no cycle in the linked list.

拿到这个题的第一想法就是通过某个类似 list 的结构记住链表对象，然后每次循环都去比对，自然想到用 hash 表。然后运行时间

> Runtime: 1144 ms, faster than 5.08% of Python online submissions for Linked List Cycle.

有点长，复杂度应该是 O(n)。

第二个办法是找两只小狗，让他们以不同的速度跑，如果跑道是圆的，两只小狗就一定会再相遇的ヾ(◍°∇°◍)ﾉﾞ

这个方法的好处在于空间复杂度小点，因为不用存那么多东西。

<!--more-->

**方法一**

```python
class Solution(object):
    def hasCycle(self, head):
        """
        :type head: ListNode
        :rtype: bool
        """
        hash_list = []
        while head:
            hash_list.append(hash(head))
            if hash(head.next) in hash_list:
                return True
            head = head.next
        return False
```

**方法二**

```python
class Solution(object):
    def hasCycle(self, head):
        """
        :type head: ListNode
        :rtype: bool
        """
        l1 = l2 = head
        while l2 and l2.next:
            l1 = l1.next
            l2 = l2.next.next
            if l1 is l2:
                return True
        return False
```



