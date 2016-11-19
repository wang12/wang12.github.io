---
title: 第一次安装应用，Home键应用多次启动问题
date: 2016-11-19 12:53:42
tags: bug
categories: android
---
# BUG修复
## 问题产生
1. 问题描述

	第一次安装应用后，直接点击打开按钮，打开APP。然后进入APP的二级界面，按下HOME键。接下来通过点击图标打开APP。你会惊奇的发现，APP居然在一级界面上。
	
	（在我的APP中，接下来的操作出现各种奇葩的错误）
	
2. 问题手机

	所有手机都会有

## 问题查找过程
1. 这个问题被提出过好多次，开始我一直以为是某一个界面出现的问题，所以一直在解决Activity模式的原因。
2. 后来，这个问题被测试妹子准确定位。重现步骤也已经明确，所以才明确问题是什么。
3. 通过下载其他的APP，发现有的也有这个问题，有的APP没有，觉得这个应该能解决。
4. 百度
	
## 解决办法
	在主Activity中添加如下代码：
	if((getIntent().getFlags() & Intent.FLAG_ACTIVITY_BROUGHT_TO_FRONT) != 0){
			finish();
			return;
	}
	
## 原因
	通过查找，很多大神都说是：
	1、安装器打开应用flag为FLAG_ACTIVITY_NEW_TASK，launcher打开应用比系统安装器多了FLAG_ACTIVITY_RESET_TASK_IF_NEEDED。
	2、因为系统启动模式是standard：打开多次MainActivity会在原有任务栈中叠加出现多个。
	
这个问题理论应该很常见，但估计也是很多Android开发忽略掉的问题，这里我一定要注意这个问题。

###最后，感谢大神博客：

http://blog.csdn.net/zhangcanyan/article/details/52777265
