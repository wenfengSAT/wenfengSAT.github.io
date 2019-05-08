---
layout:     post
title:      记一次GIT使用git reset之后找回历史文件
subtitle:   记一次GIT使用git reset之后找回历史文件
date:       2019-05-08
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - git
---

>记一次GIT使用git reset之后找回历史文件

- 事件

```

今天提交代码时，先使用git把代码add进git，当commit的时候发现当前不是在自己的个人
上开发，所以停止commit，切换到个人本地分支。切换的时候提示文件更改冲突，当时手快
直接reset代码切换分支。当切到个人分支时，再提交时一脸懵逼：我在哪？我干了啥？我的代码呢？

```


- 解决

```

1、到项目下打开git窗口执行：git fsck --lost-found

2、找到.git/lost-found/other这个路径下的文件

3、打开该路径下的文件，对应找到丢失的文件

```

