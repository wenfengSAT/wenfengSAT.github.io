---
layout:     post
title:      Java中数组、List、Set互相转换
subtitle:   Java中数组、List、Set互相转换
date:       2018-09-05
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - List
---

>数组转List

```java
    String[] staffs = new String[]{"Tom", "Bob", "Jane"};
    List staffsList = Arrays.asList(staffs);
```

需要注意的是， Arrays.asList() 返回一个受指定数组决定的固定大小的列表。
所以不能做 add 、 remove 等操作，否则会报错。

```java
    List staffsList = Arrays.asList(staffs);
	staffsList.add("Mary"); // UnsupportedOperationException
	staffsList.remove(0); // UnsupportedOperationException
```

如果想再做增删操作呢？将数组中的元素一个一个添加到列表，这样列表的长度就不固定了，可以进行增删操作。

```java
    List staffsList = new ArrayList<String>();
	for(String temp: staffs){
	  staffsList.add(temp);
	}
	staffsList.add("Mary"); // ok
	staffsList.remove(0); // ok
```

>数组转Set

```java
    String[] staffs = new String[]{"Tom", "Bob", "Jane"};
	Set<String> staffsSet = new HashSet<>(Arrays.asList(staffs));
	staffsSet.add("Mary"); // ok
	staffsSet.remove("Tom"); // ok
```

>List转数组

```java
    String[] staffs = new String[]{"Tom", "Bob", "Jane"};
	List staffsList = Arrays.asList(staffs);

	Object[] result = staffsList.toArray();
```

>List转Set

```java
    String[] staffs = new String[]{"Tom", "Bob", "Jane"};
	List staffsList = Arrays.asList(staffs);

	Set result = new HashSet(staffsList);
```

>Set转数组

```java
    String[] staffs = new String[]{"Tom", "Bob", "Jane"};
	Set<String> staffsSet = new HashSet<>(Arrays.asList(staffs));

	Object[] result = staffsSet.toArray();
```

>Set转List

```java
    String[] staffs = new String[]{"Tom", "Bob", "Jane"};
	Set<String> staffsSet = new HashSet<>(Arrays.asList(staffs));

	List<String> result = new ArrayList<>(staffsSet);
```












