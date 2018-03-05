---
layout:     post
title:      CentOS7上搭建Nginx
subtitle:   CentOS7上搭建Nginx
date:       2018-03-05
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - CentOS7
    - Nginx
---

###CentOS7上搭建Nginx

##什么是Nginx?
Nginx ("engine x") 是一个高性能的 HTTP 和 反向代理 服务器，也是一个 IMAP/POP3/SMTP 代理服务器，在高连接并发的情况下Nginx 是 Apache 服务器不错的替代品.其特点是占有内存少，并发能力强，事实上nginx的并发能力确实在同类型的网页服务器中表现较好.目前中国大陆使用nginx网站用户有：新浪、网易、 腾讯，另外知名的微网志Plurk也使用nginx。 
　　Nginx 作为 负载均衡 服务器： 
Nginx 既可以在内部直接支持 Rails 和 PHP 程序对外进行服务，也可以支持作为 HTTP 代理服务器对外进行服务。 Nginx 采用 C 进行编写， 不论是系统资源开销还是 CPU 使用效率都比 Perlbal 要好很多。作为邮件代理服务器： 
　　Nginx 同时也是一个非常优秀的邮件代理服务器（最早开发这个产品的目的之一也是作为邮件代理服务器）， Last.fm 描述了成功并且美妙的使用经验。Nginx 是一个安装非常简单，配置文件非常简洁（还能够支持perl语法）， Bugs非常少的服务器： Nginx 启动特别容易，并且几乎可以做到 7*24 不间断运行，即使运行数个月也不需要重新启动。 你还能够不间断服务的情况下进行软件版本的升级。
 
##Nginx的安装

模块依赖性Nginx需要依赖下面3个包
1. gzip 模块需要 zlib 库 ( 下载: http://www.zlib.net/ )
2. rewrite 模块需要 pcre 库 ( 下载: http://www.pcre.org/ )
3. ssl 功能需要 openssl 库 ( 下载: http://www.openssl.org/ )
 
Nginx包下载: http://nginx.org/en/download.html
 
依赖包安装顺序依次为:openssl、zlib、pcre, 然后安装Nginx包.
 

#第一步： 下载安装所需包
openssl-fips-2.0.2.tar.gz
zlib-1.2.7.tar.gz

pcre-8.21.tar.gz

nginx-1.2.6.tar.gz

#第二步:依次安装openssl-fips-2.0.2.tar.gz, zlib-1.2.7.tar.gz, pcre-8.21.tar.gz, nginx-1.2.6.tar.gz

1.安装openssl-fips-2.0.2.tar.gz

复制代码执行
[root@localhost mrms]# tar -zxvf openssl-fips-2.0.2.tar.gz 

[root@localhost mrms]# cd openssl-fips-2.0.2

[root@localhost openssl-fips-2.0.2]# ./config 

[root@localhost openssl-fips-2.0.2]# make

[root@localhost openssl-fips-2.0.2]# make install

复制代码执行
2.安装zlib-1.2.7.tar.gz

复制代码执行
[root@localhost mrms]# tar -zxvf zlib-1.2.7.tar.gz

[root@localhost mrms]# cd zlib-1.2.7

[root@localhost zlib-1.2.7]# ./configure 

[root@localhost zlib-1.2.7]# make

[root@localhost zlib-1.2.7]# make install

复制代码执行
3.安装pcre-8.21.tar.gz

复制代码执行
[root@localhost mrms]# tar -zxvf pcre-8.21.tar.gz

[root@localhost mrms]# cd pcre-8.21

[root@localhost pcre-8.21]# ./configure 

[root@localhost pcre-8.21]# make

[root@localhost pcre-8.21]# make install

如果安装失败：configure: error: You need a C++ compiler for C++ support.
执行：yum install -y gcc gcc-c++

复制代码执行
4.安装 nginx-1.2.6.tar.gz

复制代码执行
[root@localhost mrms]# tar -zxvf nginx-1.2.6.tar.gz 

[root@localhost mrms]# cd nginx-1.2.6

[root@localhost nginx-1.2.6]# ./configure --with-pcre=../pcre-8.37 --with-zlib=../zlib-1.2.8 --with-openssl=../openssl-1.0.2e

[root@localhost nginx-1.2.6]# make

[root@localhost nginx-1.2.6]# make install

至此Nginx的安装完成!

第三步:检测是否安装成功

[root@localhost nginx-1.2.6]# cd  /usr/local/nginx/sbin

[root@localhost sbin]# ./nginx -t

启动nginx
[root@localhost sbin]# ./nginx
查看端口
[root@localhost sbin]# netstat -ntlp 

##Nginx的简单配置文件
{% raw %}
```hbs
#user  nobody;
worker_processes  1;

error_log  logs/error.log;
error_log  logs/error.log  notice;
error_log  logs/error.log  info;

pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

	#gzip压缩的方式来降低带宽使用和加快传输速度，也可以不使用
    #gzip on;
    
	upstream local_tomcat {   #默认轮询
	  
	    #server  10.2.0.77;
		server 127.0.0.1:8080;
		server 106.14.191.238:8080;
		server 127.0.0.1:8888;
		#server localhost:8080 weight=1;     #加权比重   
		#server localhost:8888 weight=5;	 #加权比重	
	  
	}     
					  
	server {
    listen    80;
	
    server_name  www.3304.com;   #随便取名
 
    #charset koi8-r;
 
    #access_log logs/host.access.log main;
 
    location / {
      #root  html;        #配置静态资源目录
      #index index.html index.htm;
      proxy_set_header Host $host;
      proxy_set_header X-Real-Ip $remote_addr;
      proxy_set_header X-Forwarded-For $remote_addr;
      proxy_pass http://local_tomcat/;
    }				  
					  
	
    #server {
    #    listen       80;
    #    server_name  localhost;
    #
    #    #charset koi8-r;
    #
    #    #access_log  logs/host.access.log  main;
    #
    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```
{% endraw %}

