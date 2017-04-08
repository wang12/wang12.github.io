---
title: AppCompatActivity
date: 2017-04-09 01:08:17
tags: 学习
categories: android
---
[TOC]
# AppCompatActivity
官方介绍：

从Android 21之后引入Material Design的设计方式，为了支持Material Color 、调色板、toolbar等各种新特性，AppCompatActivity就应用而生。
代替了原有的ActionBarActivity。在AppCompatActivity中，更是引入了AppCompatDelegate类的设计，
可以在普通的Acitivity中使用AppCompate的相关特性。

## 可以用AppCompatActivity实现什么功能
### 自定义调色板
使用AppCompatActivity必须继承Theme.AppCompat样式。然后就可以使用colorPrimary，colorPrimaryDark和colorAccent进行相应的设置。
```xml
<style name="Base.AppTheme" parent="Theme.AppCompat">
<!--Android系统自带标题栏颜色,一般情况下，我们都会设置为去除标题栏-->
<item name="colorPrimary">#ff00ff00</item>
<!--标题栏的字体颜色-->
<item name="android:textColorPrimary">#ff000000</item>
<!--系统状态栏颜色-->
<item name="colorPrimaryDark">#ffff0000</item>
<!--EditText编辑、RadioButton和CheckBox选中时的颜色-->
<item name="colorAccent">#ff0000ff</item>
<!--EditText、RadioButton和CheckBox等预设颜色-->
<item name="colorControlNormal">#ff00ff00</item>
<!--去除标题栏-->
<item name="windowNoTitle">true</item>
<item name="windowActionBar">false</item>
<!--只是一个主窗口的颜色，必须使用这种引用的方式设置颜色-->
<item name="android:windowBackground">@android:color/white</item>
<!--会影响所有的颜色-->
<item name="android:colorBackground">#FF00ff00</item>
<!--前景色，为什么字体颜色不发生改变呢？？-->
<item name="android:colorForeground">#ff000000</item>
<!--底部按钮的颜色，必须在5.0及以上系统才会生效-->
<item name="android:navigationBarColor">#ff099099</item>

<!--还是状态栏的颜色，只有在5.0以上系统才会生效-->
<!--<item name="android:statusBarColor">@android:color/transparent</item>-->
<item name="android:textColor">#ff000000</item>
</style>
```
### Toolbar的支持
在以前Android系统推荐的是ActionBar,但是ActionBar的自定义效果不好。现在新的ToolBar可以完美的扩展。

* android:background="?attr/colorPrimary" 记得使用预定义样式。好像开发这么久都不用这种方式.......

```xml
<android.support.v7.widget.Toolbar
android:id="@+id/tool_bar"
android:layout_width="match_parent"
android:layout_height="wrap_content"
android:background="?attr/colorPrimary"
android:minHeight="?attr/actionBarSize">

</android.support.v7.widget.Toolbar>
```
详细的ToolBar使用还需要单独研究一下
### 好用的Snackbar
可以结合Snackbar使用，Snackbar可以代替Toast等使用，并且它可以在一定程度上代替对话框功能。

### 新的对话框样式
这个对话框相比较以前默认的，确实不是一个档次的啊。
```java
android.support.v7.app.AlertDialog.Builder builder 
= new android.support.v7.app.AlertDialog.Builder(this);
builder.setTitle("好看的对话框");
builder.setMessage("这个对话框真好看");
builder.setPositiveButton("OK", null);
builder.setNegativeButton("Cancel", null);
builder.show();
```
## 总结

AppCompatActivity除过自己可以设置一些Material风格的颜色，更多的是和Material风格的控件搭配使用，比如toolBar、Snackbar和AlertDialog等等。
如果这些控件要直接在Activity中使用，那么必须通过APPCompateDelegate去进行控制。
注意：使用AppCompatActivity或直接使用APPCompateDelegate，都必须使用Theme.AppCompat样式。

# 大神
[如何给非AppCompatActivity添加Toolbar](http://www.tuicool.com/articles/RnUzQ3B)

[用好AppCompatActivity](http://www.tuicool.com/articles/EfiYrue)
