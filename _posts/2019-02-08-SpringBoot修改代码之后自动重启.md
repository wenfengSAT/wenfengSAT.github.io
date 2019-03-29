---
layout:     post
title:      SpringBoot修改代码之后自动重启
subtitle:   SpringBoot修改代码之后自动重启
date:       2019-02-08
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Linux
---


> SpringBoot修改代码之后自动重启


+  pom文件夹以下插件

```

    
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-devtools</artifactId>
		<optional>true</optional>
		<scope>runtime</scope>
	</dependency>


```


 



