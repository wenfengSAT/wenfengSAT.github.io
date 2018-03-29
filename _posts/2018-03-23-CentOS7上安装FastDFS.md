---
layout:     post
title:      CentOS7上安装FastDFS
subtitle:   CentOS7上安装FastDFS
date:       2018-03-23
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - rabbitMQ
---

>CentOS7上安装FastDFS






 
{% raw %}
```hbs



```
{% endraw %}
service fdfs_trackerd start
service fdfs_storaged start

》》》安装fastdfs教程
https://www.cnblogs.com/jkko123/p/6490744.html


》》》启动fastfds

systemctl restart  fdfs_trackerd

systemctl restart fdfs_storaged 



》》》安装 Nginx 的 fastdfs-nginx-module：
 
 
yum install readline-devel pcre-devel openssl-devel -y
下载最新版本并解压：

wget https://openresty.org/download/openresty-1.13.6.1.tar.gz

tar -xvf openresty-1.13.6.1.tar.gz

wget https://github.com/happyfish100/fastdfs-nginx-module/archive/master.zip

unzip master.zip



配置 nginx 安装，加入fastdfs-nginx-module模块：

到openresty-1.13.6.1目录下：

./configure --add-module=../fastdfs-nginx-module-master/src/
编译、安装：

make && make install

》》》查看Nginx的模块：

nginx -v

有nginx-module下就说明添加模块成功 


》》》复制 fastdfs-nginx-module 源码中的配置文件到/etc/fdfs 目录， 并修改：

cp /fastdfs-nginx-module/src/mod_fastdfs.conf /etc/fdfs/


# 连接超时时间
connect_timeout=10

# Tracker Server
tracker_server=192.168.1.190:22122

# StorageServer 默认端口
storage_server_port=23000

# 如果文件ID的uri中包含/group**，则要设置为true
url_have_group_name = true

# Storage 配置的store_path0路径，必须和storage.conf中的一致
store_path0=/home/data/fastdfs/storage


》》》复制 FastDFS 的部分配置文件到/etc/fdfs 目录：

cp /fastdfs-nginx-module/src/http.conf /etc/fdfs/
cp /fastdfs-nginx-module/src/mime.types /etc/fdfs/


》》》配置nginx，修改nginx.conf：

location ~/group([0-9])/M00 {
    ngx_fastdfs_module;
}



》》》启动openresty 下的nginx



