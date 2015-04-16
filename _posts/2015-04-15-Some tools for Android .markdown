---
layout: post
title:  "Some Tools for Android --Genymotion模拟器和lint代码检测工具"
categories: android,Genymotion,lint
author: "killnono"
---

几个关于Android的小工具介绍。 
  
+ Genymotion模拟器---比官方avd更快，功能更完善的，性能高，运行环境配置低。  
+ lint代码检测工具---adt16以后就引入的一个工具。检测代码规范，去冗余，代码更优雅更健壮。

###一.Genymotion模拟器：
[genymotion官网](https://www.genymotion.com/#!/product)  
Genymotion与其说是一个Android模拟器工具,更像是一套提供了一套运行Android的虚拟环境。  
相对于传统的的Google提供的avd，Genymotion提供了更多的模拟器设备型号。

Genymotion一些优点（官方）:

+ 速度快性能好，资源占用低，系统要求512MB内存就能运行
+ 支持 OpenGL 3D加速，可以流畅玩大型3D游戏
+ 支持同时启动多个模拟器，可以实现软件或游戏多开
+ 支持多种虚拟传感器，如电池状态、GPS、NFC、重力加速器等模拟，在微信、陌陌之类的App上有特别用处
+ 支持摄像头、蓝牙等设备
+ 可以配置模拟器参数，如屏幕分辨率、内存大小、CPU数量
+ 直接拖拽窗口即可调整画面大小，支持多种分辨率
+ 能支持几乎全部版本的 Android 系统，2.x 到 4.x 都没问题
+ 支持直接拖放文件到虚拟设备
+ 启动速度运行速度都完爆 Android SDK 官方的模拟器

个人在使用过程中的一些问题：

+ 流媒体app无法播放(对于作为做一个视频类app，wfk!!).
+ 兼容性不是很好(当我完全把其当做一个手机来体验时，从app store下载app)
+ Unfortunately, [app name] has stopped 出现的频繁~

 
tips：
安装过程中，需要先安装Virtualbox，否则在直接下载genymotion套件使用时，会显示一些问题。


###二.Android lint代码检测工具

 