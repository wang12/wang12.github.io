---
title: handler初始化
date: 2016-11-28 14:51:42
tags: source
categories: android
---
# Handler
## 目标
归纳总结Handler、Looper、MessageQueue、Message直接的关系。文字记录下自己看源码过程。
## 学习过程
### Handler的主要作用
	通过文档学习，Hanlder的主要作用有两个：
	1. 定时执行消息调度。我的理解就是定时器的意思。
	( to schedule messages and runnables to be executed as some point in the future)
	2. 线程通讯.
	(to enqueue an action to be performed on a different thread than your own.)
### Handler初始化方法

Handler创建过程，其实就是对Handler内部绑定的Looper,MessageQueue,Callback和mAsynchronous进行赋值。所有的初始化方法有：

	1. Handler()
	2. Handler(Callback callback)
	3. Handler(boolean async)
	4. Handler(Callback callback, boolean async)
	5. Handler(Looper looper)
	6. Handler(Looper looper, Callback callback)
	7. Handler(Looper looper, Callback callback, boolean async)

虽然Handler的有7个，但是最终实现了的只有两个,分别是4和7。
其中，7的实现特别简单。就是分别对Looper，MessageQueue,Callback,和mAsynchronous赋值，其中，MessageQueue是从Looper中获取到的。

对于第4个初始化方法。Looper对象从线程中获取。调用Looper.myLooper()获取Looper对象。
### 我的问题
1. Callback 是干嘛的？

	说实话，自己在创建Hanlder的时候从来没有使用过有参数的构造方法。所以看到Callback，大脑中立即开始疑问。Callback是干嘛的？
	- 全局搜索，发现 Callback是Handler的内部接口，只有一个handleMessage方法。并且有一个返回值，所以猜测这个可能是为了处理Message消息的。
	- 全局搜索mCallback的调用地方，发现如下代码：
	
		```java
		    public void dispatchMessage(Message msg) {
		        if (msg.callback != null) {
		            handleCallback(msg);
		        } else {
		            if (mCallback != null) {
		                if (mCallback.handleMessage(msg)) {
		                    return;
		                }
		            }
		            handleMessage(msg);
		        }
		    }
		```
	- 当Message中的callback 不等于NUll的时候，Message中的Callback拦截了消息处理。
	- 当Callback不为NULL的时候，Callback先处理消息，并且它有能力拦截最后的消息。
	- 如果消息一直没有拦截掉，最后才交给handleMessage处理。
	
	```
		感觉懂的了什么？这个处理的逻辑，好像按钮点击事件响应。
		这样也就是说我一直的写法，用重写handleMessage方法的处理Message是最后一个收到消息的。
		而且: Message 居然也有处理它自己的办法？这个是以前没有想到的....
		在我的理解中，Message就是一个消息对象的封装而已...
		
	```
	用法：
		1). Handler可以在任意位置创建。然后只是在发送Message的时候，通过Message 直接处理自己的消息。
		2). 不需要直接传递Handler对象，可以通过传递callback对象的方式处理Handler的所有消息。甚至可以把一个Callback绑定到多个Handler上，然后统一处理Message消息
	
2. Looper.myLooper()干了些啥？

	通过Looper.myLooper()方法可以获取当前线程中的Looper对象，这个又是如何做到的呢？在线程Thread中是不可能有Looper信息的，那么Looper又通过什么和线程信息绑定的呢？
	- 首先看到的是ThreadLocal对象。通过查API文档，发现ThreadLocal是线程局部变量的意思。额...感觉自己稍微理解了如何在Thread中保持Looper信息。
	- 在一个新的线程中，如果要创建Looper对象，必须调用prepare方法。在prepare中调用sThreadLocal.set(new Looper))让ThreadLocal持有Looper对象。
	- 通过ThreadLocal.set方法，发现每一个Thread对象都绑定了一个ThreadLocalMap。而通过一个全局的ThreadLocal对象获取每一个线程中的Looper对象。这样就实现了线程和Looper的绑定，并且可以通过每个线程拿到对应的Looper。
	
	```java
	这一块我主要不理解的是如何通过通过Thread绑定每一个Looper对象。不过通过看源码感觉有点清晰。
	这样的话是不是我也可以通过一个全局的ThreadLocal对象去实现向每一个Thread传递一个共同的对象呢？
	如果通过不同的ThreadLocal对象,那是不是就可以通过Thread绑定无限制的数据...
	```
[参考大神博客：彻底理解ThreadLocal](http://blog.csdn.net/lufeng20/article/details/24314381)
3. mAsynchronous 是控制什么的？

	全局搜索mAsynchronous，发现调用的方法只有enqueueMessage。而enqueueMessage又是send方法调用。
	而使用mAsynchronous只是单纯的设置msg.setAsynchronous(true)。
	通过单词含义的猜测，这个设置应该是对Message处理的时候进行的一个标志。等到看发送模块的时候在看看!




