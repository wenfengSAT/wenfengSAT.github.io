---
layout:     post
title:      Mybatis插入数据返回新插入数据id
subtitle:   Mybatis插入数据返回新插入数据id
date:       2018-03-05
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - CentOS7
    - Nginx
---

>Mybatis插入数据返回新插入数据id


mybatis插入数据返回主键实例：
{% raw %}
```hbs
    <!-- 
     useGeneratedKeys="true"  表示给主键设置自增长
     keyProperty="id"         表示将自增长后的Id赋值给实体类中的id字段。
     parameterType            这个属性指向传递的参数实体类
     -->
    <insert id="insertBookmark" useGeneratedKeys="true" keyProperty="id" parameterType="com.model.eclipse.entity.Bookmark">
    	insert into bookmark(pId,userId,title,code,href,icon,status,
		description,creator,createDate,modifier,modifyDate)
		values(#{pId},#{userId},#{title},#{code},#{href},#{icon},#{status},
		#{description},#{creator},#{createDate},#{modifier},#{modifyDate})
    </insert>
```
{% endraw %}	
保存之后直接通过getId()方法获取id即可。