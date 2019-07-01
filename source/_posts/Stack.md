---
layout:     post
title:      Stack
category:   LeetCode
tags: Stack
---

**汇总栈的题目**

```python
# Definition for a binary tree node.
class TreeNode:
    def __init__(self, x):
        self.stack = []
```

<!--more-->

#### 20. 有效的括号

> 给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。
有效字符串需满足：
- 左括号必须用相同类型的右括号闭合。
- 左括号必须以正确的顺序闭合。
- 注意空字符串可被认为是有效字符串。

```python
class Solution(object):
    def isValid(self, s):
        stack = []
        mapping = {")": "(", "}": "{", "]": "["}
        for char in s:
            if char in mapping:
                top_element = stack.pop() if stack else '#'
                if mapping[char] != top_element:
                    return False
            else:
                stack.append(char)
        return not stack
```

------

#### 71. 简化路径

> 以 Unix 风格给出一个文件的绝对路径，你需要简化它。

```python
class Solution:
    def simplifyPath(self, path: str) -> str:
        string = path.split('/')
        print(string)
        res = []
        for s in string:
            if s == '..':
                if res:
                    res.pop()
            elif s == '.' or s == '':
                continue
            else:
                res.append('/'+s)
        return "".join(res) if res else "/"
```

------

#### 155. 最小栈

> 设计一个支持 push，pop，top 操作，并能在常数时间内检索到最小元素的栈。
- push(x) -- 将元素 x 推入栈中。
- pop() -- 删除栈顶的元素。
- top() -- 获取栈顶元素。
- getMin() -- 检索栈中的最小元素。

以空间换时间 ヽ(ー_ー)ノ

```python
class MinStack:

    def __init__(self):
        """
        initialize your data structure here.
        """
        self.stack = []
        self.min_stack = []
        self.min = None

    def push(self, x: int) -> None:
        self.stack.append(x)
        print(self.min)
        if self.min is None or self.min >= x:
            self.min = x
            self.min_stack.append(x)

    def pop(self) -> None:
        if self.stack:
            if self.stack[-1] == self.min_stack[-1]:
                self.min_stack.pop()
                if not self.min_stack:
                    self.min = None
                else:
                    self.min = self.min_stack[-1]
            return self.stack.pop()
        else:
            return False

    def top(self) -> int:
        if self.stack:
            return self.stack[-1]
        else:
            return False

    def getMin(self) -> int:

        if not self.stack:
            return False
        else:
            return self.min_stack[-1]
```

---

#### 682. 棒球比赛

> 你现在是棒球比赛记录员。
给定一个字符串列表，每个字符串可以是以下四种类型之一：
1. 整数（一轮的得分）：直接表示您在本轮中获得的积分数。
2. "+"（一轮的得分）：表示本轮获得的得分是前两轮有效 回合得分的总和。
3. "D"（一轮的得分）：表示本轮获得的得分是前一轮有效 回合得分的两倍。
4. "C"（一个操作，这不是一个回合的分数）：表示您获得的最后一个有效 回合的分数是无效的，应该被移除。

```python
class Solution:
    def calPoints(self, ops: List[str]) -> int:
        stack = []
        sumup = 0
        
        for i in ops:
            
            if i == 'C':
                o, p = stack.pop()
                sumup -= o
            elif i == 'D':
                this = 2 * stack[-1][0]
                sumup += this
                stack.append([this,sumup])
            elif i == '+':
                this = stack[-1][0] + stack[-2][0]
                sumup += this
                stack.append([this,sumup])
            else:
                sumup += int(i)
                stack.append([int(i), sumup])
                
            #print(sumup,'\t',stack)
        
        return stack[-1][1]
```

---

#### 739. 每日温度

> 根据每日 气温 列表，请重新生成一个列表，对应位置的输入是你需要再等待多久温度才会升高超过该日的天数。如果之后都不会升高，请在该位置用 0 来代替

```python
class Solution:
    def dailyTemperatures(self, T: List[int]) -> List[int]:
        
        stack= []
        res = [0] * len(T)
        
        for i, t in enumerate(T):
            while stack and stack[-1][0] < t:
                pos = stack.pop()[1]
                res[pos] = i-pos
            stack.append([t,i])
            print(stack[-1])
        
        return res
```

---

#### 1021. 删除最外面的括号

> 删除每个部分中的最外层括号
示例：
输入："(()())(())(()(()))"
输出："()()()()(())"

```python
class Solution:
    def removeOuterParentheses(self, S: str) -> str:
        res = ''
        count = 1
        i = 1
        while i < len(S):
            if S[i] == "(":
                count += 1   
            else:
                count -= 1
            if count == 0:
                i += 2
                count = 1
                continue
            res += S[i]
            i += 1
        return res
```