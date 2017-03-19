---
title: Flask入门笔记(一)
date: 2017-01-12 15:53:47
tags: [python,flask,web框架,基本结构]
categories: [学习,程序员,python,flask]
---

**一、程序的基本结构**

**1.1 最简单的Flask程序**
```python
#coding=utf-8

# 初始化
from flask import Flask
app = Flask(__name__)

# 路由视图函数
@app.route('/')
def index():
    return '<h1>Hello World</h1>'

if __name__ == '__main__':
    # 启动服务器
    app.run(debug=True)
```

<!--more-->

**1.2 请求响应循环**
- **1. 程序和请求上下文**

| 变量名      | 上下文     | 说明 | 
| ----------- | ---------- | ---- | 
| current_app | 程序上下文 | 当前激活程序的程序实例     | 
| g           | 程序上下文 | 处理请求时用作临时存储的对象。每次请求都会重会设这个变量     | 
| request     | 请求上下文 | 请求对象，封装了客户端发出的HTTP请求中的内容     | 
| session | 请求上下文 | 用户会话，用于存储请求之间需要“记住”的值的词典 |

- **2. 请求调度**
Get、HEAD、Options

- **3. 请求钩子**

| 类型 | 作用 |
| ----------- | ---------- |
| before_first_request | 注册一个函数，在处理第一个请求之前运行 |
| before_request | 注册一个函数,在每次请求之前运行 |
| after_request | 注册一个函数，如果没有未处理的异常抛出，在每次请求之后运行 |
| teardown_request | 注册一个函数，即使有未处理的异常抛出，也在每次请求之后运行 |


