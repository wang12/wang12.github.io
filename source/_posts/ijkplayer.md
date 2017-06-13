---
title: ijkplayer
date: 2017-06-08 17:49:17
tags: 学习
categories: android
---
[TOC]
# IJKPLayer
## 简介
IJK是一款很好的开源的播放器。ijk基于ffmpeg，能够支持各种格式的视频流，对于播放直播视频也只有很低的延时效果。

https://github.com/Bilibili/ijkplayer

## 编译学习
我对于C/C++ 还有ndk一直不是很了解，所以编译出一套自己so比较困难。但是ijk完全不一样，通过github上的编译步骤，新手完全可以自己编译出来一套SO库。

我编译出来的第一个版本是基于windons下的cygwin环境下的。但是在使用过程中发现有问题，所以在后期通过询问c开发人员了解，最好还是在linux环境下编译。

* 在编译下来ijk后，我完全移除掉github中所示的Java层代码，只保留最基本的native类和方法。
* demo下载地址：https://github.com/wang12/player

![image](http://note.youdao.com/yws/public/resource/483b08543d472f94207dd34a2d6ea7de/xmlnote/CBD33F3F91334D2889ACF338253472FD/10485)
### 搭建环境
1. 我使用的是32位的Ubuntu。Ubuntu 16.10 
2. 按照github上资料所示，需要安装git和make,yasm等工具
3. 在Android平台上使用，需要安装NDK，我使用的版本是：android-ndk-r10e。注意，如果使用32位的ubuntu，那么必须安装32位的ndk.现在官网提供的全是64位。如果直接使用64位会出现各种问题
http://blog.chinaunix.net/uid-20680966-id-4961541.html

4. 按照github上的步骤执行具体的脚本
5. 复制so和demo工程，可以在Android studio上运行了

## 遇到的问题
1. 第一次在cygwin上编译出来的版本，发现seekto过程中，视频并不暂停，正常播放

遇到这个问题时，我从来没有怀疑过编译环境的问题，一直以为是ijk代码中存在漏洞，所以各种找解决办法。后来同事通过mac编译出来ijk也是存在这个问题的。所以只能找懂c和c++ 的同事处理。解决发现他们通过linux编译下载的版本是完全没有问题的。

所以在后期处理时，我一直使用linux编译ijk。

2. Syntax error: word unexpected (expecting ")")C compiler test failed.

这个是今年又想编译ijk时遇到的。因为很久没有看播放方面的事情了，所以这次是单纯学习过程。但是在编译过程中一直遇到这个问题。

开始我以为是ndk版本问题，所以各种更换ndk，各种修改编译环境。最后发现，我从官网下载的ndk一直是64位版本，而我的linux是32位版本。所以导致一直提示这个问题。

在查询资料时发现，这个问题导致的原因有很多，我所遇到的应该是最简单的。毕竟像我这种啥也不懂就上手编译的人不多！！！
