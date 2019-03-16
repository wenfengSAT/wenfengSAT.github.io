---
layout:     post
title:      CentOS7中使用yum安装Nginx的方法
subtitle:   CentOS7中使用yum安装Nginx的方法
date:       2019-03-16
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Linux
---


> CentOS7中使用yum安装Nginx的方法



- 1、添加源

　　默认情况Centos7中无Nginx的源，最近发现Nginx官网提供了Centos的源地址。因此可以如下执行命令添加源：


```shell

    sudo rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
	
```

- 2、安装Nginx

　　通过yum search nginx看看是否已经添加源成功。如果成功则执行下列命令安装Nginx。

```shell

    sudo yum install -y nginx
	
```

- 3、启动Nginx并设置开机自动运行


```shell

    sudo systemctl start nginx.service
	sudo systemctl stop nginx.service
	
```


    

 
