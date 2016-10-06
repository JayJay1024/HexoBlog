---
title: Windows7(64bit) Install Android Studio
date: 2016-10-06 12:20:36
tags: android
categories: 攻城狮那些事儿
---

## 北京：
&emsp;&emsp;作为一个Android开发的菜鸟，以前用的都是Eclipse，后来听说Android Studio比Eclipse逼格高，刚好前段时间重装了系统，把以前的软件都卸了，主要还是本着装一下B的精神，这次尝试安装Android Studio玩玩。首先系统是64位的win7，所以下面安装的东西都是基于win7 64bit的。

<!--more-->

## 步骤：
- JDK

&emsp;&emsp;在正式安装Android Studio前需要先安装JDK。到[官网](https://www.oracle.com/index.html)上去下载JDK（如果有墙，自备梯子），这里下载最新的JDK 8u102。下载完成后像安装正常软件一样双击安装，安装完成，可以点击"[后续步骤](http://docs.oracle.com/javase/8/docs/)"查看开发文档。

&emsp;&emsp;接下来需要配置环境变量。打开"我的电脑->属性->高级系统设置->环境变量"，在系统变量中：

&emsp;&emsp;&emsp;&emsp;添加JAVA\_HOME：安装JDK的目录，我的为"D:\Program Files\Java\jdk1.8.0_102"

&emsp;&emsp;&emsp;&emsp;添加CLASSPATH：".;%JAVA_HOME%\lib\tools.jar;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\bin;"（注意前面有'.'号）

&emsp;&emsp;&emsp;&emsp;编辑Path：在前面加上".;%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;"（注意前面有'.'号）

&emsp;&emsp;配置完在命令行下输入"java -version"，如果出现下面的信息则说明配置正确（务必配置正确，否则Android Studio启动不起来）

&emsp;&emsp;![image](http://ocww00tfq.bkt.clouddn.com/android_studio/jdk_edt_05.png)