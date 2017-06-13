---
title: LibRTMP_Demo
date: 2017-06-13 15:40:15
tags: 学习
categories: android
---
[TOC]
# LibRtmp编译并导入Android Studio
## 简介
 做了好久的推流项目，但是没有接触过底层知识，所以只是相当于SDK的使用者。在工作不忙的时候，突然间想学一学LibRtmp。不求能完全学会，只希望能自己编译出一个可以推流的库。
## 编译环境
* LibRtmp编译
    1. Ubuntu 虚拟机：36~16.04.1-Ubuntu SMP Sun Feb 5 09:39:41 UTC 2017 i686 i686 i686 GNU/Linux

    2. Android NDK：android-ndk-r10e，这里注意：我的系统是32位的，所以这个也是32位的
* 导入Android Studio
    1. Android Studio:版本2.3
    2. 需要下载SDK Tools中的 CMake,LLDB,NDK.
## LibRtmp的编译

### 编译步骤
 1. 保证你虚拟机中搭建了完整的环境，可以先编译NDK中的Demo试试
 2. 编译LibRtmp需要linux中安装了Openssl。
    
    ```
    安装openssl
    sudo apt-get install openssl
    sudo apt-get install libssl-dev
    ```
**需要注意：** 我的ubuntu安装完成后，openssl在/var/include/openssl中。执行命令
```
 : openssl version
  OpenSSL 1.0.2g  1 Mar 2016
```
3. 下载librtmp
```shell
mkdir librtmp #选择创建自己的目录
git clone git://git.ffmpeg.org/rtmpdump
```
4. 配置编译环境。这一步主要是复制NDK中一些编译脚本到特定的位置。
```shell
/home/xiaoqiang/android-ndk-r10e/build/tools/make-standalone-toolchain.sh
--toolchain=arm-linux-androideabi-4.9 --platform=android-14
--install-dir=/home/xiaoqiang/workspace/librtmp/utils
```
5. 设置环境变量。把之前复制的脚本统一加入环境变量中

```shell
export PATH=/home/xiaoqiang/workspace/librtmp/utils/bin:$PATH
```
**
到了这一步，说明你的编译环境已经OK了。接下来开始编译工作。

编译LibRtmp需要先编译polarssl,所以我们开始polarssl的下载和编译工作
**
6. 下载polarssl并且做一个小改动

```shell
tar -zxvf polarssl-1.2.14-gpl.tgz #我下载的版本是1.2.14。
#改动

cd include/polarssl/config.h
#去掉 POLARSSL_HAVEGE_C 的注释

#define POLARSSL_HAVEGE_C
```
7. polarssl编译
```shell
1. 首先进入polarssl的目录中
2. 执行编译命令
make CC=arm-linux-androideabi-gcc APPS=
3. 执行安装命令
make install DESTDIR=/home/xiaoqiang/workspace/librtmp/utils/sysroot
```
**注意**
```
1. 如果提示 arm-linux-androideabi-gcc 命令找不到，重复4，5步骤试试。如果还不行，可以使用绝对路径(其他大神说的。我没有出现这个问题)
2.安装位置，必须是4，5步骤中设定的位置。否则会在编译中提示：
error: cannot find -lpolarssl

我解决第二个问题解决了好久，一直搞不明白
```

8. 编译静态库
```shell
make SYS=android CROSS_COMPILE=arm-linux-androideabi- INC="-I/home/xiaoqiang/workspace/librtmp/utils/sysroot/include" CRYPTO=POLARSSL
```
9.编译共享库
```shell
make SYS=android CROSS_COMPILE=arm-linux-androideabi- INC="-I/home/xiaoqiang/workspace/librtmp/utils/sysroot/include" CRYPTO=POLARSSL SHARED=
```
**注意**
```
1.我成功编译出了librtmp.a文件。但是在编译.so的时候出现了问题。并没有成功生成，也没有任何的提示。

可能的原因是：
网上有大神说是需要加入-shared参数
我找到所有的MAKEFILE文件，然后在LD_FLAGS参数后面加入了-shared。
很遗憾没有成功。

```
### 通过ndk-build编译共享库

参考大神博客：http://blog.csdn.net/a992036795/article/details/54377892

* 编写两个Android.mk文件
  1. 在rtmpdump目录中，文件内容如下
  ```
  LOCAL_PATH := $(call my-dir)
  subdirs := $(addprefix $(LOCAL_PATH)/,$(addsuffix /Android.mk, \ librtmp \ ))
  SSL := /home/blueberry/developer/android_tools/armeabi-4.9/sysroot
  ifndef SSL
  $(error "You must define SSL before starting")
  endif
  include $(subdirs)
  ```
  2. 在rtmpdump/librtmp目录中，新建一个Android.md文件。内容如下
  ```
    LOCAL_PATH:= $(call my-dir)

    include $(CLEAR_VARS)

    LOCAL_MODULE := polarssl
    LOCAL_EXPORT_C_INCLUDES := $(SSL)/include
    LOCAL_SRC_FILES := $(SSL)/lib/libpolarssl.a

    include $(PREBUILT_STATIC_LIBRARY)

    include $(CLEAR_VARS)

    LOCAL_C_INCLUDES += $(NDK_PROJECT_PATH)/librtmp \
    $(SSL)/include

    LOCAL_SRC_FILES:= \
        amf.c \
        hashswf.c \
        log.c \
        parseurl.c \
        rtmp.c

    LOCAL_STATIC_LIBRARIES = polarssl
    LOCAL_CFLAGS += -I$(SSL)/include -DUSE_POLARSSL
    LOCAL_LDLIBS += -L$(SSL)/lib -L$(SSL)/usr/lib
    LOCAL_LDLIBS += -lz

    LOCAL_MODULE := librtmp

    include $(BUILD_SHARED_LIBRARY)
  ```
  3. 新建jni/Application.mk文件。内容如下
  ```
    NDK_TOOLCHAIN_VERSION := 4.9
    APP_PLATFORM := android-21
    APP_CPPFLAGS += -DANDROID
    APP_ABI := armeabi-v7a
    APP_PROJECT_PATH := $(shell pwd)
    APP_BUILD_SCRIPT := $(APP_PROJECT_PATH)/Android.mk
  ```
## 导入Android Studio
1. 新建一个Android项目，注意选择include c++ support。
2. 运行demo,看看你的环境是不是OK的。在我这边是直接可以运行的。demo中SO也动态替我编译出来了
3. 复制编译出来的librtmp.so到libs目录下。
```xml
在build.gradle中增加如下配置。
   sourceSets {
        main {
            jniLibs.srcDirs = ['libs']
        }
    }
```
4. 在cpp目录下新建一个include目录。并吧需要用到的rtmp头文件拷贝进去。最终目录结构如下图
![image](http://note.youdao.com/yws/public/resource/483b08543d472f94207dd34a2d6ea7de/xmlnote/C24D1F1C0F1146B88CC8D0C2159616D8/10736)

5. 打开CMakeLists.txt文件，增加关于librtmp的配置
```
1.add RTMP库
add_library( rtmp-lib
             SHARED
             IMPORTED)
2. 设置RTMP库位置
set_target_properties( # Specifies the target library.
                       rtmp-lib

                       # Specifies the parameter you want to define.
                       PROPERTIES IMPORTED_LOCATION

                       # Provides the path to the library you want to import.
                       /Users/xiaoqiang/workspace/codec/workspace/RtmpDemo/app/libs/${ANDROID_ABI}/librtmp.so )
3. 增加头文件位置。这样就可以代码提示，如果没有代码提示真的写不习惯程序
include_directories( src/main/cpp/include )
```
**注意:**第二部设定的位置，必须是绝对路径，不能是相对路径，负责会报错
6.引用librtmp库
```
target_link_libraries( # Specifies the target library.
                       native-lib
                       rtmp-lib  # 这里就是使用libRTMP

                       # Links the target library to the log library
                       # included in the NDK.
                       ${log-lib} )
```
7. 在build.gradle中增加ndk的设定
```xml
defaultConfig {
      XXXXXXXX
        ndk {
            abiFilters 'armeabi-v7a'
        }
    }
```
8.增加测试代码
![image](http://note.youdao.com/yws/public/resource/483b08543d472f94207dd34a2d6ea7de/xmlnote/AA59D30D240B4E13A6F9272AD60FE2FA/10767)


**在手机中运行。很好，没有崩溃！！！！**

代码已经上传到github.在后期我可能会进行其他的改动！！

代码位置：https://github.com/wang12/RTMPDemo.git