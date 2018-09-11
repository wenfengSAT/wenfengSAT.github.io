---
layout:     post
title:      JPA entity 忽略字段
subtitle:   JPA entity 忽略字段
date:       2018-09-11
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - JPA
---

>JPA entity 忽略字段


java 的transient关键字的作用是需要实现Serilizable接口，将不需要序列化的属性
前添加关键字transient，序列化对象的时候，这个属性就不会序列化到指定的目的地中。

使用示例：

```java
import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.Transient;

@Entity
public class OrderEntity {
    
    @Id
    //...
    private String id;

    /**
     * 附件id不需要持久化
     */
    @Transient
    private String attachMgr;

}
```

>Transient 使用小结

{% raw %}
```hbs  
Transient 使用小结：
　　1）一旦变量被transient修饰，变量将不再是对象持久化的一部分，该变量内容在序列化后无法获得访问。
　　2）transient关键字只能修饰变量，而不能修饰方法和类。注意，本地变量是不能被transient关键字修饰
	的。变量如果是用户自定义类变量，则该类需要实现Serializable接口
　　3）被transient关键字修饰的变量不再能被序列化，一个静态变量不管是否被transient修饰，均不能被序列化。
```
{% endraw %}













