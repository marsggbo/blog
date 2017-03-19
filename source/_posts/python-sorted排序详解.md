---
title: python-sorted排序详解
date: 2017-03-04 15:45:23
tags: [python,sorted,排序]
categories: [学习,程序员,python]
---


**1. operator函数**
在介绍sorted函数之前需要了解一下operator函数。

operator函数是python的内置函数，提供了一系列常用的函数操作
比如，operator.mul(x, y)等于x+y 
```python
a = [1,2,3]
b = [5,6,7]
c = map(operator.mul, a, b)
# c的值就为[5, 12, 21]
```
这里只介绍它的**itemgetter()** 方法，更多的介绍可参考官网。
[operator — Standard operators as functions][1]

<!--more-->

operator模块提供的itemgetter函数用于获取对象的哪些维的数据，参数为一些序号（即需要获取的数据在对象中的序号），下面看例子。
```bash
a = [1,2,3] 
>>> b=operator.itemgetter(1)      //定义函数b，获取对象的第1个域的值
>>> b(a) 
2 
>>> b=operator.itemgetter(1,0)  //定义函数b，获取对象的第1个域和第0个的值
>>> b(a) 
(2, 1)
```


**2. sorted函数**
函数介绍：[Built-in Function][2]
官网示例：[Sorting HOW TO¶][3]
函数原型：**sorted(iterable[, cmp[, key[, reverse]]])**

>Return a new sorted list from the items in iterable.(会返回一个重新排列好的list)
**iterable(可迭代)**:可以是list也可以是dict
**cmp**:可以自定义比较规则，这里不详细叙述
**key**:用于比较的值
**reverse**:指定是顺序还是逆序

下面以dict排序为例做讲解：
```python
c =  {'a': 15, 'ab': 6, 'bc': 16, 'da': 95}

d = sorted(c.iteritems(),key=operator.itemgetter(0),reverse=True)
>>> [('da', 95), ('bc', 16), ('ab', 6), ('a', 15)]

e = sorted(c.iteritems(),key=operator.itemgetter(0),reverse=True)
>>>['da', 'bc', 'a', 'ab']
```
1.注意区分dict加与不加**iteritems()** 对于结果的影响
2.我们的key选择的是传入参数的第0号元素，在这里即是键(keys),所以最终的排序是按照键排序，我们也可以以值作为标准进行排序，看下面示例
```python
d = sorted(c.iteritems(),key=operator.itemgetter(1),reverse=True)
>>> [('da', 95), ('bc', 16), ('a', 15), ('ab', 6)]
```

看到这你会不会觉得operator的itemgetter函数可以用lambda函数实现：我们可以将上面的示例改成lambda
```python
d = sorted(c.iteritems(),key=lambda x:x[1],reverse=True)
>>> [('da', 95), ('bc', 16), ('a', 15), ('ab', 6)]
```
你看结果是一样一样的。

文章参考：
[Python中的sorted函数以及operator.itemgetter函数][4]
[python中的operator库][5]

<br><br><hr>
<footer style="padding:10px;border-radius:10px;;text-align:center;background-color:rgb(11,12,13);color:white;">
written by <b style="color:tomato;font-size:16px;">MARSGGBO</b>
<br><span style="font-size:16px;">
2017-2-14</span>
</footer>


  [1]: https://docs.python.org/3/library/operator.html
  [2]: https://docs.python.org/2/library/functions.html?highlight=sorted#sorted
  [3]: https://docs.python.org/2/howto/sorting.html#sortinghowto
  [4]: http://www.cnblogs.com/100thMountain/p/4719503.html
  [5]: http://blog.csdn.net/lindaydk/article/details/6314444