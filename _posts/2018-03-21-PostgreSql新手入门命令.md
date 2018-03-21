---
layout:     post
title:      PostgreSql新手入门命令
subtitle:   PostgreSql新手入门命令
date:       2018-03-21
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - PostgreSql
---

>PostgreSql新手入门命令

## 1 命令行登录数据库
有两种方式，一是直接在系统shell下执行psql命令；而是先进入psql环境，然后再连接数据库。下面分别给出实例：

# (1)直接登录
执行命令：psql -h 172.16.35.179 -U username -d dbname ，其中username为数据库用户名，dbname为要连接的数据库名，执行后提示输入密码如下：
Password for user username: （在此输入密码）

输入密码后即可进入psql环境了。

# (2)切换数据库
有时候需要在psql环境下切换数据库，此时执行如下psql命令：
\c dbname username serverIP port
其中除了数据库名外，其他的参数都是可选的，如果使用默认值可以使用-作为占位符
执行这个命令后，也是提示输入密码。

## 2 查看帮助
psql提供了很好的在线帮助文档，总入口命令是help，输入这个命令就可以看到
vsb9=# help
You are using psql, the command-line interface to PostgreSQL.
Type:  \copyright for distribution terms
       \h for help with SQL commands
       \? for help with psql commands
       \g or terminate with semicolon to execute query
       \q to quit

可以看到，标准SQL命令的帮助和psql特有命令的帮助是分开的。输入\?查看psql命令，会发现所有的psql命令都是以\开头，这就很容易和标准的SQL命令进行区分开来。

## 3 常用命令
为了便于记忆，这里把对应的mysql命令也列出来了。

# (1)列出所有的数据库
mysql: show databases
psql: \l或\list
# (2)切换数据库
mysql: use dbname
psql: \c dbname

# (3)列出当前数据库下的数据表
mysql: show tables
psql: \d

# (4)列出指定表的所有字段
mysql: show columns from table name
psql: \d tablename

# (5)查看指定表的基本情况
mysql: describe tablename
psql: \d+ tablename

# (6)退出登录
mysql: quit 或者\q
psql:\q



