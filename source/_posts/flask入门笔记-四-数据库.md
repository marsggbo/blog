---
title: flask入门笔记(四)--数据库
date: 2017-01-19 14:34:07
tags: [flask,flask-sqlalchemy,数据库]
categories: [学习,程序员,python,flask]
---

这里介绍的是**flask-SQLAlchemy** 


### **1、 安装扩展**
```python
pip install flask-sqlalchemy
```

### **2、 配置数据库**
<!--more-->
就这么配置吧，具体的原理以后慢慢学习。
```python
import os
basedir = os.path.abspath(os.path.dirname(__file__)) 

app = Flask(__name__)
app.config['SECRET_KEY'] = 'hard to guess string'

app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///' + os.path.join(basedir, 'data.sqlite')
app.config['SQLALCHEMY_COMMIT_ON_TEARDOWN'] = True
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = True

db = SQLAlchemy(app)
```

### **3、 定义模型**
这里的模型是以论坛网站为背景，每个用户都扮演一个角色(**Role**)，即管理员、版主和普通用户。每个用户拥有自己的表单数据(**User**)。
```python
class Role(db.Model):
    __tablename__ = 'roles'
    id = db.Column(db.Integer,primary_key=True)
    name = db.Column(db.String(64),unique=True)
    users = db.relationship('User',backref='role')

    def __repr__(self):
        return '<Role %r>' % self.name

class User(db.Model):
    __tablename__ = 'users'
    id = db.Column(db.Integer,primary_key=True)
    role_id = db.Column(db.Integer,db.ForeignKey('roles.id'))
    username = db.Column(db.String(64),unique=True,index=True)

    def __repr__(self):
        return '<User %r>' % self.username
```

### **4、 关系**
关系已经在上面的代码中显示出来了，下面只对上面的代码做一下解释。
```python
class Role(db.Model):
    __tablename__ = 'roles'
    # ...
    users = db.relationship('User',backref='role')


class User(db.Model):
    __tablename__ = 'users'
    # ...
    role_id = db.Column(db.Integer,db.ForeignKey('roles.id'))
```

db.ForeignKey()表明这一列被定义为外键，其中传入的参数是"roles.id"表明这一列的值是roles表中行的id值。

db.relationship()的第一个参数表示这个关系的另一端是哪个模型，另外backref参数想User模型中添加一个role属性，从而定义反向关系，这一属性可替代role_id访问Role模型，此时获取的是模型对象，而不是外键的值，所以课给出如下数据模型的定义：
```python
admin_role = Role(name="Admin")     #管理员
mod_role = Role(name="Moderator")   #版主
user_role = Role(name="User")       #普通用户

user_marsA = User(username="marsA",role=admin_role)
user_marsB = User(username="marsB",role=user_role)
user_marsC = User(username="marsC",role=user_role)
```
可见User模型传入的第二个参数是模型对象，而不是具体的键值。

### **5、 视图函数**
注意之前导入表单模块是这样的from flask_wtf import Form,但是根据官方提示，因为版本升级，为了以后的代码兼容问题建议写成这样
```python
from flask_wtf import FlaskForm
```

**视图函数**

```python
class NameForm(FlaskForm):
    name = StringField('Whats your name?', validators=[Required()])
    submit = SubmitField('Submit')

@app.route('/',methods=['GET','POST'])
def index():
    form = NameForm()
    if form.validate_on_submit():
        user = User.query.filter_by(username=form.name.data).first()
        if user is None:
            user = User(username = form.name.data)
            db.session.add(user)
            session['known'] = False
        else:
            session['known'] = True
        session['name'] = form.name.data
        form.name.data = ''
        return redirect(url_for('index'))
    return render_template('index.html',form=form,name=session.get('name'),known=session.get('known'))
```

**基模板**

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

**衍生模板**

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
            {% if known %}
                <h3 class="text-center">Nice to See you again!</h3>
            {% else %}
                <h3 class="text-center">Nice to meet you!</h3>
            {% endif %}
        </div>
        <form method="POST">
            {{ form.hidden_tag() }}
            {{ form.name.label }}{{ form.name(id='my-text-field', class="form-control" )}}
            {{ form.submit(class='btn btn-info form-control') }}
        </form>
    </div>
{% endblock %}
```