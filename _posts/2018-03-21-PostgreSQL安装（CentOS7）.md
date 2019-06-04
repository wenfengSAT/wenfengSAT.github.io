---
layout:     post
title:      PostgreSQL安装（CentOS7）
subtitle:   PostgreSQL安装（CentOS7）
date:       2018-03-21
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - PostgreSql
---

>PostgreSQL安装（CentOS7）


```hbs

1、yum install https://download.postgresql.org/pub/repos/yum/9.6/redhat/rhel-7-x86_64/pgdg-centos96-9.6-3.noarch.rpm
waiting....
选择y，继续..

2、yum install postgresql96-server postgresql96-contrib
waiting....
选择y，继续..

3、/usr/pgsql-9.6/bin/postgresql96-setup initdb
waiting....
初始化数据库..

4、systemctl start postgresql-9.6
启动数据库

5、systemctl enable postgresql-9.6


5、iptables -I INPUT -p tcp --dport 5432 -m state --state NEW -j ACCEPT
iptables-save > /etc/sysconfig/iptables
开放端口

firewall-cmd --add-service=postgresql --permanent
firewall-cmd --reload



7、修改密码
su - postgres
psql -U postgres
alter user postgres with password 'postgres@123456'
\q
exit



8、 允许远程访问
#配置文件中，默认只能本机访问postgresql；

vi /var/lib/pgsql/9.6/data/postgresql.conf
修改listen_addresses = 'localhost'为listen_addresses = '*'，允许所有远程访问

vim /var/lib/pgsql/9.6/data/pg_hba.conf
添加host    all             all             0.0.0.0/0               trust 

systemctl restart postgresql-9.6.service


```



参考：http://blog.csdn.net/DaSo_CSDN/article/details/75330009  

	  http://www.jb51.net/article/104829.htm  
	  
	  https://www.linuxidc.com/Linux/2017-10/147536.htm



