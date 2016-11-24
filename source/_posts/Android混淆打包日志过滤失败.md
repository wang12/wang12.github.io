---
title: Android混淆打包日志过滤失败
date: 2016-11-24 17:38:57
tags: bug
categories: android
---
# BUG修复
## 问题产生
1. 问题描述
	
	APP要上线了，但是在测试时，打印了很多关于安全性的日志，而且使用了很多其他的SDK，这些SDK中也有一部分日志。
	所以想到了在混淆时，直接删除所有的日志信息。
	
2. 混淆代码
	我使用的混淆代码如下：
	
	```java
	-optimizationpasses 5
-dontusemixedcaseclassnames
-dontskipnonpubliclibraryclasses
-verbose
-ignorewarnings
-optimizations !code/simplification/arithmetic,!field/*,!class/merging/*
-keepattributes Signature
-keep public class * extends android.app.Activity
-keep public class * extends android.app.Application
-keep public class * extends android.app.Service
-keep public class * extends android.content.BroadcastReceiver
-keep public class * extends android.content.ContentProvider
-keep public class com.android.vending.licensing.ILicensingService
-keep class android.support.** {*;}
-keep public class * extends android.view.View
-keep class **.R$* {
 *;
}
-assumenosideeffects class android.util.Log{
    public static *** d(...);
    public static *** v(...);
    public static *** i(...);
    public static *** w(...);
    public static *** e(...);
}
	```
3、开发环境

	Android stdio
	
## 问题查找过程
	1. 通过上面的代码，我在AS下打出正式包。但是日志还是继续在控制台下打印。通过对APK的反编译，我发现我的APK确实是混淆了的
	2. 在百度各种寻找，发现混淆代码就这么一种写法。对于网上说的混淆失败是使用-dontoptimize参数的问题。我这边并没有使用
	3. 无意间通过Eclipse测试打包，发现打包出来的日志确实没有在打印，所以觉得这个是AS打包的问题。

## 解决办法
	由于我的APK使用了其他的SDK，而那些SDK通过AS下载了很多其他的包。并不能在Eclispe上直接运行，需要进行大量的修改。
	所以只能考虑通过参数控制日志的输出。对于其他SDK中的日志就无能为力了。
	所以我新建Log类，通过全部项目中"android.util.Log"字符串的方式解决自己日志输出的问题。
	
## 原因

	在各大网站上搜索，并没有找到这个问题的原因。
	