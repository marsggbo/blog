---
title: hexo -d报错解决办法
date: 2017-03-04 15:58:48
tags: [hexo,git]
categories: [学习,程序员,hexo]
---

hexo d的时候报错，错误信息如下：
```
$ hexo d
INFO Deploying: git
INFO Clearing .deploy folder...
INFO Copying files from public folder...
warning: LF will be replaced by CRLF in 2015/12/05/hello-world/index.html.
The file will have its original line endings in your working directory.
……
warning: LF will be replaced by CRLF in index.html.
```

在某乎上找到了答案，链接在文章最下面。


----------
搬运工来啦：


这个问题是这样解决的
- 1. 删除你hexo 下面的.deploy_git文件夹
- 2. 运行 
```bash
git config -global core.autoclrf false
```
- 3. 重新部署
```
hexo clean
hexo g
hexo d
```
- 4. 打开自己的网址，进行验证是否成功


----------


**作者：邹世杰
链接：https://www.zhihu.com/question/38219432/answer/123443945
来源：知乎**

<br><br><hr>
<footer style="padding:10px;border-radius:10px;;text-align:center;background-color:rgb(11,12,13);color:white;">
written by <b style="color:tomato;font-size:16px;">MARSGGBO</b>
<br><span style="font-size:16px;">
2017-2-14</span>
</footer>