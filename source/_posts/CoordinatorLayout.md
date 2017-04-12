---
title: CoordinatorLayout
date: 2017-04-12 20:46:01
tags: 学习
categories: android
---
[TOC]
# CoordinatorLayout
CoordinatorLayout 主要使用于以下两个场景
* 作为一个顶层父容器出现
* 协调一个或多个子View之间的交互

CoordinatorLayout 通过Behaviors指定子View的交互行为。

使用CoordinatorLayout需要导入design包
## 我进入的误区
才开始接手CoordinatorLayout，没有理清CoordinatorLayout的具体用途和使用场所。所以在学习过程中出现了很多不必要的问题。
1. 刚开始以为CoordinatorLayout和FrameLayout之类的布局一样，没有认清CoordinatorLayout的作用
2. 忽视了Behavior的重要性。
3. 不熟悉CoordinatorLayout，想着直接可以上手，忽视了对Demo的研究
## CoordinatorLayout简单使用
CoordinatorLayout的核心就是Behavior，Behavior就是定制子View的行为。
### 布局
CoordinatorLayout 继承与ViewGroup，但是最基本的表现形式和FrameLayout很相识。

也就是布局是重叠的从左上角开始。

如果要用好CoordinatorLayout,那么最基本的就是通过Behavior去控制。

### 实现效果
想通过CoordinatorLayout布局，实现顶层Toolbar加中间Fragment加底部菜单这种通用型的布局。

* 更换父布局为android.support.design.widget.CoordinatorLayout
* 按照网上的实例，对toolbar上封装一层AppBarLayout
* 添加frameLayout布局。
```xml
<FrameLayout
android:id="@+id/fragmet_layout"
android:layout_width="match_parent"
android:layout_height="match_parent"
android:background="#ffffffff"
app:layout_behavior="@string/appbar_scrolling_view_behavior">

</FrameLayout>
```
* 添加底部布局LinearLayout.因为CoordinatorLayout表现形式和FrameLayout很相识，所以底部布局我直接增加底部对齐

很好，效果实现了。效果图如下：
![image](http://note.youdao.com/yws/public/resource/b6ad28b3c1f377a862b1603d1cdff24b/xmlnote/ECB8DA547F484500BED50BE1DBCEC046/10027)

### 疑问
* 对于网上的事例，我首先的问题是，Toolbar必须用AppBarLayout封装么？

我动手删掉了AppBarLayout，然后运行效果是：Toolbar直接不显示了！好诡异，开始纠结这个问题。半个小时后发现，Toolbar被FrameLayout给覆盖住了..

但是结果还是不对啊？难道要我主动对frameLayout增加一个marginTop?如果我不知道toolbar的高度，或者不好获取这个高度，那不是坑大了？

* 对比发现唯一的不同之处就是多了layout_behavior属性。这个属性是干什么的？

看到这里。要感谢大神写的博客[CoordinatorLayout的使用如此简单](http://blog.csdn.net/huachao1001/article/details/51554608).

因为发现各种的坑，又不明白原理。基本快对CoordinatorLayout打差评的我，发现这个博客，然后一步一步研究这个，并且根据网上各种人的说法，把重心放在Behavior上。然后研究behavior的layoutDependsOn和onDependentViewChanged方法。
## Behavior
最主要的两个方法：
![image](http://note.youdao.com/yws/public/resource/b6ad28b3c1f377a862b1603d1cdff24b/xmlnote/43DC93CC54244A92813AFEE396E52207/10055)
可以看到大神的介绍：
* layoutDependsOn主要是判断child布局是否依赖当前这个View。
* onDependentViewChanged主要是当依赖的View改变时，这个child布局改变动作

看到这里，我突然有点明白了，CoordinatorLayout是协调一个或多个子View之间的交互。那么FrameLayout在Toolbar下面出现，这是不是也是一种依赖关系呢？

如果是我想的这样，那么我会在layoutDependsOn检查当dependency是Toolbar的时候，就返回true

在onDependentViewChanged的方法中，获取dependency的高度，然后动态的把这个高度设置给child,也就是FrameLayout。

## AppBarLayout
懒得动手，我选择直接去找AppBarLayout中是如何实现Behavior中的两个方法的。打算复制过来看看。
![layoutDependsOn](http://note.youdao.com/yws/public/resource/b6ad28b3c1f377a862b1603d1cdff24b/xmlnote/039C765856384713BFF2335064377855/10075)

layoutDependsOn实现很简单粗暴。dependency是AppBarLayout,那么就返回true
![onDependentViewChanged](http://note.youdao.com/yws/public/resource/b6ad28b3c1f377a862b1603d1cdff24b/xmlnote/D5F323AC4E8E42A3A4CDF20F2252D711/10084)

![offsetChildAsNeeded](http://note.youdao.com/yws/public/resource/b6ad28b3c1f377a862b1603d1cdff24b/xmlnote/ACFD33793BDA47668643CB3D66FD053C/10086)

虽然没用过ViewCompat.offsetTopBottom方法，但是大概理解这个方法的意思。好吧，我的猜想完全是正确的。

## 代码实现
```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
xmlns:app="http://schemas.android.com/apk/res-auto"
android:layout_width="match_parent"
android:layout_height="match_parent">

<FrameLayout
android:id="@+id/fragmet_layout"
android:layout_width="match_parent"
android:layout_height="match_parent"
android:background="#00ffffff"
app:layout_behavior="com.xiaoqiang.view.MyBehavior">

</FrameLayout>

<include layout="@layout/tool_bar_base" />

<LinearLayout
android:id="@+id/rl_bottom_menu"
android:layout_width="match_parent"
android:layout_height="80dp"
android:layout_alignParentBottom="true"
android:layout_gravity="bottom"
android:orientation="horizontal">

<Button
android:id="@+id/btn_first"
android:layout_width="0dp"
android:layout_height="match_parent"
android:layout_weight="1"
android:text="first" />

<Button
android:id="@+id/btn_second"
android:layout_width="0dp"
android:layout_height="match_parent"
android:layout_weight="1"
android:text="second" />

<Button
android:id="@+id/btn_three"
android:layout_width="0dp"
android:layout_height="match_parent"
android:layout_weight="1"
android:text="three" />
</LinearLayout>
</android.support.design.widget.CoordinatorLayout>

```
app:layout_behavior="com.xiaoqiang.view.MyBehavior",这里我自定义了一个MyBehavior.

```java
public class MyBehavior extends CoordinatorLayout.Behavior<FrameLayout> {

public MyBehavior(Context context, AttributeSet attrs) {
super(context, attrs);
}

@Override
public boolean layoutDependsOn(CoordinatorLayout parent, FrameLayout child, View dependency) {
return dependency instanceof ToolLinearLayout;
}

@Override
public boolean onDependentViewChanged(CoordinatorLayout parent, FrameLayout child, View dependency) {
ViewCompat.setPaddingRelative(child,0,dependency.getBottom() - child.getTop(),0,0);
return false;
}
}

```
layoutDependsOn中，当dependency是ToolLinearLayout的时候，直接返回true.这里我的想法是如果一个布局文件中有多个toolbar，那么直接使用会不会导致动作错乱。所以仿照了AppBarLayout。

执行后，效果果然和我的一样。对于CoordinatorLayout也才有了一点点的理解。感觉功能很强大！

![image](http://note.youdao.com/yws/public/resource/b6ad28b3c1f377a862b1603d1cdff24b/xmlnote/9C9445B702704C56B373E5912BA74C04/10118)

## 后记
在网上看博客过程中，也了解了一些CoordinatorLayout的细节。

* CoordinatorLayout只能对直接子view操作。
* CoordinatorLayout和一些新的控件配合，可以实现很好的效果。CoordinatorLayout是 Material Design的一种很好体现
