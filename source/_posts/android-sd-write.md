---
title: Android写SD卡写文件失败
date: 2016-11-18 22:15:49
tags: bug
categories: android
---
#BUG修复
## 问题产生
1、异常代码

```java
File Storage = Environment.getExternalStorageDirectory();
File tmepfile = new File(Storage, "/Android/data/包名/***");
if (! tmepfile.exists())
	tmepfile.mkdirs();
```
2、问题手机

	型号：m3 note
	系统：Android 5.0
	
3、问题描述
	
	上面的创建文件代码，没有任何的异常产生。但是检查结果发现，文件居然没有创建成功。
	而同样的代码在大部分手机上运行都是成功的。所以对于结果不明白为什么。
## 问题查找过程
1. 在项目中遇到这个问题，我首先怀疑的是是不是权限问题，但是经过检查发现权限并没有错误
2. 接着我百度各种错误原因，但是并没有发现什么相关的信息，只是无意间看到别人使用“mnt/sdcard/0”作为根路径创建成功。
3. 发现SD卡下出现一个0文件夹，然后里面内容和我创建的一致。
4. 我直接在Activity.getFilesDir()下面创建文件夹成功

## 解决办法
		对于一个项目而言，解决办法是很简单。我如果在判断文件没有创建成功的时候。那么主动在
	“/Android”前面加一个上级目录“/temp”，这样在m3手机上测试是可以用的。
	
## 原因

	事后，我猜测可能的原因有：
	1. 这个手机是双内存卡的？？
	2. 是否是调试模式导致的？？
	当然我没有详细调研过原因。
	
如果找到具体的原因，我会补充自己这个BUG记录的
