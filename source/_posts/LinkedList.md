---
layout:     post
title:      LinkedList
category:   LeetCode
tags: LinkedList
---

**汇总链表的题目**

```python
# Definition for singly-linked list.
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None
```

<!--more-->

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

------

#### 24. Swap Nodes in Pairs

> Given a linked list, swap every two adjacent nodes and return its head. You may not modify the values in the list's nodes, only nodes itself may be changed.

思路：两个节点为一个单位做处理，循环每次交换两个相邻节点

在每一次循环时，先取出 `head.next` 赋给一个临时变量 `temp` , 然后用 `head.next = temp.next` 扔掉 `head` 中的 `next`, 再令 `temp.next = head` 完成交换节点的目的

<!-- more -->

```python

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

------

#### 61. Rotate List

>Given a linked list, rotate the list to the right by k places, where k is non-negative.

先获取长度，再把链表结成一个环，最后把指针指向那个点的前一个

```python

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

#### 86. Partition List
>Given a linked list and a value x, partition it such that all nodes less than x come before nodes greater than or equal to x. You should preserve the original relative order of the nodes in each of the two partitions.

```python
class Solution:
    def partition(self, head: ListNode, x: int) -> ListNode:
        A = l1 = ListNode(0)
        B = l2 = ListNode(0)
        
        while head:
            if head.val < x:
                l1.next = ListNode(head.val)
                l1 = l1.next
            else:
                l2.next = ListNode(head.val)
                l2 = l2.next
            head = head.next
        l1.next = B.next
        
        return A.next
```
------

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
------

#### 143. Reorder List

>Given a singly linked list L: L0→L1→…→Ln-1→Ln,
reorder it to: L0→Ln→L1→Ln-1→L2→Ln-2→…
You may not modify the values in the list's nodes, only nodes itself may be changed.

思路应该是先用快慢指针找到指针的[中点](#876-Middle-of-the-Linked-List)，然后把后半个指针[翻转](#206-Reverse-Linked-List)过来，最后合并两个指针，每一步都是 **知识点** ⊂[┐'_'┌]⊃

合并思路是：

* 先把 A 的下一位保存，然后指向 B，指针下移一位
* 当前指针的下一位指向上一步保存的位置
* 指针下移一位，链表二指针也下移一位 

```python
class Solution:
    def reorderList(self, head: ListNode) -> None:
        """
        Do not return anything, modify head in-place instead.
        """
        if not head or not head.next:
            return
        
        a, b = self.splitList(head)
        b = self.reverseList(b)
        head = self.mergeLists(a, b)
        
    def splitList(self, head: ListNode) -> None:
        fast = slow = head
        while fast and fast.next:
            fast = fast.next.next
            slow = slow.next
        mid = slow.next
        slow.next = None
        return head,mid

    def reverseList(self, head: ListNode) -> None:
        l1 = ListNode(0)
        l1 = l1.next
        while head:
            l2 = head.next
            head.next = l1
            l1 = head
            head = l2
        return l1

    def mergeLists(self, l1: ListNode, l2: ListNode) -> None:
        head = l1
        tail = l1

        while l2:
            A = tail.next
            tail.next = l2
            tail = tail.next
            l2 = l2.next
            tail.next = A
            tail = tail.next
        return head
```


------

#### 160. Intersection of Two Linked Lists

>Write a program to find the node at which the intersection of two singly linked lists begins.

```python
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

------

#### 206. Reverse Linked List

>Reverse a singly linked list.

* 保存当前头结点的下个节点。
* 将当前头结点的下一个节点指向“上一个节点”，这一步是实现了反转。
* 将当前头结点设置为“上一个节点”。
* 将保存的下一个节点设置为头结点。

做这种题总感觉赋来赋去的乱的很，，

```python
class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        l1 = ListNode(0)
        l1 = l1.next
        while head:
            l2 = head.next
            head.next = l1
            l1 = head
            head = l2
        return l1
```

------

#### 328. Odd Even Linked List

> Given a singly linked list, group all odd nodes together followed by the even nodes. Please note here we are talking about the node number and not the value in the nodes.

* 先初始化两个小狗并保存，它们的 `next` 分别指向 `head` 和 `head.next`，然后让小狗各自站到自己的 `next` 上（此时两只小狗是错位的）
* 检查 `even` 小狗是不是 `None`，是的话就赋给 `head` 结束循环，不是的话就让 `head` 指针向后两步（开始检查循环）
* 循环结束之后，令 `odd` 小狗的 `next` 指向初始化时保存的 `even` 小狗的 `next`
* 返回 `even` 小狗的备份的 `next`

```python
class Solution:
    def oddEvenList(self, head: ListNode) -> ListNode:
        l1 = odd = ListNode(0)
        l2 = even = ListNode(0)
        while head:
            odd.next = head
            even.next = head.next
            odd = odd.next
            even = even.next

            head = head.next.next if even else None
        odd.next = l2.next
        return l1.next
```

------

#### 876. Middle of the Linked List

> Given a non-empty, singly linked list with head node head, return a middle node of linked list. If there are two middle nodes, return the second middle node.

两种思路，第二种实现仅仅需要五行。最近做题发现，很多时候解决一个问题，思路真的很重要。很多时候自己那些想法是需要被推翻和颠覆的。

第二种方法的思路是同时跑两个小狗，一个一次跑一步，另一个跑两步，关键在于设置停止的条件：当跑得快的小狗首先跑到结尾时有两种情况，一种是自己站在 `None` 处，另一种是它的下一步 `next` 指向了 `None` 。这两个条件的任何一个出现时，就去把跑得慢的小狗的位置返回去 -=≡Σ(((つ•̀ω•́)つ...

<!--more-->

##### 方法1

```python
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