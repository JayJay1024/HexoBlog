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

- **JDK**

&emsp;&emsp;在正式安装Android Studio前需要先安装JDK。到[官网](https://www.oracle.com/index.html)上去下载JDK（如果有墙，自备梯子），这里下载最新的JDK 8u102。下载完成后像安装正常软件一样双击安装，安装完成，可以点击"[后续步骤](http://docs.oracle.com/javase/8/docs/)"查看开发文档。
![image](http://ocww00tfq.bkt.clouddn.com/android_studio/down_jdk_01.png)

接下来需要配置环境变量。打开"我的电脑->属性->高级系统设置->环境变量"，在系统变量中：

> - 添加JAVA\_HOME：安装JDK的目录，我的为"D:\Program Files\Java\jdk1.8.0_102"
> - 添加CLASSPATH：".;%JAVA_HOME%\lib\tools.jar;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\bin;"（注意前面有'.'号）
> - 编辑Path：在前面加上".;%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;"（注意前面有'.'号）

![image](http://ocww00tfq.bkt.clouddn.com/android_studio/jdk_edt_01.png)
![image](http://ocww00tfq.bkt.clouddn.com/android_studio/jdk_edt_02.png)
![image](http://ocww00tfq.bkt.clouddn.com/android_studio/jdk_edt_03.png)
![image](http://ocww00tfq.bkt.clouddn.com/android_studio/jdk_edt_04.png)

&emsp;&emsp;配置完在命令行下输入"java -version"，如果出现下面的信息则说明配置正确（务必配置正确，否则Android Studio启动不起来）
![image](http://ocww00tfq.bkt.clouddn.com/android_studio/jdk_edt_05.png)

- **Android Studio**

&emsp;&emsp;建议到[官网](https://developer.android.com/studio/index.html)上直接用网页下载，不要用迅雷，至于为什么，听说是避免发生"[XCodeGhost](https://www.zhihu.com/question/35721299)"事件。这里下的是最新的*android-studio-bundle-145.3276617-windows.exe*
![image](http://ocww00tfq.bkt.clouddn.com/android_studio/android_studio_01.png)

下载完成后，双击进行安装，当然可以参考[官网的安装教程](https://developer.android.com/studio/install.html)
![image](http://ocww00tfq.bkt.clouddn.com/android_studio/sdk_path.png)

&emsp;&emsp;安装完成后，如果电脑没有翻墙，建议先不要勾选*Start Android Studio*，点击*Finish*完成安装，然后在安装目录的*bin*文件夹下找到*idea.properties*，在后面添加"disable.android.first.run=true"，这是设置打开Android Studio后不联网检测

&emsp;&emsp;第一次打开Android Studio，首先会让你是否导入配置文件，可根据自身情况选择，点击*OK*，等待安装SDK，安装需要一段时间，耐心等待。安装完成如下
![image](http://ocww00tfq.bkt.clouddn.com/android_studio/install_finish_01.png)

配置SDK环境,点击*Configue->Project Defaults->Project Structure*
![image](http://ocww00tfq.bkt.clouddn.com/android_studio/install_finish_02.png)

配置编码为UTF-8,点击*Configue->Project Defaults->Settings*。And then，开始Android Studio之旅吧
![image](http://ocww00tfq.bkt.clouddn.com/android_studio/settings_utf8.png)

## 参考：
- [http://blog.csdn.net/zhoujn90/article/details/8957713](http://blog.csdn.net/zhoujn90/article/details/8957713)
- [http://www.2cto.com/kf/201604/500642.html](http://www.2cto.com/kf/201604/500642.html)