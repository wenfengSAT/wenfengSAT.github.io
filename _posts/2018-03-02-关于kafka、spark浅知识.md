---
layout:     post
title:      关于kafka、spark浅知识
subtitle:   关于kafka、spark浅知识
date:       2018-03-02
author:     wenfengSAT
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - 漫谈
---

>关于kafka、spark浅知识

#为什么搭建Kafka需要zookeeper：

kafka 很多说不需要安装zk的是因为他们都使用了kafka自带的zk
至于kafka为什么使用zk，你首先要知道zk的作用, 作为去中心化的集群模式。
需要要消费者知道现在那些生产者（对于消费者而言，kafka就是生产者）是可用的。
如果没了zk消费者如何知道呢？如果每次消费者在消费之前都去尝试连接生产者测试下是否连接成功，效率呢？
所以kafka需要zk，在kafka的设计中就依赖了zk了。

#关于zookeeper：

去中心化的集群模式。


#关于kafka：

Kafka是一种高吞吐量的分布式发布订阅消息系统，它可以处理消费者规模的网站中的所有动作流数据。
Broker
Kafka集群包含一个或多个服务器，这种服务器被称为broker[5] 
Topic
每条发布到Kafka集群的消息都有一个类别，这个类别被称为Topic。（物理上不同Topic的消息分开存储，逻辑上一个Topic的消息虽然保存于一个或多个broker上但用户只需指定消息的Topic即可生产或消费数据而不必关心数据存于何处）
Partition
Partition是物理上的概念，每个Topic包含一个或多个Partition.
Producer
负责发布消息到Kafka broker
Consumer
消息消费者，向Kafka broker读取消息的客户端。
Consumer Group
每个Consumer属于一个特定的Consumer Group（可为每个Consumer指定group name，若不指定group name则属于默认的group）

#关于spark使用：

目前我这边能做的只是用java传给kafka,kafka再给spark,spark计算完后再回传给kafka,java程序再接收

如果你是web同步与spark交互，会阻塞servlet线程，吞吐量有限。
除非你的集群非常碉堡，能在很短时间完成响应。另外结果建议是写到Redis上，前台查询后台，
会得到一个请求id，这个请求id就是redis的key。然后拿着这个id轮询结果获取接口。
我们这边做Spark即时查询，都是SparkContext和Web Service分离，
通过Zookeeper实现HA和负载均衡。Web Service接收SQL查询请求，对SQL进行优化后，
查询ZK获取较空闲的Driver，然后将SQL发送给Driver执行，结果是写在HDFS上，
然后把请求id作为key，HDFS路径作为value，写到Redis上。



