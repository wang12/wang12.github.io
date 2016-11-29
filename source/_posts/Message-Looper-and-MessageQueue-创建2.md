---
title: Looper and MessageQueue创建
date: 2016-11-29 10:31:13
tags: source
categories: android
---
# Looper and MessageQueue
## Looper
### 描述
	Looper主要是在线程中开启一个循环队列。
	就像Android API文档中描述的那样。调用prepare()创建，调用loop开始循环。
### 创建
```java
1. Looper.prepare(); 
2. Looper.prepareMainLooper(); 
// API 文档中说了，这个方法是系统调用的。我们永远不能调用这个方法。
```
	1. 在prepare中，首先判断的是当前线程中是否已经使用过Looper.如果已经存在一个就抛出异常。这个使用的是ThreadLocal进行判断的额。
	2. 在构造方法中创建了MessageQueue和获取当前线程的对象
	3. 在loop中，主要是从MessageQueue中取出每一个Message。在获取Message中保留的target中的Handler对象,分发每一个Message。
### 我的问题
下面这段代码干嘛的？

```java
Printer logging = me.mLogging;
if (logging != null) {
   logging.println(">>>>> Dispatching to " + msg.target + " " +msg.callback + ": " + msg.what);
}
```
	通过代码测试了一下，发现是调试日志的使用方式。
	这种方式是好还是不好？
	在编写SDK过程中，我是不是也可以使用这种形式去让其他开发者处理调试日志呢？
## MessageQueue
### 描述
	一个先进先出的队列
### 创建
	在Looper创建的时候，会new 一个新的MessageQueue。
	一个消息，add到MessageQueue的位置：
		在Handler中，是调用MessageQueue.enqueueMessage(Handler,long)方法把每一个Message添加到队列中。
### 我的问题
1. MessageQueue都有哪些方法。感觉不能自己单独使用MessageQueue

	```java
		1.addIdleHandler(MessageQueue.IdleHandler handler)
		2.removeIdleHandler(MessageQueue.IdleHandler handler)
		3. addOnFileDescriptorEventListener(FileDescriptor fd, int events, MessageQueue.OnFileDescriptorEventListener listener)//看不懂
		4. removeOnFileDescriptorEventListener(FileDescriptor fd)
		5. isIdle() // 判断当前Looper是闲置的，也就是MessageQueue
	```

	```java
	/**
	  * 回调。判断当前MessageQueue是否处于空闲状态。
	  * return true:不自动调用removeIdleHandler。
	  *        false: 自动调用removeIdleHandler.
	  */
	public static interface IdleHandler {
        boolean queueIdle();
    }
	```
2. 如何实现时间相关的添加？

	新加入的Message按执行时间插入到原有的队列中，然后根据情况调用nativeAwake函数。
	
[大神博客：Android7.0 MessageQueue](http://blog.csdn.net/gaugamela/article/details/52599512)