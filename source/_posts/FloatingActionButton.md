---
title: FloatingActionButton
date: 2017-04-25 20:13:47
tags: 学习
categories: android
---
[toc]
# FloatingActionButton
google出的一个Material design分割悬浮按钮。特殊的运动、变形行为、锚定点的转移。

悬浮按钮两种规格：默认的，小的。该尺寸通过fabSize属性设置。

## 个性属性

android.support.design:fabSize : 图标大小
* mini 小按钮
* normal 正常按钮
* ffffff 具体的尺寸

android.support.design:elevation ：阴影大小

* 尺寸。默认是有阴影，设置为0dp可以取消阴影

android.support.design:rippleColor 设置点击时的颜色，这个在material design下的效果是一个渐变的。比如设置为绿色，那么从默认的蓝色渐变为绿色

* 颜色值

android.support.design:useCompatPadding 是否兼容padding属性
* 默认为FALSE ,不建议使用，亲测发现点击时背景变为方形

android.support.design:backgroundTint 背景颜色
android.support.design:pressedTranslationZ 点击时阴影的大小，发现也是一个渐变的效果

android.support.design:layout_anchor 设置描点,相对于那个控件位置

android.support.design:layout_anchorGravity 相对于描点的居中方式

## 出现的问题

* 我在4.3的手机上测试，发现点击后颜色有一个边框，所以需要app:borderWidth="0dp"把边框取消掉




