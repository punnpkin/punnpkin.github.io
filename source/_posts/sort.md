---
layout:     post
title:      排序算法的总结 
category:   Algorithm
tags: Sort
---

**总结一下排序的算法**

#### 选择排序

思路： 找出数组中最大的元素和下标，将下标对应的元素从旧数组中取出依次存入新的数组中

复杂度 $O(n^2)$

```python

def findMaxNum(arr):
    maxNum = arr[0]
    maxNumIndex = 0
    for i in range(1, len(arr)):
        if arr[i] > maxNum:
            maxNum = arr[i]
            maxNumIndex = i
    return maxNumIndex

def sort(arr):
    newArr = []
    for _ in range(len(arr)):
        maxIndex = findMaxNum(arr)
        newArr.append(arr.pop(maxIndex)) 
    return newArr

print(sort([5,3,6,2,10]))
```

<!--more-->

#### 快排

快排是一种使用分治策略的算法，分治思想又源于递归，于是先复习一点递归：

分治思想解决问题过程包括两个步骤：

1. 找出基线条件
2. 不断的将问题的规模进行分解

一个利用递归做列表求和的例子：

```python
def sum(arr):
    if len(arr) == 1:
        return arr[0]
    else:
        return arr[0] + sum(arr[1:])

print(sum([1,2,3,4,5]))
```

接下来开始快排：

```python
def quick_sort(arr):
    if len(arr) < 2:
        return arr
    else:
        pivot = arr[0]
        less = [i for i in arr[1:] if i <= pivot]
        greater = [i for i in arr[1:] if i > pivot]
        return quick_sort(less) + [pivot] + quick_sort(greater)

print(quick_sort([6, 8, 1, 4, 3, 5, 2, 0, 12, 22]))
```

只用了 6 行，python 大法好呀，


#### 归并

归并法也是分治思想的典型应用：将有序的子序列合并，得到完全有序的序列。

思路：充分利用 分治 的思想，先分后治，我觉得递归的思想适合人类的正常思维不太一样，所以有时候不要太抠细节。

想好两个条件：

1. 基线条件：列表的长度等于 1 ，即长度为 1 的列表不要再次排序。
2. 递归条件：不停的分解列表直至满足基线条件。

治 的过程只需要考虑对两个有序数组进行排序。

```python
def divide(arr):
    if len(arr) == 1:
        return arr
    mid = len(arr) // 2
    
    right = arr[:mid]
    left = arr[mid:]

    r = divide(right)
    l = divide(left)

    return sort(r,l)

def sort(right, left):
    result = []
    while len(right) > 0 and len(left) >0:
        if right[0] <= left[0]:
            result.append(right.pop(0))
        else:
            result.append(left.pop(0))
    result += right
    result += left

    return result

print(divide([6, 8, 1, 4, 3, 5, 2, 0, 12, 22]))

```
