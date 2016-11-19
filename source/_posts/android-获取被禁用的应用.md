---
title: 获取被禁用的应用
date: 2016-11-19 15:41:56
tags: function
categories: android
---
# 功能开发
## 需求
1、问题

		在开发APP分享功能，使用ShareSDK 完成微信、朋友圈、QQ和QQ空间的分享。产品要求在手机上没有安装微信和QQ时，
	给出提示未安装这些应用。
		但是在三星一款手机上，系统内置微信应用。所以微信是不能卸载的，但是可以直接禁用这个应用。由此出现提示不准确问题。
2、需求
	
		除去检查APP是否被安装以外，还需要检查APP是否被禁用。如果被禁用那样同样提示用户没有安装这个应用。
## 解决过程
	遇到这个问题，我首先想到的时候这个禁用是Android系统自带的功能还是三星自己定制的功能。
	最后发现这个在其他手机上也有，不过需要你从应用信息中去停用，而不是像三星那样，在删除的时候可以直接选择停用。
	Android系统支持的功能，那么在API中就有说明。如此，我直接找API文档。
	第一反应到PackageInfo中寻找答案。通过测试以及看文档，发现这个中并没有关于应用是否被警用的信息。
	然后寻找其他的info，发现都没有相关的属性。难道是我想错了？
	我的理解是禁用功能应该是对某个APP增加一个标识，那么这个标识不是最应该保持在想关的包信息中的么？
	经过一番折腾，我又去看PackageManager。禁用的描述信息真好在这里可以获取到。看来我对PackageManager和PackageInfo的理解还是有误。
	在PackageManager中有一下三个信息：
```java
COMPONENT_ENABLED_STATE_DEFAULT//默认开启状态
COMPONENT_ENABLED_STATE_DISABLED//已被明确禁用
COMPONENT_ENABLED_STATE_DISABLED_UNTIL_USED//没有理解这个含义
COMPONENT_ENABLED_STATE_DISABLED_USER//用户已明确禁用应用程序
COMPONENT_ENABLED_STATE_ENABLED//明确指定启用？？
```
它们都对应setApplicationEnabledSetting(String, int, int)方法。既然能set,那么应该会有相应的get方法。去获取就行了
##解决方法
相应的代码如下

```java
final PackageManager packageManager = context.getPackageManager();// 获取packagemanager
		 List<PackageInfo> pinfo = packageManager.getInstalledPackages(0);// 获取所有已安装程序的包信息
		 if (pinfo != null) {
	           for (int i = 0; i < pinfo.size(); i++) {  
	               String pn = pinfo.get(i).packageName;  
	               if (pn.equals("com.tencent.mm")) {
					   if(packageManager.getApplicationEnabledSetting("com.tencent.mm") == 	PackageManager.COMPONENT_ENABLED_STATE_DISABLED_USER){
						   return false;
					   }
	                   return true;  
	               }
	           }  
	       }  
	       return false; 
```
## 注意事项
	之后在测试中，又发现一个BUG，原因是我在判断QQ是同时判断了com.tencent.mobileqq或com.tencent.qqlite中有一个存在，
	那么就去判断这两个是否有一个不可用。
	结果getApplicationEnabledSetting出现异常，导致程序崩溃。
	所以在使用getApplicationEnabledSetting方法时，必须保证这个APP在手机中是存在的，如此才可以
	进行禁用的判断！
