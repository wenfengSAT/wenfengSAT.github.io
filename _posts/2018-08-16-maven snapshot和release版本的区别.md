---
layout:     post
title:      maven snapshot和release版本的区别
subtitle:   maven snapshot和release版本的区别
date:       2018-08-16
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - maven
---

>maven snapshot和release版本的区别

{% raw %}
```hbs
	在使用maven过程中，我们在开发阶段经常性的会有很多公共库处于不稳定状态，
随时需要修改并发布，可能一天就要发布一次，遇到bug时，甚至一天要发布N次。
我们知道，maven的依赖管理是基于版本管理的，对于发布状态的artifact，
如果版本号相同，即使我们内部的镜像服务器上的组件比本地新，maven也不会主动下载的。
如果我们在开发阶段都是基于正式发布版本来做依赖管理，那么遇到这个问题，
就需要升级组件的版本号，可这样就明显不符合要求和实际情况了。
但是，如果是基于快照版本，那么问题就自热而然的解决了，而maven已经为我们准备好了这一切。


	maven中的仓库分为两种，snapshot快照仓库和release发布仓库。
snapshot快照仓库用于保存开发过程中的不稳定版本，
release正式仓库则是用来保存稳定的发行版本。定义一个组件/模块为快照版本，
只需要在pom文件中在该模块的版本号后加上-SNAPSHOT即可(注意这里必须是大写)，如下：

  <groupId>com.asiainfo</groupId>
  <artifactId>vehicle-lcu</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>    <!-- 如果不加-SNAPSHOT，默认是上传到release版本 -->
  <name>vehicle-lcu</name>

  
	maven2会根据模块的版本号(pom文件中的version)中是否带有-SNAPSHOT来判断是快照
版本还是正式版本。如果是快照版本，那么在mvn deploy时会自动发布到快照版本库中，
而使用快照版本的模块，在不更改版本号的情况下，直接编译打包时，maven会自动从镜
像服务器上下载最新的快照版本。如果是正式发布版本，那么在mvn deploy时会自动发
布到正式版本库中，而使用正式版本的模块，在不更改版本号的情况下，编译打包时如
果本地已经存在该版本的模块则不会主动去镜像服务器上下载。

	所以，我们在开发阶段，可以将公用库的版本设置为快照版本，而被依赖组件则
引用快照版本进行开发，在公用库的快照版本更新后，我们也不需要修改pom文件
提示版本号来下载新的版本，直接mvn执行相关编译、打包命令即可重新下载最新
的快照库了，从而也方便了我们进行开发。


附pom配置：

<!-- 私有仓库 -->
  <repositories>
    <repository>
      <id>nexus</id>
      <name>nexus</name>
      <url>http://ip:端口/nexus/content/groups/public</url>
      <releases>
        <enabled>true</enabled>
      </releases>
      <snapshots>
        <enabled>true</enabled>
      </snapshots>
    </repository>
  </repositories>

  <pluginRepositories>
    <pluginRepository>
      <id>nexus</id>
      <name>nexus</name>
      <url>http://ip:端口/nexus/content/groups/public</url>
      <releases>
        <enabled>true</enabled>
      </releases>
      <snapshots>
        <enabled>true</enabled>
      </snapshots>
    </pluginRepository>
  </pluginRepositories>

  <!-- 打包发布 -->
  <distributionManagement>
    <repository>
      <id>releases</id>
      <url>http://ip:端口/nexus/content/repositories/releases/</url>
    </repository>

    <snapshotRepository>
      <id>snapshots</id>
      <url>http://ip:端口/nexus/content/repositories/snapshots</url>
    </snapshotRepository>
  </distributionManagement>
```
{% endraw %}