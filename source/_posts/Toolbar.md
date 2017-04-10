---
title: Toolbar
date: 2017-04-09 01:08:17
tags: 学习
categories: android
---

[TOC]
# Toolbar
## 介绍
 从Android L开始，Android官方为我们提供了一个新的工具栏Toolbar,这个工具栏代替了以前不好用的ActionBar.
 
 我自己从来没有用过这个，但是看到其他人使用Toolbar搭配Meterial Design实现各种好看的效果，忍不住心动想学一下。
 
 从官方文档中发现，Toolbar具有以下各种能力：
 * 一个导航按钮：返回、切换、导航等各种功能的一个按钮
 * 一个logo图片：可以任意设置宽高的logo图片
 * 标题和副标题：
 * 一个或多个自定义View
 * 菜单按钮
 
综上所述，这个Toolbar已经完全符合我们对工具栏的大部分要求了
## 最简单的Toolbar
 要使用toolbar
 1. 导入最新的V7包
 2. 继承AppCompatActivity或者使用AppCompatDelegate.
 3. 设置heme.AppCompat系列的主题，必须取消系统默认的ActionBar
 
 xml配置，通过<include>标签添加到你的布局中去
 ```xml
 <?xml version="1.0" encoding="utf-8"?>
 <android.support.v7.widget.Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
 android:layout_width="match_parent"
 android:layout_height="wrap_content"
 android:background="?attr/colorPrimary"
 android:minHeight="?attr/actionBarSize">
 
 </android.support.v7.widget.Toolbar>
 ```
 java:设置当前toolbar代替ActionBar。经过这一步的设置，最明显的是你可以看到系统默认会把当前APPName显示在工具栏上。这样设置以后你可以可以通过getSupportActionBar去直接获取。这一步必须要有
 
 ```java
 toolbar = (Toolbar) findViewById(R.id.tool_bar);
 setSupportActionBar(toolbar);
 ```
 ## 更多样式
 ### 设置导航按钮
 1. 在子Activity开启导航功能
 ```java
 getSupportActionBar().setDisplayHomeAsUpEnabled(true);
 ```
 2. 设置导航按钮图片
 
 我通过样式设置，也可以直接通过代码设置
 ```xml
 <!--getSupportActionBar().setHomeAsUpIndicator(R.drawable.back);-->
 <xmlns:app="http://schemas.android.com/apk/res-auto"
 app:navigationIcon="@drawable/back">
 ```
 3. 其他样式
 * contentInsetStartWithNavigation 设置到标题间的距离
 4. 事件监听
 
 注意：一定要放在setSupportActionBar方法之后设置
 ```java
 toolbar.setNavigationOnClickListener(new View.OnClickListener() {
 @Override
 public void onClick(View v) {
 
 }
 });
 ```
 5. 如何不显示
 
 调用getSupportActionBar().setDisplayShowTitleEnabled(false);即可不显示标题
 ### 标题和副标题
 1. 可以通过xml 和 java代码设置
 ```xml
 <app:title="首页" 
 app:subtitle="Home">
 ```
 2. 其他设置
 ![image](http://note.youdao.com/yws/public/resource/b6ad28b3c1f377a862b1603d1cdff24b/xmlnote/WEBRESOURCEa13d070e82c79f7520661fcc76d006d5/9719)
 
 ### logo图片
 1. 设置
 ```xml
 <app:logo="@drawable/logo">
 ```
 ### 菜单按钮
 1. 建立res/menu/menu_base.xml
 
 注意，android:showAsAction会报错，这个不需要管，主要是为了解决在一些手机中图标不显示的问题
 ```xml
 <menu xmlns:android="http://schemas.android.com/apk/res/android"
 xmlns:app="http://schemas.android.com/apk/res-auto"
 xmlns:tools="http://schemas.android.com/tools"
 tools:context=".MainActivity">
 
 <item android:id="@+id/action_edit"
 android:title="搜索"
 android:orderInCategory="80"
 android:icon="@drawable/search"
 app:showAsAction="ifRoom"
 android:showAsAction="ifRoom"/>
 
 <item android:id="@+id/action_settings"
 android:title="设置"
 android:orderInCategory="100"
 app:showAsAction="withText|ifRoom"
 android:showAsAction="withText|ifRoom"/>
 </menu>
 ```
 2. 在Activity中监听事件
 注意：一定要放在setSupportActionBar方法之后设置
 ```java
 toolbar.setOnMenuItemClickListener(new Toolbar.OnMenuItemClickListener() {
 @Override
 public boolean onMenuItemClick(MenuItem item) {
 Toast.makeText(BaseActivity.this, "点击的菜单", Toast.LENGTH_SHORT).show();
 return true;
 }
 });
 ```
 3. 重写Activity的onCreateOptionsMenu方法
 ```java
 @Override
 public boolean onCreateOptionsMenu(Menu menu) {
 getMenuInflater().inflate(R.menu.menu_base,menu);
 return true;
 }
 ```
 ## 自定义View
 主要的自定义方式，就是把Toolbar当做一个最简单的ViewGroup去使用。常用到需要自定义的地方如:居中的标题显示
 ```xml
 <?xml version="1.0" encoding="utf-8"?>
 <android.support.v7.widget.Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
 xmlns:app="http://schemas.android.com/apk/res-auto"
 android:layout_width="match_parent"
 android:layout_height="wrap_content"
 android:background="?attr/colorPrimary"
 app:navigationIcon="@drawable/back"
 app:contentInsetStartWithNavigation="20dp"
 app:contentInsetLeft="0dp"
 app:contentInsetStart="0dp"
 app:title="首页"
 app:subtitle="Home"
 app:logo="@drawable/logo"
 android:minHeight="?attr/actionBarSize">
 <TextView
 android:layout_width="wrap_content"
 android:layout_height="wrap_content"
 android:gravity="center"
 android:layout_gravity="center"
 android:text="居中的主题"
 />
 </android.support.v7.widget.Toolbar>
 ```
 ![image](http://note.youdao.com/yws/public/resource/b6ad28b3c1f377a862b1603d1cdff24b/xmlnote/A6842C7FCB404B65B29DF3605578D4A7/9788)
