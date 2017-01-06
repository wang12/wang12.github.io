---
title: handler post remove
date: 2016-12-17 18:53:32
tags: source
categories: android
---
# Handler post
## 描述
	在Handler使用中，可以通过各种方法把一个Message添加到MessageQueue中。
## 方法列表
	```java
	1. post(Runnable r)
	2. postAtFrontOfQueue(Runnable r)
	3. postAtTime(Runnable r, long uptimeMillis)
	4. postAtTime(Runnable r, Object token, long uptimeMillis)
	5. postDelayed(Runnable r, long delayMillis)

	6. sendEmptyMessage(int what)
	7. sendEmptyMessageAtTime(int what, long uptimeMillis)
	8. 	sendEmptyMessageDelayed(int what, long delayMillis)
	9. sendMessage(Message msg)
	10. sendMessageAtFrontOfQueue(Message msg)
	11. sendMessageAtTime(Message msg, long uptimeMillis)
	12. sendMessageDelayed(Message msg, long delayMillis)

	//Message 
	13. sendToTarget()
	```
内部方法调用

	1  -> 12 -> 11
	2  -> 10
	3  -> 11
	4  -> 11
	5  -> 12 -> 11
	6  -> 8  -> 12 -> 11
	7  -> 11
	9  -> 12 -> 11

可以看到，方法最终调用到 11和10 这两个方法。其他的参数都是为了向Message中添加元素。


sendMessageAtFrontOfQueue(Message msg)	
//把这个Message放到最前面。也就是下一个就会立即执行Message 

 * <font color="red">This method is only for use in very special circumstances -- it can easily starve the message queue, cause ordering problems, or have other unexpected side-effects.</font>
 
 大概意思是这个方法用在一些特殊的情况下。不过可能会导致消息队列锁死或者导致消息排序问题。总结下来就一句话，别用这个方法

sendMessageAtTime(Message msg, long uptimeMillis)
//在一个确定的时间执行Message

* <font color="blue">注意，这里的时间是手机启动至今运行的时间，并不是其他的绝对时间。所有使用时一定要注意，如果你发现使用这个方法，但是立即执行了Message或者过了好久好久也不执行。那也就理所当然了。</font>
* <font color="red">如果你对这里的uptimeMillil设置为0，其实和sendMessageAtFrontOfQueue 执行一样。这里应该也是不安全的</font>

sendEmptyMessageDelayed

 //这个方法其实还是调用了sendMessageAtTime 方法。只不过唯一的区别是使用delayed自己计算了绝对时间。计算方式就是"SystemClock.uptimeMillis() + delayMillis"。
 
 ```java
 //返回一个毫秒值，表示系统自启动以来的时间，不包括深度睡眠时间
 SystemClock.uptimeMillis()
 ```
 ---
# Handler remove
## 描述
移除消息是很重要的。比如你通过postDelay做一个定时器，然后定时器在OnResume中启动。但是你在onStop中不退出会如何。
答案就是你在后台回来的时候，发现定时器完全乱了。。
## 方法列表
```java
removeCallbacks(Runnable r)
		--> mQueue.removeMessages(this, r, null);
removeCallbacks(Runnable r, Object token)
		--> mQueue.removeMessages(this, r, token);
removeMessages(int what)
		--> mQueue.removeMessages(this, what, null);
removeMessages(int what, Object object)
		--> mQueue.removeMessages(this, what, object);
removeCallbacksAndMessages(Object token)
		--> mQueue.removeCallbacksAndMessages(this, token);
```
removeIdleHandler(IdleHandler handler) ???? 

可以看到，所有的移除方法都是调用了消息队列中的移除。一共分为三类，根据Runnable移除、根据 what和object 移除以及根据token移除

* removeMessages(Handler h, Runnable r, Object object)
	
	1. 首先保证handler和Runnable 都不为空
	2. 接下来判断当前正要处理的Message是不是你要移除的，如果是，那么指向下一个Message，并且回收当前Message
	3. 接下来循环处理所有Message消息，如果handler,runnable,和Message.obj和传入的一样，那么全部回收掉message 
	4. 所谓的从消息队列中移除，只不过是把前一个Message的指针指向下下一个Message。这样就丢掉了符合条件的Message

* removeMessages(Handler h, int what, Object object)
	
	这个方法的是实现方式和第一个完全一样，只是更换了判断标准

* removeCallbacksAndMessages(Handler h, Object object)

	如果object为空，那么可以移除当前Handler中的所有Message消息。直接使用post(Runnable),实际上还是封装成一个Message消息。

----
在看MessageQueue的时候，突然间发现两个没有使用过的方法。

```java
Looper.myQueue().addIdleHandler();
Looper.myQueue().removeIdleHandler();

```
通过各种网络资源，说是添加的IdleHandler是在MessageQueue线程中没有Message消息，或者说空闲时间会使用。

最常规的用法，在Actviity中添加这个。
当Activity显示出来的时候就会回调。如果返回false。那么只会回调一次。

我测试发现，一般是在onResume之后回调。
 


