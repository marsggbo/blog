---
title: flask模板教程笔记
date: 2017-02-06 17:10:56
tags: [flask,模板,python]
categories: [学习,程序员,python,flask]
---


以下笔记的记录方式主要是以实际要求作为背景，然后介绍具体的解决办法来行文。

**1、在模板中调用自定义的过滤器**
- **问题背景**
> 之所以要用到自定义的过滤器是因为我自己在做一个博客，博客中有使用markdown语法编辑文章的页面，而最后数据库保存的文章内容是markdown格式而不是html格式，因为我认为这样会节省空间一些，如果要显示文章，只需将markdown格式解析一下即可。

<!--more-->

- **问题**

但是现在遇到的问题就是何时解析markdown格式。
我使用的数据库是flask-sqlalchemy。
我首先通过视图函数将 **数据表(articles)** 传给模板,代码如下
```python
articles = Article.query.all()
...
return render_template('edit.html',articles=articles)
```
在需要渲染文章内容的时候我理所应当的使用for循环来逐个渲染，文章的标题、分类和标签这些信息都非常好处理，但是轮到markdown格式的文章内容的时候我懵了，我知道需要解析成html格式，但是在html模板中我不知道怎么在模板中调用python。

- **解决办法**
后来在麦子学院看了视频茅塞顿开(其实就是教程里给出了解决办法。。。)，可以通过使用过滤器来解决这个问题。使用方法如下：

    - 首先注册过滤器

**views.py**
```python
@app.template_filter('md')
def markdown_to_html(md):
    import markdown
    return markdown.markdown(md)

@app.route('index')
def index():
    articles = Article.query.all()
    ...
    return render_template('edit.html',articles=articles)
    
```

    - 在模板中使用md过滤器，用法为 **{{ 变量|md }}**

**index.html**
```html
{% for article in articles %}
<header class="article-header">
	<h1 class="article-title">
            {{ article.title }}
	</h1>
	<div class="article-mata">
		<span class="article-categories">
			<a href="">{{ article.categories }}</a>
		</span>
	</div>
	<div class="article-body">
		{{ article.content|md|safe }}
	</div>
</header>
```
效果展示

![](http://i1.piimg.com/567571/4b38b0dcfa8a9fa8.png)

**2、flask上下文**
- **问题背景**
> 背景与上面的类似，只是这里是如何解析本地的md文件

- **问题**
如何在模板中调用外部函数？

- **解决办法**
使用上下文装饰器，用法如下：
    - 首先定义好需要调用的外部函数
```python
def read_md(filename):
    from markdown import markdown
    with open(filename,'rb') as f:
        data = reduce(lambda x , y:x + y,f.readlines())
    return markdown(data.decode('utf-8'))
```
    
    - 注册上下文
```python
@app.context_processor
def inject_methods():
    return dict(read_md=read_md)
```
    
    - 在模板中调用即可
    加入'hello.md'在同级目录下
```html
{{ read_md('hello.md') }}
```
