---
title: Home键应用重启问题
date: 2016-11-19 12:21:42
tags: bug
categories: android
---
# BUG修复
## 问题产生
1. 问题描述
	
	项目开发测试过程中，发现在一个二级界面中，按下HOME键，然后再通过点击应用图标进入。发现应用又到了一级界面。
	
2. 问题手机
	
	所有手机都会出现

## 问题查找过程
	这个问题不是我所遇到的，但是是我查找另一个关于应用多次启动问题时发现的这个。以前自己也没有做
	过这方面的研究，但是看到这个问题后觉得很基础，所以在这里记一下。百度上有很多关于这个问题的描述，
	直接输入“Android android:launchMode="singleTask"”可以找到很多答案。

## 解决办法
	删除manifest文件中主Activity配置的android:launchMode="singleTask"模式。
	
## 原因
	这个问题主要和singleTask模式有关
	1、standard系统默认启动模式。当你多次调用startActivity时会启动多个Activity
	2、singleTop。如果你要启动的Activity在当前应用栈的栈顶，那么就不会重复启动。如果不在栈顶，那么就会重新启动一个。
	3、singleTask。如果你要启动的Activity在当前应用栈的栈顶，那么不会重复启动。如果不在栈顶，那么就关掉到栈顶的所有其他启动的Activity.
	4、singleInstance。新的Activity在一个单独的栈中
	
正因为这种特殊性，如果主Activity设置singleTask，那么你每次点击图标，其他的Activity都会被关掉，相当于应用重启，这个问题和Activity被启动多次不同，启动多次的问题可以看我的另一篇博客。