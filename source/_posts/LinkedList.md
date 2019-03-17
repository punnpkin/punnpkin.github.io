---
layout:     post
title:      LinkedList
category:   LeetCode
tags: LinkedList
description: LinkedList
---

**汇总链表的题目**

#### 21. Merge Two Sorted Lists

> Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.



一开始想的简单了 因为是 easy 类型的 还以为就是简单的合并一下（等长，不按顺序）

要求也没说清楚啊 ╭(╯^╰)╮



```python
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None

class Solution:
    def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
        l4 = l3 = ListNode(0)
        if not l1 or not l2:
            return l2 or l1
        
        while l1 and l2:
            if l1.val < l2.val:
                l3.next = l1
                l1 = l1.next
            else:
                l3.next = l2
                l2 = l2.next
            l3 = l3.next
        l3.next = l1 or l2        
        return l4.next
```

Test

```python
s  = Solution
l1 = ListNode(3)
l2 = ListNode(1)
l2.next = ListNode(2)
L = s.mergeTwoLists(s,l1,l2)
while L:
    print(L.val," -> ", end = "")
    L = L.next
print("None")

# 1  -> 2  -> 3  -> None
```

#### 24. Swap Nodes in Pairs

> Given a linked list, swap every two adjacent nodes and return its head. You may not modify the values in the list's nodes, only nodes itself may be changed.

思路：两个节点为一个单位做处理，循环每次交换两个相邻节点

在每一次循环时，先取出 `head.next` 赋给一个临时变量 `temp` , 然后用 `head.next = temp.next` 扔掉 `head` 中的 `next`, 再令 `temp.next = head` 完成交换节点的目的

<!-- more -->

```python
# Definition for singly-linked list.
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None

class Solution:
    def swapPairs(self, head: ListNode) -> ListNode:
        l1 = l2 = ListNode(0)
        
        if not head or not head.next:
            return head
        
        while head and head.next:
            a = head.next
            head.next = a.next
            a.next = head
            l2.next = a
            l2 = l2.next.next
            head = head.next
        
        return l1.next
```

Test

```python
l2 = l1 = ListNode(1)
l1.next = ListNode(2)
l1 = l1.next
l1.next = ListNode(3)
l1 = l1.next

s = Solution
L = s.swapPairs(s,l2)
while L:
    print(L.val," -> ", end = "")
    L = L.next
# 2  -> 1  -> 3  -> 
```

#### 61. Rotate List

>Given a linked list, rotate the list to the right by k places, where k is non-negative.

先获取长度，再把链表结成一个环，最后把指针指向那个点的前一个

```python
# Definition for singly-linked list.
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None

class Solution:
    def rotateRight(self, head: ListNode, k: int) -> ListNode:
        if not head:
            return None
        l1 = l2 = head
        m = 1
        while head.next:
            head = head.next
            m += 1
        k %= m
        head.next = l1
        for _ in range(m - k):
            head = head.next
        a = head.next
        head.next = None
        return a
```

#### 160. Intersection of Two Linked Lists

>Write a program to find the node at which the intersection of two singly linked lists begins.

```python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def getIntersectionNode(self, headA, headB):
        """
        :type head1, head1: ListNode
        :rtype: ListNode
        """
        if headA is None or headB is None:
            return None
        l1 = headA
        l2 = headB
        # 最后总是会到达同一个位置，None 或者交叉点
        while l1 != l2:
            l1 = l1.next if l1 else headB
            l2 = l2.next if l2 else headA
        return l1
```

#### 141. Linked List Cycle

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

#### 876. Middle of the Linked List

> Given a non-empty, singly linked list with head node head, return a middle node of linked list. If there are two middle nodes, return the second middle node.

两种思路，第二种实现仅仅需要五行。最近做题发现，很多时候解决一个问题，思路真的很重要。很多时候自己那些想法是需要被推翻和颠覆的。

第二种方法的思路是同时跑两个小狗，一个一次跑一步，另一个跑两步，关键在于设置停止的条件：当跑得快的小狗首先跑到结尾时有两种情况，一种是自己站在 `None` 处，另一种是它的下一步 `next` 指向了 `None` 。这两个条件的任何一个出现时，就去把跑得慢的小狗的位置返回去 -=≡Σ(((つ•̀ω•́)つ...

<!--more-->

##### 方法1

```python
#Definition for singly-linked list.
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None

class Solution:
    def middleNode(self, head: ListNode) -> ListNode:
        i = 0
        l = head
        while head:
            head = head.next
            i += 1
        for j in range(int(i/2)):
            l = l.next
        return l
```



##### 方法2

```python
#Definition for singly-linked list.
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None

class Solution:
    def middleNode(self, head: ListNode) -> ListNode:
        l = head
        while l and l.next:
            l = l.next.next
            head = head.next
        return head
       
```

Test

```python
s  = Solution
l1 = ListNode(1)
l1.next = ListNode(2)
l1 = l1.next
l1.next = ListNode(3)
l1 = l1.next
l1.next = ListNode(4)
l1 = l1.next
l1.next = ListNode(5)
L = s.middleNode(s,l1)
while L:
    print(L.val," -> ", end = "")
    L = L.next
print("None")
# 3 -> 4 -> 5
```