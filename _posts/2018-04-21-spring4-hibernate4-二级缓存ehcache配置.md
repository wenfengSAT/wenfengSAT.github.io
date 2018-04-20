---
layout:     post
title:      SpringBoot日志logback配置备份
subtitle:   SpringBoot日志logback配置备份
date:       2018-04-20
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Log
---

>SpringBoot日志logback配置备份



 
{% raw %}
```hbs


一、 Ehcache配置文件ehcache.xml

<?xml version="1.0" encoding="UTF-8"?>

<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="../config/ehcache.xsd">

 
	<!-- 
    Subdirectories can be specified below the property e.g. java.io.tmpdir/one 
    -->  
    <diskStore path="java.io.tmpdir"/>  
  
    <!--  
    Mandatory Default Cache configuration. These settings will be applied to caches  
    created programmtically using CacheManager.add(String cacheName)  
    -->  
    <defaultCache  
         maxElementsInMemory="10000"  
         eternal="false"  
         timeToIdleSeconds="120"  
         timeToLiveSeconds="120"  
         overflowToDisk="true"  
         maxElementsOnDisk="10000000"  
         diskPersistent="false"  
         diskExpiryThreadIntervalSeconds="120"  
         memoryStoreEvictionPolicy="LRU"  
     />  
      
    <cache name="org.hibernate.cache.spi.UpdateTimestampsCache"  
           maxElementsInMemory="5000"   
           eternal="true"   
           overflowToDisk="true" />  
    <cache name="org.hibernate.cache.internal.StandardQueryCache"  
           maxElementsInMemory="10000"   
           eternal="false"   
           timeToLiveSeconds="120"  
           overflowToDisk="true" />    
      
    <!--  
    java文件注解查找cache方法名的策略：如果不指定java文件注解中的region="ehcache.xml中的name的属性值",   
    则使用name名为com.lysoft.bean.user.User的cache(即类的全路径名称), 如果不存在与类名匹配的cache名称, 则用 defaultCache  
    如果User包含set集合, 则需要另行指定其cache  
    例如User包含citySet集合, 则也需要  
    添加配置到ehcache.xml中  
    -->      
    <cache name="copierCache" maxElementsInMemory="2000" eternal="false"   
           timeToIdleSeconds="120" timeToLiveSeconds="120"  
           overflowToDisk="true" />  

</ehcache>

注意该配置文件须放置在项目的classpath中

二、 Hibernate配置二级缓存（applicationContext-dao.xml配置，在hibernate的sessionFactory里加）

在hibernate的LocalSessionFactoryBean这个bean的配置中添加二级缓存的配置项：

<!-- 开启二级缓存 -->  

<prop key="hibernate.cache.use_second_level_cache">true</prop> 

<!-- 启动"查询缓存"如果想缓存使用findall()、list()、Iterator()、createCriteria()、createQuery()等方法获得的数据结果集,必须配置此项-->  

<prop key="hibernate.cache.use_query_cache">true</prop> 

<!-- 高速缓存提供程序 -->   

<!-- 由于spring也使用了Ehcache, 保证双方都使用同一个缓存管理器 -->  

<prop key="hibernate.cache.region.factory_class">  

     org.hibernate.cache.ehcache.SingletonEhCacheRegionFactory  

</prop>    

其中

<prop key="hibernate.cache.region.factory_class">  

     org.hibernate.cache.ehcache.SingletonEhCacheRegionFactory  

</prop>

是当spring也配置了ehcache作为缓存时，为了使spring缓存和hibernate缓存使用同一缓存管理器，从而保证缓存的一致性，
需要使用org.hibernate.cache.ehcache.SingletonEhCacheRegionFactory。
如果仅是hibernate使用ehcache的话，org.hibernate.cache.ehcache.EhCacheRegionFactory即可。

三、 Hibernate映射实体的缓存配置（*.hbm.xml文件配置）

为了使hibernate缓存相应的实体对象，需要在实体映射配置的实体类的第一行添加cache配置，如下：

<class name="User" table="User">

    <cache usage="read-write"/>

…

</class>

在代码中使用Query的list等查询方法时，默认是不会自动缓存实体对象的，需要调用Query的方法query.setCacheable(true).list();来进行缓存即可。


```
{% endraw %}


