---
title: python多线程学习笔记-超详细
date: 2016-12-03 09:26:20
tags: [python,多线程,Threading]
---


一. Threading简介

首先看下面的没有用Threading的程序

```python
import threading,time

def fun():
    s = 0
    for i in range(30):
        s += i
        time.sleep(0.1)
    print(s)

if __name__ == '__main__':
    t = time.time()
    fun()
    fun()
    print(time.time()-t)
    
>>> 
435
435
6.023701906204224
[Finished in 6.6s]
```

如果使用线程会有什么样的效果呢

<!--more-->

```python
import threading,time

def fun():
    s = 0
    for i in range(30):
        s += i
        time.sleep(0.1)
    print(s)

if __name__ == '__main__':
    # 创建了一个线程列表，包含2个线程
    ths = [threading.Thread(target=fun) for i in range(2)]
    for th in ths:
        th.start()
    t = time.time()
    for th in ths:
        th.join()
    print(time.time()-t)
  
  
>>>  
435
435
3.116874933242798
[Finished in 3.7s]
```
这说明两个线程几乎是同时进行的


----------
二. Threading的应用进阶

1) **join(timeout)用来实现线程等待。**
被调用join()方法的线程会一直阻塞调用者的线程，
直到自己结束（正常结束，或引发未处理异常），
或超出timeout的时间。
```python
import threading,time

class MyThread(threading.Thread):

    def run(self):
        for i in range(30):
            print('threading:',i)
            time.sleep(0.1)

if __name__ == '__main__':
    t = MyThread()
    t.start()
    t.join(1)
    for i in range(10):
        print('Main:',i)
        time.sleep(0.1)
        
>>> 
threading: 0
threading: 1
threading: 2
主线程等待t这个线程0.1秒后也开始运行
Main: 0
threading: 3
Main: 1
threading: 4
Main: 2
threading: 5
Main: 3
threading: 6
Main: 4
threading: 7
Main: 5
threading: 8
Main: 6
threading: 9
Main: 7
Main: 8
Main: 9
[Finished in 2.0s]
```
注意每次运行的结果都不太一样

2)**daemon属性**
被设定为后台运行的线程，会在主程序退出时主动自杀。
设置为后台运行线程的方法是：==设置线程的daemon属性为True==
```python
import threading,time

def dmn():
    print('dmn start...')
    time.sleep(2)
    print('dmn end.')

def ndmn():
    print('ndmn start...')
    time.sleep(1)
    print('ndmn end.')

d = threading.Thread(target=dmn)
d.daemon = True
n = threading.Thread(target=ndmn)
print('start...')
d.start()
n.start()
print('end.')

>>>
start...
dmn start...
ndmn start...
end.
ndmn end.
[Finished in 1.3s]
```
由上面打印的结果我们可以看到dmn线程设置为后台线程后，它的 **print('dmn end.')** 语句并不没有执行，这是因为后台线程在主线程结束后会自杀，所以主线程执行完后，dmn线程没能说出自己的“遗言”。
作为对比，我将==daemon==设为False，结果如下
```python
...

d = threading.Thread(target=dmn) 
d.daemon = False 
...

>>>
start...
dmn start...
ndmn start...
end.
ndmn end.
dmn end.
[Finished in 2.5s]
```

3) 线程同步
1 )指令锁  **threading.Lock** 

acquire尝试获得锁定，进入阻塞状态。
acquire(blocking=True, timeout=－1)) 

release释放获得锁定（资源使用完后）
release() 
```python
import threading,time,random

share = 4 
lock = threading.Lock() #初始化指令锁

class MyThread(threading.Thread):
    def __init__(self,i):
        super().__init__()
        self.i = i

    def run(self):
        global share
        for d in range(2):
            lock.acquire()
            print(share)
            share += self.i
            time.sleep(random.random())
            print('+',self.i,'=',share)
            lock.release()

if __name__ == '__main__':
    t = MyThread(2)
    tt = MyThread(6)
    t.start()
    tt.start()
    
>>>
4
+ 2 = 6
6
+ 6 = 12
12
+ 2 = 14
14
+ 6 = 20
[Finished in 2.9s]
```
为了更好的感受指令锁的作用，将acquire和release去掉后结果如下
```python
...
def run(self): 
        global share 
        for d in range(2): 
            # lock.acquire() 
            print(share) 
            share += self.i 
            time.sleep(random.random()) 
            print('+',self.i,'=',share) 
            # lock.release() 
...

>>>
4
6
+ 6 = 12
12
+ 2 = 18
18
+ 6 = 20
+ 2 = 20
[Finished in 2.2s]
```
比较后可以知道，加了指令锁后可以清楚地知道对共享资源share操作的具体情况

2 )条件变量**threading.Condition**
属性
- 实例化时，可指定锁。
- **acquire()**                       
- **release()**                       
- **wait(timeout=None)**
释放锁，进入等待阻塞，
直到唤醒或超时。
- **notify(n=1)** 
唤醒等待该条件变量的线程。默认1个。
- **notify_all()**
唤醒等待该条件变量的所有线程。

> 实现严格的依照次序操作的线程之间的通信。              
典型的实例：==**生产者/消费者**==（只有生产后，才能消费）。
线程之间可以互相通知，以达到默契的配合。                 
条件变量可以使用默认的锁或用户创建的锁来工作。 

话不多说看代码
```python
import threading,time

share = 0

share_cond = threading.Condition()

# 生产者
class ProThread(threading.Thread):
    def __init__(self):
        super().__init__()
        self.name = 'Produce'

    def run(self):
        global share
        if share_cond.acquire():
            while True:
                if not share:   # 若没东西了，即开始生产
                    share += 1
                    print(self.name,share)
                    share_cond.notify() #唤醒消费者？这个是我自己的理解 
                share_cond.wait()
                time.sleep(1)

# 消费者
class CustomThread(threading.Thread):
    def __init__(self):
        super().__init__()
        self.name = 'Custom'

    def run(self):
        global share
        if share_cond.acquire():
            while True:
                if share:
                    share -= 1  # 若有东西就买买买
                    print(self.name,share)
                    share_cond.notify() #唤醒生产者，同上，仅是个人理解，如有错请告知，谢谢
                share_cond.wait()
                time.sleep(1)

if __name__ == '__main__':
    t  = ProThread()
    tt = CustomThread()
    t.start()
    tt.start()

>>>
Produce 1
Custom 0
Produce 1
Custom 0
Produce 1
Custom 0
...
...
...
```
上面的结果会一直重复执行下去


3 ) 信号量**threading.Semaphore**

属性
- 实例化时，指定使用量。
- 其内置计数器，锁定时+1，
释放时－1，计数器为0则阻塞。
- acquire(blocking=True,timeout=None)
- release()释放锁。                      

```python
import threading,time

sema = threading.Semaphore(2)

class MyThread(threading.Thread):
    def __init__(self,name):
        super().__init__()
        self.name = name

    def run(self):
        if sema.acquire():
            print(self.name,'Had got resource.')
            time.sleep(1)
        sema.release()
        print(self.name,'Had released resource.')

if __name__ == '__main__':
    ths = [MyThread(str(i)+'Sema') for i in range(5)]
    for th in ths:
        th.start()
        
>>>
0Sema Had got resource.
1Sema Had got resource.
2Sema Had got resource.
1Sema Had released resource.
3Sema Had got resource.
0Sema Had released resource.
3Sema Had released resource.
4Sema Had got resource.
2Sema Had released resource.
4Sema Had released resource.
[Finished in 3.6s]
```

4 ) 线程通信**threading.Event**

- 其管理一个内部标志.实现一个线程，唤醒其它线程。
- **set()** 设置内部标志为True       
- **clear()** 设置内部标志为False   
- **wait(timeout)**
阻塞线程，到内部标志为True。

```python
import threading,time

event = threading.Event()

class MyThreadWait(threading.Thread):
    def run(self):
        self.name = 'Wait Thread'
        print(self.name,"Wait...")
        event.wait()
        print(self.name,"Start...")
        event.clear()

class MyThreadMain(threading.Thread):
    def run(self):
        time.sleep(3)
        print('Main thread set event flag!')
        event.set()

if __name__ == '__main__':
    thw = MyThreadWait()
    thm = MyThreadMain()
    thw.start()
    thm.start()
    
>>>
Wait Thread Wait...
Main thread set event flag!
Wait Thread Start...
[Finished in 3.6s]
```

好了，大概就是这些了，其他的以后再补充，另外感谢麦子学院提供的免费课程~~~真心不是打广告![哈哈][1]
为了避嫌，顺便感谢一下imooc，极客学院~很多都是从这些造福人类的网站学到的。
另附上麦子学院的视频教程，毕竟要学会感恩嘛
http://www.maiziedu.com/course/644-9663/


  [1]: http://p1.bqimg.com/4851/6d5ff0bccf698287.jpg 