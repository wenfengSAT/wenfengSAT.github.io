---
layout:     post
title:      eclipse git checkout conflict with files
subtitle:   关于eclipse git插件中同步代码报错checkout conflict with files的解决方法
date:       2018-07-13
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - GIT
---

>关于eclipse git插件中同步代码报错checkout conflict with files的解决方法

{% raw %}
```hbs

 使用eclipse git时候已经把冲突解决了，pull代码时候遇到checkout conflict with files。


步骤：

1.根据details中提示的路径，找到报错的那个文件

2.在eclipse的package explore中选择项目，右键选择Team-->Synhronize Workspace

3.在打开的Synhronize窗口中找到那个文件，然后选择dataConversionTask文件右键 Overwrite，弹出窗口选择yes

4.再次选择那个文件右键选中mark as merged

5.之后再pull代码就不会报错了



```
{% endraw %}


