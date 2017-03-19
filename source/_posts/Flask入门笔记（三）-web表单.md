---
title: Flask入门笔记（三）--web表单
date: 2017-01-18 12:15:43
tags: [Python,flask,flask-wtf,web表单]
categories: [学习,程序员,python,flask]
---

- **4.1 跨站请求伪造保护**

```python
app = Flask(__name__)
app.config['SECRET_KEY'] = 'hard to guess string'
```

- **4.2 表单类**
导入flask的表单扩展方法：
（之前的版本是**flask.ext.wtf**,建议还是用**flask_wtf**）

<!--more-->

```python
from flask_wtf import Form
from wtforms import StringField,PasswordField
```
**WTForms支持的HTML标准字段**

|  字段类型   | 说明    |  字段类型   |   说明  |
| --- | --- | --- | --- |
|  StringField   | 文本字段    |  BooleanField   | 复选框(True/False)    |
|  TextAreaField   |  多行文本字段   | RadioField    | 单选框    |
|  PasswordField   |  密码文本字段   | SelectField    | 下拉列表    |
|  HiddenField   |  隐藏文本字段   | SelectMultipleField    |  可多选下拉列表   |
|  DateField   |  值为datetime.date格式   | FileField    | 文件上传字段    |
|  DateTimeField   |  值为datetime.datetime格式   | SubmitField    |  提交字段   |
|  IntegerField   |  文本字段,整型   | FormField    | 把表单嵌入到另一个表单    |
|  DecimalField   | 文本字段，十进制    | FieldList    | 一组指定类型的字段    |
|  FloatField   |   文本字段，浮点型  |     |     |

**WTForms验证函数**

| 验证函数    | 说明                                           | 验证函数 | 说明                       |
| ----------- | ---------------------------------------------- | -------- | -------------------------- |
| Email       | 验证电子邮件格式                               | Optional | 无输入值时跳过其他验证函数 |
| EqualTo     | 比较两字段的值，常用于账号注册，如输入两次密码 | Required | 确保字段中有数据           |
| IpAddress   | 验证Ipv4地址                                   | Regexp   | 使用正则验证               |
| Length      | 验证字符串长度                                 | URL      | 验证URL                    |
| NumberRange | 验证输入值在指定范围内                         | Anyof    | 确保输入值在可选值列表中   |
| Noneof      |  确保输入值不在可选值列表中                    |          |      |

使用步骤：

 1. **首先创建表单类**

```python
class NameForm(Form):
	name = StringField('Hello?', validators=[Required()])
	submit = SubmitField('Submit')
```

 2. **把表单渲染成HTML**
我这里使用到了模板继承，下面给出基模板和衍生模板

**基模板 base.html**

```html
<!DOCTYPE html>
<html>
<head>
	{% block head %}
	<title>{% block title%}{% endblock %}-Base HTML</title>
	<style type="text/css">{% block style %}{% endblock %}</style>
	<link rel="stylesheet" type="text/css" href="../static/bootstrap.css">
	{% endblock %}
</head>
<body>
	{% block navbar %}
	<div class="navbar navbar-inverse" role="navigation">
		<div class="container">
			<div class="navbar-collapse collapse">
				<ul class="nav navbar-nav">
					<li><a href="\">Home</a></li>
					<li><a href="\">Download</a></li>
					<li><a href="\">Contact</a></li>
				</ul>
			</div>
		</div>
	</div>
	{% endblock%}

	{% block content %}
	<div class="container">
		{% block page_content %}{% endblock %}
	</div>
	{% endblock %}
</body>
</html>
```

**衍生模板 index.html**

```html
{% extends "base.html" %}
{% block title %}Flasky{% endblock %}

{% block page_content %}
	<div class="page-header">
		<h1 class="text-center">Flask-WTF</h1>
		<h1 class="text-center">
			Hello,{% if name %}{{ name }}{% else %}Stranger{% endif %}!
		</h1>
		<div class="container">
			{% for message in get_flashed_messages() %}
				<div class="alert alert-warning">
					<button type="button" class="close" data-dismiss="alert">&times;</button>
					{{ message}}
				</div>
			{% endfor %}
		</div>
		<form method="POST">
			{{ form.hidden_tag() }}
			{{ form.name.label }}{{ form.name(id='my-text-field', class="form-control" )}}
			{{ form.submit(class='btn btn-info form-control') }}
		</form>
	</div>
{% endblock %}
```

3. **在视图函数中处理表单**
```python
@app.route('/',methods=['GET','POST'])
def index():
	name = None
	form = NameForm()
	if form.validate_on_submit():
		name = form.name.data
		print("form:",form,'\n',"form.name:",form.name,'\n',"form.name.label:",form.name.label,'\n','name:',name)
		form.name.data = ''
	return render_template('index.html',form=form,name=name)
```
局部变量name用于获取表单中用户输入的信息，其中提交表单后，若通过验证(第一步骤中的表单类的 **name = StringField('Hello?', ==validators=[Required()]==)** 使用了验证函数),若通过验证，则  **form.validate_on_submit()** 返回True,然后渲染表单页面。

- **4.3 重定向和用户会话**
使用redirect和url_for()。
```python
@app.route('/redirect',methods=['GET','POST'])
def redirect_():
	form = NameForm()
	if form.validate_on_submit():
		session['name'] = form.name.data
		return redirect(url_for('redirect_'))
	return render_template('index.html',form=form,name=session.get('name'))
```
注意url_for()中的值要与函数名相匹配

- **4.4 flash消息**
```python
@app.route('/flash',methods=['GET','POST'])
def flash_():
	form = NameForm()
	if form.validate_on_submit():
		old_name = session.get('name')
		if old_name and old_name != form.name.data:
			flash('You new name is %s!'%form.name.data)
		session['name'] = form.name.data
		return redirect(url_for('flash_'))
	return render_template('index.html',form=form,name=session.get('name'))
```
使用flash方法，光这样还不行，还需要在模板中渲染flash。Flask把**get_flashed_messages()** 函数开放给模板，用于渲染flash消息。代码示例：
```html
<div class="container">
	{% for message in get_flashed_messages() %}
		<div class="alert alert-warning">
			<button type="button" class="close" data-dismiss="alert">&times;</button>
			{{ message}}
		</div>
	{% endfor %}
</div>
```
效果图

![flash](http://i1.piimg.com/567571/b0335518fa5bf2ac.png)