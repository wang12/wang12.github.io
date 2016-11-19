---
title: seekBar镂空,透明效果
date: 2016-11-19 16:29:48
tags: function
categories: android
---
# 功能开发
## 需求

设计要求seekBar样式具体如下图所示：

![按钮中间透明](/images/seek_bar_hollow_out.png)

	以前我也自定义过seekBar。但是这种透明效果的还是第一次看到。



## 解决过程
首先自定义seek,实现颜色以及大小的变化。代码如下

```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@android:id/background">
                
        <shape>   
            <corners android:radius="1dip" />
            <solid android:color="#88ffffff" />
        </shape>
    </item>
    <item android:id="@android:id/progress">
        <clip>
            <shape>
                <corners android:radius="1dip" />
                <solid android:color="#e8ffffff"/>
            </shape>
        </clip>
    </item>
</layer-list>

```
配置seekbar的最基本的属性

```xml
android:maxHeight="1dp" //高度
android:minHeight="1dp"
android:progressDrawable="@drawable/letv_recorder_zoom_style"//进度条样式
android:thumb="@drawable/letv_recorder_zoom_slider"//按钮图片
```
接下来的镂空效果，我问了几个同事，大家一致的让我去看源码，自己也心动了，想去看源码。如果不行自己最后只能自己定义实现一个seekBar了。
	不过本人比较懒，所以先决定google一下。结果发现有一个android:splitTrack属性，但是看博客
	的意思是把这个值设置为false，好吧，这里坑了我好久。不过最后总算想到试试给为true~~~

只想说一句：Android开发的大神们太给力了。
## 代码
主要附上完整的seekbar属性代码

```xml
      <SeekBar
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginLeft="10dp"
                android:layout_marginRight="10dp"
                android:layout_toLeftOf="@id/iv_zoom_add"
                android:layout_toRightOf="@id/iv_zoom_decrease"
                android:maxHeight="1dp"
                android:minHeight="1dp"
                android:progressDrawable="@drawable/letv_recorder_zoom_style"
                android:splitTrack="true" //镂空效果
                android:thumb="@drawable/letv_recorder_zoom_slider"
                android:thumbOffset="0dp" />// 解决滑块在最左边被隐藏的问题，如果值过大，也可以完全隐藏的效果
```

感谢Android的贴身考虑！！

## 附
Android源码，实现原理就是绘制线的时候减去图片的大小

```java
 @Override
    void drawTrack(Canvas canvas) {
        final Drawable thumbDrawable = mThumb;
        if (thumbDrawable != null && mSplitTrack) {
            final Insets insets = thumbDrawable.getOpticalInsets();
            final Rect tempRect = mTempRect;
            thumbDrawable.copyBounds(tempRect);
            tempRect.offset(mPaddingLeft - mThumbOffset, mPaddingTop);
            tempRect.left += insets.left;
            tempRect.right -= insets.right;

            final int saveCount = canvas.save();
            canvas.clipRect(tempRect, Op.DIFFERENCE);
            super.drawTrack(canvas);
            canvas.restoreToCount(saveCount);
        } else {
            super.drawTrack(canvas);
        }
    }
```
