---
title: View 滑动效果
date: 2017-02-08
tags: function
categories: android
---
[TOC]
# View
## 实现View滑动的方式
主要分为以下三种方式

```graphLR
View[View 滑动方式] -->|内容滑动|a(使用ScrollTo和ScrollBy)
View --> b(使用动画的方式)
b --> |3.0以上版本|e[属性动画]
b --> |只是影像滑东,事件还在原来位置|f[传统动画]
View --> c(通过改变布局参数)
```
![View动画流程图](/images/view_huadong.png)
### 使用ScrollTo 和 ScrollBy
 只能实现View内容在View内的变动。
 ```java
 scrollTo(int x,int y)//绝对距离的滑动
 scrollBy(int x,int y)//相对距离的滑动
 ```
 1. 使用这种方式处理内容滑动，为了达到平滑的效果，一般会借助Scroller类，具体的方法如下：
 ```java
 public TestView extends View{
    //Scroller 本身不能操作View的滑动，它只是根据我们设定的初始化位置和结束位置以及滑动时间，帮我们计算每个时间点的具体位置。
     Scroller mScroller = new Scroller(mContext);
     private void smoothScrollTo(int destX,int destY){
         int scrollX = getScrollX();
         int deltaX = destX - scrollX;
         //初始化Scroller的起始和结束位置还有时间
         mScroller.startScroll(scrollX,0,deltax,0,1000);
         //开始重绘
         invalidate();
     }
     /**
      * View每次绘制调用OnDraw()方法的时候，会先调用这个方法。这个方法在父类中* 是空实现的
      */
     @Override
     public void computeScroll(){
        //计算时间。返回值表示当前滑动是否已经结束。
         if（mScroller.computeScrollOffset()){
            //调用内部方法开始滑动了
             scrollTo(mscroller.getCurrX(),mScroller.getCurrY());
             //开始下一次的重新绘制
             postInvalidate();
         }
     }
 }
 
 ```
 ### 使用动画的方式
 #### 属性动画
    通过操作"translationX"和"translationY"属性去进行平滑的滑动。使用ObjectAnimator.ofFloat()进行处理。
    
* **衍生介绍 ValueAnimator 类**
这个类主要是设定一个范围和时间，然后数字平滑的在这个时间中变动，也可以设定变动规则(比如加速度)。

```java
ValueAnimator animator = ValueAnimator.ofInt(0,1).setDuration(1000);
animator.addUpdateListener(new AnimatorUpdateListener(){
   @Override
   public void onAnimationUpdate(ValueAnimator animator){
      float fraction = animator.getAnimatedFraction();
      mButton.scrollTo(startX+(int)(deltaX * fraction),0);
   }
});
animator.start();
```
 #### 常规动画
 常用的translate各种帧动画效果
### 改变布局参数
通过设定LayoutParams的Margin的方法，实现动画的效果