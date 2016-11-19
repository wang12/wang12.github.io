---
title: TextView中文标点符号会导致直接换行问题
date: 2016-11-19 11:58:41
tags: bug
categories: android
---

# BUG修复
## 问题产生
1. 问题描述

		编程过程中，对于一部分异常提示需要弹出对话框进行提示。因为对话框风格在每个APP上都是唯一的。
		所以我把APP中所有的对话框进行了封装。对于提示语，最大情况下只有两行，所以对话框的宽度和高度都是固定的。
		结果在Lenovo k30-T手机上出现问题，有一个提示语居然显示不全。

2. 问题手机

		手机型号：Lenovo k30-T

## 问题查找过程
	这个问题其实很常见，但是自己记不得答案了。
	所以我直接询问同事，同事告诉我是中文符号导致的，一般情况下需要进行中文转义处理。所以我又从百度上查找所有的解决办法。

## 解决办法
1. 自定义TextView
2. 将TextView内容所有字符转换为半角字符
3. 把中文标点替换为英文标点符号。

我使用了第二种解决办法。代码如下：

```java
public static String ToDBC(String input) {  
   char[] c = input.toCharArray();  
   for (int i = 0; i< c.length; i++) {  
       if (c[i] == 12288) {  
         c[i] = (char) 32;  
         continue;  
       }if (c[i]> 65280&& c[i]< 65375)  
          c[i] = (char) (c[i] - 65248);  
       }  
   return new String(c);  
}  
```

## 原因
	具体原因需要看源码。这个也只能等待后续记起来的时候看看。不过百度的时候原因如下：
	android源码中对换行的处理问题，Android换行算法参考Unicode的线断算法，对于字符显示在行首和行尾有严格控制，源码见StaticLayout。

注：我主要参考以下大神的博客：

	http://niufc.iteye.com/blog/1729792
	http://trinea.iteye.com/blog/1486801
