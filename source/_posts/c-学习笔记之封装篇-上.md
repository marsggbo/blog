---
title: c++学习笔记之封装篇(上)
date: 2017-03-12 18:59:01
tags: [c++,c,封装,类]
categories: [学习,程序员,c/c++]
---

### **一、类对象**
假设我们由**Tv**这个类，定义如下
> 注意class结尾要加上分号

```cpp
class Tv()
{
    int width;
    int height;
    int volume;
    void changeVolume()
    {
        //changeVolume
    }
};

```
类的实例化有两种方法，一种是**栈中实例化**。另一种则是**堆中实例化**。

而这两种方法除了在**用法上**有区别以外(区别见下面代码)，**内存管理**也有区别(栈不用考虑内存回收问题，系统会自动回收，而堆则需要程序员主动释放内存)。

<!--more-->
#### **1.实例化**
- 从栈中实例化对象
```cpp
Tv tv;

或者 

Tv tv[20]  //实例化对象数组     从栈中实例化系统自动回收内存
```
- 从堆中实例化对象 
**实例化一个对象后还需要判断是否成功申请到内存**
```cpp
Tv *p = new Tv();    
Tv *q = new Tv[20];  //开辟一块对象内存  

if(p==NULL)
{
    cout<<"类实例化失败"<<endl;
}

//从堆中实例化数组对象，需要程序员回收
delete p;   
delete[]q; 
p = NULL;
q =NULL;
```

#### **访问数据**
- 通过栈实例化的对象访问数据成员： 
```cpp
tv.volume = 5;   
tv.changeVolume();
```
- 通过堆实例化的对象访问数据成员: 
```cpp
tv->volume = 5; 
tv->changeVolume();
```

### **二、string**
可以很方便的定义一个字符串，而不再是使用麻烦的char[].
使用方法：
```cpp
// 引入string头文件
#include <string>
```
#### **初始化方法**
> 注意:第4个初始化方法中的第二个参数只能是一个字符，不能是字符串。

![enter description here][1]

#### **常用操作**

![enter description here][2]

对于字符串相加操作，需要注意一点，不能直接使用两个字符串相加，中间必须得有字符串变量连接才行(下面例子中最后一个赋值语句是错误的)，具体例子如下：

![enter description here][3]

另外如果要将字符串和整数连在一起输出，则需要在整数前加上 **<<** 进行处理，直接用 “**+**” 是无效的。
```cpp
string name;
cin>>name;
cout<<name<<name.size()<<endl;
```

### **三、构造函数**
> - 构造函数必须与类同名，起到的作用与python中的 **_ _init_ > _()** 函数类似,即对类进行数据初始化。
> - 构造函数在对象实例化时被自动调用，且只调用一次。
> - 构造函数没有返回值，不需要return值。
> - 构造函数可以重载。

#### **1. 构造函数分为有参与无参**
举个栗子：
```cpp
class Student
{
    // 无参构造函数
    Student()
    {
        m_iHeight = 180;
        m_iWeight = 70;
    }
    
    // 有参构造函数
    Student(int weight, int height)
    {   
        m_iWeight = weight;
        m_iHeight = height;
    }
    
    private:
        int m_iWeight;
        int m_iHeight;
}
```

#### **2. 构造函数初始化列表**
> 在初始化对象时，**初始化列表是最先执行的**，所以有的时候对于const变量我们也可以提前赋值。

语法：
**类名():变量1(参数),变量2(参数){}**


用法：
我们都知道一个人的身份证信息一般来说是不能随意篡改的，所以我们会使用**const** 来修饰，但是因为const修饰后，变量则不能再赋值，但是因为每个人的身份证又不一样，我们不可能在使用const修饰的同时进行赋值，那怎么办呢？所以这里用到了初始化列表来达到这么一个效果，详见下面的代码示例：
```cpp
class Student
{
    // 正确示例
    // 初始化列表
    Student(string id):m_iId(id)
    {
        // 初始化成功
    }
    
    // 编译会报错
    // Student(string id)
    // {
    //    m_iId = id;
    // }
    
    private:
      const string m_iId;
}
```

初始化类数组
```cpp
Coordinate *coorArr = new Coordinate[2]{{1,2},{3,4}};
``` 

具体示例
```cpp
#include <iostream>
using namespace std;
class Coordinate
{
    
    public:
        Coordinate(int x,int y):m_iX(x),m_iY(y)
        {
            cout<<m_iX<<","<<m_iY<<endl;
        }
        // 打印坐标的函数
        void printInfo()  
        {
            cout<<m_iX<<","<<m_iY<<endl;
        }
    private:
        int m_iX;
        int m_iY;
};
int main(void)
{
    //定义对象数组
    Coordinate *coorArr = new Coordinate[2]{{1,2},{3,4}};
    
    
    //遍历数组，打印对象信息
    for(int i = 0; i < 2; i++)
    {
        coorArr[i].printInfo();
    }   
    return 0;
}
```

#### **3. 拷贝构造函数**
- **不能重载**
格式：
**类名(const 类名& 变量名)**

关于拷贝构造函数更具体的解析参考：
[C++拷贝构造函数详解][4]


#### **4.析构函数**
与构造函数相反，当一个对象的生命周期结束时，C++会自动调用一个特殊的成员函数，即析构函数进行善后工作，对成员变量所占内存进行释放操作。
格式：(不需要参数)
**~类名(){}**
```cpp
class Student
{
    // 构造函数，初始化列表
    Student():m_iId(110){};
    // 析构函数，c++可以自动生成
    ~Student(){};
}
```


对象的生命历程：
**申请内存**-->**初始化列表**-->**构造函数**-->**参与运算**-->**析构函数**-->**释放内存**


![enter description here][5]
<br><br><hr>
<footer style="padding:10px;border-radius:10px;;text-align:center;background-color:rgb(11,12,13);color:white;">
written by <b style="color:tomato;font-size:16px;">MARSGGBO</b>
<br><span style="font-size:16px;">
2017-3-11</span>
</footer>


  [1]: http://wx2.sinaimg.cn/large/83e758c9ly1fdjy5scbdsj20ni0d8775.jpg "string初始化.png"
  [2]: http://wx4.sinaimg.cn/large/83e758c9ly1fdjym62q7qj20nn0d7q7m.jpg "操作.png"
  [3]: http://wx3.sinaimg.cn/large/83e758c9ly1fdjyoocr8gj20nl0d7jtg.jpg "！.png"
  [4]: http://blog.csdn.net/lwbeyond/article/details/6202256
  [5]: http://wx4.sinaimg.cn/large/83e758c9ly1fdk2g7sbwej20nl0d2gnv.jpg "2.png
  "