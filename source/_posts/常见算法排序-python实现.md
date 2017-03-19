---
title: 常见算法排序-python实现
date: 2017-03-04 15:43:45
tags: [python,排序,算法]
categories: [学习,程序员,数据结构]
---

**1.二分法**
```python
#coding=utf-8
def binary_search(input_array, value):
    """Your code goes here."""
    length = len(input_array)
    left = 0
    right = length-1
    if length == 1:
        return 0 if value == input_value[0] else -1
    else:
        mid = (left+right)/2
        while(right-left>1):
            if input_array[mid] == value:
                return mid
            elif input_array[mid] > value:
                right = mid
                mid = (left+right)/2
            else:
                left = mid
                mid = (left+right)/2
        if input_array[left] == value:
            return left
        elif input_array[right] == value:
            return right
        else:
            return -1

test_list = [1,3,9,11,15,19,29]
test_val1 = 25
test_val2 = 15
print (binary_search(test_list, test_val1))
print (binary_search(test_list, test_val2))
```

<!--more-->
**2.冒泡法**
```python
#coding=utf-8

# way=1递增排序 way=0递减排序
def bubble_sort(array,way=1):
    length = len(array)

    if not length:
        print("Error!The length of array must be greater than 0.\n")
        return 'Wrong array' 

    if way == 1:
        while length > 0:
            for i in range(length-1):
                if array[i] > array[i+1]:
                    array[i],array[i+1] = array[i+1],array[i]
            length -= 1
        return array

    elif way == 0:
        while length > 0:
            for i in range(length-1):
                if array[i] < array[i+1]:
                    array[i],array[i+1] = array[i+1],array[i]
            length -= 1
        return array

# 加入排序判断标志，可提高运行效率
# way=1递增排序 way=0递减排序
def better_bubble_sort(array,way=1):
    is_sorted = True    # 判断记录上次遍历是否进行过交换，若没有则表示不用再遍历了
    length = len(array)

    if not length:
        print("Error!The length of array must be greater than 0.\n")
        return 'Wrong array' 

    if way == 1:
        while length > 0 and is_sorted:
            for i in range(length-1):
                is_sorted = False
                if array[i] > array[i+1]:
                    array[i],array[i+1] = array[i+1],array[i]
                    is_sorted = True
            length -= 1
        return array

    elif way == 0:
        while length > 0 and is_sorted:
            for i in range(length-1):
                is_sorted = False
                if array[i] < array[i+1]:
                    array[i],array[i+1] = array[i+1],array[i]
                    is_sorted = True
            length -= 1
        return array


test =  [21, 4, 1, 3, 9, 20, 25, 6, 21, 14] 
print(better_bubble_sort(test,1))
```

**3.插入排序**
```python
#coding=utf-8

def insert_sort(array):
    length = len(array)
    flag = array[0]
    for x in range(1,length):
        # 之前的
        if array[x] < array[x-1]:
            flag = array[x]
            y = x
            while y != 0 and array[y-1] > flag :
                array[y] = array[y-1]
                y -= 1
            array[y] = flag
    return array

test = [21, 4, 1, 3, 9, 20, 25, 20, 3]
print(insert_sort(test))
```

**4.归并排序**
```python
#coding=utf-8

def merge_sort(array):
    if len(array) <= 1:
        return array

    split_index = len(array)/2
    left = merge_sort(array[:split_index])
    right = merge_sort(array[split_index:])
    return merge(left,right)

def merge(left,right):
    i = 0
    j = 0
    result = []
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1

    result += (left[i:])
    result += (right[j:])
    return result

a = [1,2]
test = [21, 4, 1, 3, 9, 20, 25] 
print(merge_sort(test))
```

**5.选择排序**
```python
#coding=utf-8

def select_sort(array):
    length = len(array)
    # mini = array[0]
    for i in range(length):
        mini = array[i]
        for j in range(i,length):
            if array[j] < mini:
                mini = array[j]
                array[i],array[j] = array[j],array[i]
    return array

test = [21, 4, 1, 3, 9, 20, 25, 20, 3] 
print(select_sort(test))
```

**6.快速排序**
```python
#coding=utf-8

# 递归
def quick_sort(lists, left, right):
    # 快速排序
    if left >= right:
        return lists
    key = lists[left]
    low = left
    high = right
    while left < right:
        while left < right and lists[right] >= key:
            right -= 1
        lists[left] = lists[right]
        while left < right and lists[left] <= key:
            left += 1
        lists[right] = lists[left]
    lists[right] = key
    quick_sort(lists, low, left - 1)
    quick_sort(lists, left + 1, high)
    return lists


test = [21, 4, 1, 3, 9, 20, 25, 6, 21, 14]
print (quick_sort(test,0,len(test)-1))
```
<br><br><hr>
<footer style="padding:10px;border-radius:10px;;text-align:center;background-color:rgb(11,12,13);color:white;">
written by <b style="color:tomato;font-size:16px;">MARSGGBO</b>
<br><span style="font-size:16px;">
2017-2-14</span>
</footer>