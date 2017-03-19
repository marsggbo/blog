---
title: Flask入门笔记（二）--模板
date: 2017-01-16 18:27:59
tags: [python,flask,web框架,模板,Jinja]
categories: [学习,程序员,python,flask]
---


## **1. 渲染模板**

使用了**Jinja2**模板引擎
**render_template**

```python
from flask import render_template,Flask
```

<!--more-->

## **2. 变量**

Jinja2变量过滤器

| 过滤器名 | 说明 |
| --- | --- |
| safe | 渲染值时不转义 |
| capitalize | 把值的首字母转换成大写 |
| lower | 把值转换成小写形式 |
| upper | 把值转换成大写形式 |
| title | 把值中每个单词的首字母都转换成大 写 |
| trim | 把值的首尾空格去掉 |
| striptags | 渲染之前把值中所有的HTML标签都删掉 |

示例
```html
# html文件
hello {{ name|capitalize}}
```

## **3. 控制结构**
- ### **条件控制语句**

```python
{% if user %}
    Hello, {{ user }}
{% else %}
    Hello, Stranger!
{% endif %}
```

- ### **for循环**

```html
<ul>
    {% for comment in comments %}
    	<li>{{ comment }}</li>
    {% endfor %}
</ul>
```

Jinja2中支持宏。例如
```html
{% macro render_comment(comment)%}
    <li>{{ comment }}</li>
{% endmacro %}

<ul>
    {% for comment in comments %}
    	{{ render_comment(comment) }}
    {% endfor %}
</ul>
```

有时会**重复**使用宏，所以最好保存在单独的文件中，然后在需要使用的模板中导入即可。

```html
{% import 'macro.html' as macro %}

<ul>
    {% for comment in comments %}
    	{{ macro.render_comment(comment) }}
    {% endfor %}
</ul>
```

- ### **使用模板**

另一种重复使用代码的强大方式是模板继承。
首先，创建一个名为base.html的及模板:
```html
<!DOCTYPE html>
<html>
<head>
	{% block head %}
	<title>{% block title%}{% endblock %}-Base HTML</title>
	<style type="text/css">{% block style %}{% endblock %}</style>
	{% endblock %}
</head>
<body>
	这是基模板
	{% block body%}
		
	{% endblock %}
</body>
</html>
```

衍生模板
```html
{% extends "base.html "%}
{% block title %}Index{% endblock %}
{% block style %}
	body{
		background-color: tomato;
	}
{% endblock %}
{% block head %}
	{{ super() }}


{% endblock %}
{% block body %}
	<h1>这是衍生模板</h1>
{% endblock %}
```
效果图：

![](http://p1.bqimg.com/567571/8ba8ba915e95ef6b.png)
注意衍生模板中的title和style需要放在head的上面，即如果衍生模板代码是下面这样的就会重复显示内容
```html
{% extends "base.html "%}
{% block head %}
{% block title %}Index{% endblock %}
{% block style %}
	body{
		background-color: tomato;
	}
{% endblock %}
	{{ super() }}


{% endblock %}
{% block body %}
	<h1>这是衍生模板</h1>
{% endblock %}
```

如下图

![](http://p1.bqimg.com/567571/e77287e10d18a3c2.png)
之所以会这样，我的理解是title和style都是head的子元素，之所以要放在head的前面是因为有 **super()** 这个方法会获取原来的内容，并插入到适当的位置。

- ### **自定义错误页面**

```python
@app.errorhandler(404)
def page_not_find(e):
	return render_template('404.html'),404

@app.errorhandler(500)
def interval_server_error(e):
	return render_template('500.html'),500
```