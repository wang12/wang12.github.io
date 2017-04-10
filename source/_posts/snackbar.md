---
title: snackbar
date: 2017-04-11 00:14:48
tags: 学习
categories: android
---
[TOC]
# Snackbar 以及注意事项
## 介绍
Snackbar出现在Android5.1以后，按照官方的介绍：Snackbar为了给提供一种简单快捷的操作。

在使用snackbar时，我们要导入design包.

## 注意事项
1. 必须导入V7包并且使用AppCompatActivity
2. 在CoordinatorLayout下使用Snackbar才支持侧滑功能

## 最简单的Snackbar
```java
/**
* @param view 通过这个View查找父布局
* @param msg 需要显示的消息
* @params Snackbar.LENGTH_SHORT 显示时间，和toast一样
*/
Snackbar.make(view,msg,Snackbar.LENGTH_SHORT).show();
```
Snackbar会把自己加在符合条件的父层布局，至于什么是符合条件可以看下面的源码：
![image](http://note.youdao.com/yws/public/resource/b6ad28b3c1f377a862b1603d1cdff24b/xmlnote/599B4E4CBA4740419AF88D28B0DCE0A4/9823)
可以通过make方法的源码看到，如果view 或者View的父布局是CoordinatorLayout，那么直接加载CoordinatorLayout上。

如果是Framelayout,并且是content根布局，那么就显示在根布局上。

如果不是根布局,那么就暂存，继续找下一个。一直找到根布局

***因为Snackbar是添加到View中的，所以和Toast相比，Snackbar当Activity不可见的时候也会消失***

## Snackbar显示时间
* Snackbar.LENGTH_INDEFINITE 不消失显示，除非手动取消
* Snackbar.LENGTH_SHORT 短时间显示
* Snackbar.LENGTH_LONG 长时间显示
* 自定义一个毫秒值
这一点上，Snackbar比Toast多了一个不消失可以自定义时间
## Snackbar动作
### 添加一个简单的行为
![image](http://note.youdao.com/yws/public/resource/b6ad28b3c1f377a862b1603d1cdff24b/xmlnote/37E865A4BA7F471282B076E816CA769B/9854)
1. 添加一个string的资源ID当做动作触发点。
2. 直接添加一个字符串当做动作的触发点。
3. 设置动作的颜色，

### Snackbar主动动作
* show() 调用后显示Snackbar
* dismiss() 调用后隐藏Snackbar
* getContext() 获取父布局所使用的Context
* getDuration() 当前设置的超时时间
* getView() Snackbar所对应的SnackbarBaseLayout，自定义Sanckbar就靠它了
* isShown() 判断当前Snackbar是否正在显示
* isShownOrQueued() 判断当前Snackbar是否正在显示或者已经添加到队列里
* setDuration(int duration) 设置消失时间
### 监听Snackbar的状态
可以通过addCallback(BaseCallback<B> callback)监听Snackbar的显示和消失，通过removeCallback(BaseCallback<B> callback)清除这种监听。
```java
bar.addCallback(new BaseTransientBottomBar.BaseCallback<Snackbar>() {
@Override
public void onDismissed(Snackbar transientBottomBar, int event) {
Log.d("q","Snackbar 消时了");
}

@Override
public void onShown(Snackbar transientBottomBar) {
Log.d("q","Snackbar 显示了");
}
});
```
## 自定义Snackbar
### 修改背景色和修改msg字体颜色
![image](http://note.youdao.com/yws/public/resource/b6ad28b3c1f377a862b1603d1cdff24b/xmlnote/F20253A26A874AF485D5B969D7C3FEBA/9920)


通过源码发现，Snackbar加载了一个R.layout.design_layout_snackbar_include的布局文件。

打开这个布局文件
![image](http://note.youdao.com/yws/public/resource/b6ad28b3c1f377a862b1603d1cdff24b/xmlnote/11B3B843CF67433997A450D3259C103B/9926)
所以要改变背景色和msg字体颜色可以进行如下的操作
```java
bar.getView().setBackgroundColor(0xff0000ff);
((TextView)bar.getView().findViewById(android.support.design.R.id.snackbar_text)).setTextColor(0xffff0000);
```
### 自定义图标
Snackbar使用的是SnackbarLayout当做父布局，SnackbarLayout是一个FrameLayout。
所以可以通过如下代码增加View：
![image](http://note.youdao.com/yws/public/resource/b6ad28b3c1f377a862b1603d1cdff24b/xmlnote/E185D6700F0A4C63B4362CA24CCFAACD/9913)
```java
Snackbar bar = Snackbar.make(view,msg,10*1000);
Snackbar.SnackbarLayout layout = (Snackbar.SnackbarLayout) bar.getView();
ImageView image = new ImageView(bar.getContext());
image.setBackgroundColor(0x00ffffff); image.setImageResource(R.drawable.xingxing);
FrameLayout.LayoutParams params = new FrameLayout.LayoutParams(FrameLayout.LayoutParams.WRAP_CONTENT, FrameLayout.LayoutParams.WRAP_CONTENT);
params.gravity = Gravity.CENTER_VERTICAL|Gravity.RIGHT;
layout.addView(image,params);
bar.show();
```
最终的效果图：

![image](http://note.youdao.com/yws/public/resource/b6ad28b3c1f377a862b1603d1cdff24b/xmlnote/5C55FF1DDB6E447CA053F881E872E47F/9956)
