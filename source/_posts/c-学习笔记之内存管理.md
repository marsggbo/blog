---
title: c++学习笔记之内存管理
date: 2017-03-12 18:58:44
tags: [C++,指针,引用,const]
categories: [学习,程序员,c/c++]
---

### **一、const常量**

![const使用方法][1]
如图所示，const有两种使用情况，总结起来就是const离谁近，就作用于谁，具体看下面示例：

- **const int * p**
const作用于*p，所以 *p不能变化，但是p可以重新赋值

![const int * p][2]

- **int * const p**
const作用于p，所以p不能变化

![int * const p][3]

<!--more-->

- **const int * const p**
这种情况下，p和*p都能不重新赋值。

![enter description here][4]

为更好理解，下面给出一道例题：

![enter description here][5]

![enter description here][6]

### **二、内存管理**
- **c与c++的区别**

![c和c++的区别][7]

- **单个变量内存的申请和释放**

![enter description here][8]

- **块内存的申请和释放**

![enter description here][9]

- **注意事项**

1.在申请完内存后，不仅需要注意释放内存，另外还需要注意判断内存**是否成功申请**,另外如果没有申请成功，仍然需要对指针**做释放操作和重定向为NULL**.
```cpp
// 申请内存
int *arr = new int[1000];

// 判断申请成功与否
if(arr==NULL) // 申请失败
{
  cout<<"内存申请失败"<<endl;
  // 异常处理
}

// 释放指针
delete []arr;
// 重定向
arr = NULL;
```

2.new运算符申请的内存是在堆中申请的。




<br><br><hr>
<footer style="padding:10px;border-radius:10px;;text-align:center;background-color:rgb(11,12,13);color:white;">
written by <b style="color:tomato;font-size:16px;">MARSGGBO</b>
<br><span style="font-size:16px;">
2017-3-11</span>
</footer>


  [1]: http://wx3.sinaimg.cn/large/83e758c9ly1fdjv2z28hbj20nj0d8gmq.jpg "const使用方法"
  [2]: http://wx3.sinaimg.cn/large/83e758c9ly1fdjv4z3x01j20ni0dcmyq.jpg "const int * p"
  [3]: http://wx2.sinaimg.cn/large/83e758c9ly1fdjv6aot9bj20nj0d9gn4.jpg "int * const p"
  [4]: http://wx4.sinaimg.cn/large/83e758c9ly1fdjv820llaj20ni0dcjt7.jpg "5.png"
  [5]: http://wx4.sinaimg.cn/large/83e758c9ly1fdjv99wfy3j20xf0ev3yt.jpg "xiti.png"
  [6]: http://wx2.sinaimg.cn/large/83e758c9ly1fdjv9l2g85j20lp08yglq.jpg "dana.png"
  [7]: http://wx4.sinaimg.cn/large/83e758c9ly1fdjvdgqidgj20ng0d7q4i.jpg "c和c++的区别.png"
  [8]: http://wx1.sinaimg.cn/large/83e758c9ly1fdjvenv36pj20ng0d4tae.jpg "1.png"
  [9]: http://wx2.sinaimg.cn/large/83e758c9ly1fdjvf1qvt3j20nh0d5gnk.jpg "快内存.png"