---
title: 剑指Offer_编程题大集结
date: 2017-01-20 21:46:23
tags: [python,数据结构]
categories: [学习,程序员,数据结构]
---

## **剑指Offer_编程题大集结**

以下题目摘自牛客网，仅供学习，欢迎大家互相交流学习。

### **1、 二维数组中的查找**
>在一个二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

<!--more-->

```python
# -*- coding:utf-8 -*-
class Solution:
# array 二维列表
def Find(self, target, array):
    # write code here
    flag = False
    for item in array:
        if target in item:
        flag = True
        break
    return flag
```

### **2、替换空格**
> 请实现一个函数，将一个字符串中的空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。

```python
# -*- coding:utf-8 -*-
class Solution:
# s 源字符串
    def replaceSpace(self, s):
        # write code here
        return s.replace(' ','%20')
```

### **3、从尾到头打印链表**
>输入一个链表，从尾到头打印链表每个节点的值。

```python
# -*- coding:utf-8 -*-
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    # 返回从尾部到头部的列表值序列，例如[1,2,3]
    def printListFromTailToHead(self, listNode):
        # write code here
        box = []
        while listNode:
            box.insert(0,listNode.val)
            listNode = listNode.next
        return box
```

### **4、重建二叉树**
>输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。

```python
# -*- coding:utf-8 -*-
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
class Solution:
    # 返回构造的TreeNode根节点
    def reConstructBinaryTree(self, pre, tin):
    # write code here
    if len(pre):
        root = TreeNode(pre[0])
        # 找到根节点在中序遍历序列中的位置
        rootIndex = tin.index(pre[0])
        root.left = self.reConstructBinaryTree(pre[1:rootIndex+1],tin[:rootIndex])
        root.right = self.reConstructBinaryTree(pre[rootIndex+1:],tin[rootIndex+1:])
        return root
    else:
        return None
```

### **5、用两个栈实现队列**
>用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型。

```python
# -*- coding:utf-8 -*-
class Solution:
    def __init__(self):
        self.stack1 = []
        self.stack2 = []
    def push(self, node):
        # write code here
        self.stack1.append(node)
    def pop(self):
        # return xx
        if len(self.stack2):
            return self.stack2.pop()
        else:
            while len(self.stack1) != 0:
                self.stack2.append(self.stack1.pop())
            return self.stack2.pop()
```

### **6、旋转数组的最小数字**
> 把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。
输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。
例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。
NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

<b style="color:tomato;">考察二分法</b>

```python
# -*- coding:utf-8 -*-
class Solution:
    def minNumberInRotateArray(self, rotateArray):
        # write code here
        if len(rotateArray) == 0:
            return 0
        else:
            left = 0
            right = len(rotateArray) - 1
            mid = int(right/2)
        # 没有旋转的情况
        if rotateArray[left] < rotateArray[right]:
            return rotateArray[left]
        # 旋转的情况
        else:
            return self.dichotomization(rotateArray,left,mid,right)
        
        def dichotomization(self,rotateArray,left,mid,right):
        # 递归结束条件：当右指针和左指针只相差一个位置，然后判断大小即可。
        # 其实也不用判断了，在该段代码中可已直接判断出最小值为右指针所指的值，
        # 因为若果调用了该递归函数，则表明是一个非递减数列的旋转数列
        # 所以可以很容易的推测出当右指针和左指针只相差一个位置，最小值一定是右指针所指示的值
        if right - left == 1 :
            if rotateArray[right] < rotateArray[left]:
                return rotateArray[right]
            else:
                return rotateArray[left]
        else:
        # 递归法只适用于顺序排列而且没有重复数字的序列，如果有重复出现的数字则无法判断区间，所以只能遍历查找
            if rotateArray[mid] == rotateArray[right]:
                return self.ergodic(rotateArray)
            if rotateArray[mid] < rotateArray[right]:
                right = mid
                left = left
                mid = int((left+right+1)/2)
                return self.dichotomization(rotateArray,left,mid,right)
            else:
                left = mid
                right = right
                mid = int((left+right+1)/2)
                return self.dichotomization(rotateArray,left,mid,right)
        
        def ergodic(self,rotateArray):
            if len(rotateArray) == 1:
                return rotateArray[0]
            else:
                for i in range(len(rotateArray)):
                    if rotateArray[i] > rotateArray[i+1]:
                        break
                return rotateArray[i+1]

# 最简单的方法
# class Solution:
# def minNumberInRotateArray(self, rotateArray):
# # write code here
# if len(rotateArray) == 0:
# return 0
# else:
# return min(rotateArray)
```

### **7、斐波那契数列**
>大家都知道斐波那契数列，现在要求输入一个整数n，请你输出斐波那契数列的第n项。
n<=39

```python
# -*- coding:utf-8 -*-
class Solution:
    # # 递归法适用于n比较小的情况
    # def Fibonacci(self, n):
        # # write code here
            # if n <= 2:
                # return 1
            # else:
                # return self.Fibonacci(n-1) + self.Fibonacci(n-2)

    # 迭代法
    def Fibonacci(self, n):
        # write code here
        if n < 0:
            return -1
        elif n == 0:
            return 0
        elif n <= 2:
            return 1
        else:
            a = 1
            b = 1
            while n > 2:
                a,b = b,a+b
                n -= 1
            return b
```

### **8、跳台阶**
>一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

**经过简单写出几种当n比较小的情况的结果，可以看出结果一个斐波拉契数列**

```python
# -*- coding:utf-8 -*-
class Solution:
    def jumpFloor(self, number):
        if number < 1:
            return -1
        elif number == 1:
            return 1
        else:
            a = 1
            b = 1
            while number > 1:
                a,b = b,a+b
                number -= 1
            return b
```
