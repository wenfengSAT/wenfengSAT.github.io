---
layout:     post
title:      CompletableFuture线程池的使用总结
subtitle:   CompletableFuture线程池的使用总结
date:       2020-07-26
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - 并发
---

>CompletableFuture线程池的使用总结

```

java8新特性，异步线程，可以简化多线程的创建过程。
CompletableFuture.runAsync：异步执行没有返回值；
CompletableFuture.supplyAsync：异步执行有返回值；
CompletableFuture.thenApply：继续执行当前线程future完成的函数，不需要阻塞等待其处理完成；
CompletableFuture.thenApplyAsync：在不同线程池异步地应用参数中的函数；
CompletableFuture.thenCompose：用于多个彼此依赖的futrue进行串联起来
CompletableFuture.thenCombine：并联起两个独立的future，注意，这些future都是在长时间计算都完成以后

```


#### CompletableFuture.runAsync

```java

import java.util.concurrent.BlockingQueue;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.LinkedBlockingQueue;
import java.util.stream.IntStream;

import org.junit.Test;
import org.springframework.scheduling.annotation.Async;
import org.springframework.transaction.annotation.Transactional;

public class CompletableFutureTest1 {

	private static final BlockingQueue<String> queue = new LinkedBlockingQueue<String>();

	public BlockingQueue<String> createBlockingQueue() {
		return queue;
	}

	@Async
	@Transactional
	public void asyncDeal() {
		try {
			String reqStr = queue.take();
			doSth(reqStr);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}

	public void doSth(String reqStr) {
		System.err.println(reqStr);
	}

	@Test
	public void test() {
		CompletableFutureTest1 test1 = new CompletableFutureTest1();
		IntStream.range(1, 100).forEach(index -> {
			test1.createBlockingQueue().add(String.valueOf(index));
			try {
				CompletableFuture.runAsync(() -> {
					test1.asyncDeal();
				});
			} catch (Exception e) {
				e.printStackTrace();
			}
		});

	}

}

```

#### CompletableFuture.supplyAsync

```java

import java.util.concurrent.BlockingQueue;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.LinkedBlockingQueue;
import java.util.stream.IntStream;

import org.junit.Test;
import org.springframework.scheduling.annotation.Async;
import org.springframework.transaction.annotation.Transactional;

public class CompletableFutureTest2 {

	private static final BlockingQueue<String> queue = new LinkedBlockingQueue<String>();

	public BlockingQueue<String> createBlockingQueue() {
		return queue;
	}

	@Async
	@Transactional
	public String asyncDeal() {
		try {
			String reqStr = queue.take();
			return doSth(reqStr);
		} catch (InterruptedException e) {
			e.printStackTrace();
			return "error";
		}
	}

	public String doSth(String reqStr) {
		return reqStr;
	}

	@Test
	public void test() {
		CompletableFutureTest2 test2 = new CompletableFutureTest2();
		IntStream.range(1, 100).forEach(index -> {
			test2.createBlockingQueue().add(String.valueOf(index));
			// 异步处理
			CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
				try {
					return test2.asyncDeal();
				} catch (Exception e) {
					return "error";
				}
			});

			try {
				String response = future.get();
				System.out.println(response);
			} catch (Exception e) {
				e.printStackTrace();
			}
		});

	}

}


```
