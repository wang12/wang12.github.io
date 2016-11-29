---
title: Message创建
date: 2016-11-28 17:57:44
tags: source
categories: android
---
## Message
### 描述
	Message 是Handler数据的一个封装。
	Message 对象可以通过new,Message.obtain()或者Handler.obtainMessage()获取。
### 创建
1. 直接new一个Message,此时不包含任何的参数。
2. 通过Message.obtain系列方法获取一个。

	```java
	Message.obtain();
	Message.obtain(Message orig);
	Message.obtain(Handler h);
	Message.obtain(Handler h, Runnable callback);
	Message.obtain(Handler h, int what);
	Message.obtain(Handler h, int what, Object obj);
	Message.obtain(Handler h, int what, int arg1, int arg2);
	Message.obtain(Handler h, int what, 
	            int arg1, int arg2, Object obj);
	
	```

3. 通过Handler.obtainMessage 系列方法获取一个

	```java
	Handler.obtainMessage();
	Handler.obtainMessage(int what);
	Handler.obtainMessage(int what, Object obj);
	Handler.obtainMessage(int what, int arg1, int arg2);
	Handler.obtainMessage(int what, int arg1, int arg2, Object obj);
	```
	---
		1. 通过Handler创建Message方法时，其实还是调用到对应的Message.obtain方法中。
		2. 所有的方法都会执行Message.obtain。主要作用是进行Message的回收利用。所以比单独的new一个Message效率好
	---

### 我的问题
1. Message对象是如何实现回收利用的?

	有五个对象需要关注：sPoolSync,sPool,m.next,sPoolSize,MAX_POOL_SIZE:
	
	```java
	1). sPoolSync只是一个简单的Object对象，static final 描述，全局看都是通过synchronized去实现同步。
	2). 在回收时，next指向当前sPool，sPool指向当前Message。看来这两个参数是相当于列表的指针
	3). 判断sPoolSize小于MAX_POOL_SIZE。这个应该是最大保留的Message对象，最大50个。
	4). 每次使用时，当前Message中都有两个对象，一个指向上一次使用的Message.一个指向当前的可用的Message。
	当每次取一个之后，就会把队列中当前的Message向前移动一格。如果使用不频繁，那么每次都会使用一个。
	当频繁使用的时候就会使用好多个，最后使用完成又增加好多个Message，这样完成重用的机制。
	
	```
	这里有一个容易出问题，就是你不能直接调用Message的recycle方法。因为Looper.loop()方法中已经主动调用了这个。
	
	感谢下面大神的博客，不然我也一直想不通为啥我从来没有调用过recycle，还怀疑是自己使用错了呢。
	
	[浅谈 Recycle 机制](http://dourok.info/2014/07/15/quick-overview-of-recycling-pattern-in-android/)
	
	<font color=red>仿照Message实现对象的重用是一个很好的例子，但是要注意形成死循环。</font>读读源码真好，这个原理知道，但是以前想不到这个办法，以前能想到的创建一个队列去处理！！！
	
2. 在Message中为什么要保留Handler对象?

	如果只是单纯的通过Message类去看Handler对象的使用。那么在Message中保留Handler对象的第一个作用就是：
	
	```java
	//target 就是Handler对象
	 public void sendToTarget() {
        target.sendMessage(this);
    }
  ```
	第二个作用：(最主要作用)
	在Looper的loop循环中，会根据每个Message的Handler发送消息给自己的Handler。
	
	```java
	try {
         msg.target.dispatchMessage(msg);
	} finally {
         if (traceTag != 0) {
              Trace.traceEnd(traceTag);
         }
   } 
	```