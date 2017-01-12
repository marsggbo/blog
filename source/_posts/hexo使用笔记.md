---
title: hexo使用笔记
date: 2016-12-03 23:47:32
tags: [hexo,配置,主题]
---

**一、创建新页面**

下面假如我要创建一个个人介绍的单独页面，具体步骤如下：
- **第一步：新建页面**

```
$ cd your-hexo-site
$ hexo new page about
```
之后会出现如下提示

![建立新页面](http://p1.bqimg.com/567571/3474be1690d9ed04.png)

<!--more-->
- **第二步：设置页面类型**

新建页面后，会在source目录下创建和你命名的一个同步的文件夹，文件夹中有一个index.md文件，之后的个人简介可以在这里展示。文件结构如下图

![设置页面类型](http://p1.bqimg.com/567571/29226b9e7e4cecb9.png)

之后打开index.md文件，将文件类型标注为about即可
```
---
title: about
date: 2016-12-03 23:45:44
comments: false
type: 'about'
---
```

- **第三步：修改主题配置文件**

```
menu:
  home: /
  archives: /archives
  tags: /tags
  about: /about
```
--------

注：如果你开启了多说评论，但是在个人介绍页面不想显示多说，可以再添加**comments: false**禁止多说,如下
```markdown
---
title: about
date: 2016-12-03 23:45:44
type: 'about'
comments: false
---
```