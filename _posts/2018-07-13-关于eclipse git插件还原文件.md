---
layout:     post
title:      关于eclipse git插件还原文件
subtitle:   关于eclipse git插件还原文件
date:       2018-07-13
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - GIT
---

>关于eclipse git插件还原文件

{% raw %}
```hbs

 git提交代码步骤：
 1、pull:先拉取git远程仓库代码
 2、commit：提交代码到本地仓库
 3、push:提交代码到远程仓库


步骤：

在pull代码的时候有时候会发现代码冲突，由于git不想svn一样，即使有代码冲突也一样可以pull代码。
所以我们提交的时候可以做个备份，还原代码到上一版本。

如果直接还原整个项目：

1.选择项目，右键Team--Reset

2.Reset Type最好选择Hard（HEAD,index，and working tree updated）,Reset

3.还原之后pull代码，增加自己的代码，提交代码

还原单个文件：

1.选择文件，右键Team--Show in History

2、选择历史版本文件，选择Hard选项Rest

3.还原之后pull代码，增加自己的代码，提交代码


```
{% endraw %}


