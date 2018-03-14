---
layout:     post
title:      CentOS7上安装rabbitMQ
subtitle:   CentOS7上安装rabbitMQ
date:       2018-03-14
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - rabbitMQ
---

>CentOS7上安装rabbitMQ


centos安装rabbitmq：

1、安装erlang
yum install erlang
2、输入erl命令进入erlang操作界面，5+6后面有个小数点，q(). 退出操作，最后有个小数点（或两次ctrl+c退出）
3、安装RabbitMQ
rpm -ivh https://bintray.com/rabbitmq/rabbitmq-server-rpm/download_file?file_path=rabbitmq-server-3.6.10-1.el7.noarch.rpm

报错解决：
要安装类似依赖的东西
rpm -ivh https://mirrors.ustc.edu.cn/epel/7//x86_64/Packages/e//epel-release-7-11.noarch.rpm

参考命令：
安装： rpm -ivh https://mirrors.ustc.edu.cn/epel/7//x86_64/Packages/e//epel-release-7-11.noarch.rpm
更新：rpm -Uvh https://mirrors.ustc.edu.cn/epel/7//x86_64/Packages/e//epel-release-7-11.noarch.rpm
卸载：rpm -e epel-release

4、启动RabbitMQ服务命令
/sbin/service rabbitmq-server start
5、查看RabbitMQ服务状态命令
/sbin/service rabbitmq-server status
6、配置网页访问RabbitMQ

1)查看RabbitMQ中用户命令

rabbitmqctl list_users

2)创建用户命令

 rabbitmqctl add_user hwf hwf

3)赋予用户权限命令

 rabbitmqctl  set_permissions -p "/" hwf '.*' '.*' '.*'

4)赋予用户角色命令

rabbitmqctl set_user_tags hwf administrator

5)开启rabbitmq管理控制台命令

rabbitmq-plugins enable rabbitmq_management

6)访问http://106.14.191.238:15672/


7、查看是否安装成功：
ps -ef|grep rabbitmq
ps -aux|grep 5672