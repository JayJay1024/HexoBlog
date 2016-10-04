---
title: Ubuntu14.04 Install Wine
date: 2016-09-11 10:26:40
tags: wine
categories: 攻城狮那些事儿
---

## What is the wine
wine(Wine Is Not an Emulator)是一款开源的，免费的Windows程序加速器，能够让多种POSIX-compliant操作系统（如Linux，Mac OSX及BSD等）运行Windows应用程序的兼容层，它不是模拟器，也不是虚拟机，它是运用API转换技术做出Linux和Windows相对应的函数DLL以运行Windows程序。

<!--more-->

## How to install
Ubuntu安装wine可以通过*apt-get install*的方式，也可以通过编译源码进行安装。
通过*apt-get install*的方式安装简单快捷（[Here](https://wiki.winehq.org/Ubuntu)），但是通过编译源码安装兼容性和稳定性更好。不喜欢唱歌的屌丝不是好水手，所以下面看看怎么通过编译源码的方式安装wine（如果是64位ubuntu,如果安装不成功可以使用*apt-get install*的方法，不努力一下怎么知道什么叫绝望呢）
- 从[官网](https://www.winehq.org/)上下载源码包，目前最新的稳定版是[1.8.4](https://www.winehq.org/announce/1.8.4)
- 下载后解压
```
tar -jxvf wine-1.8.4.tar.bz2
cd wine-1.8.4/
```
打开README可以看到，可以执行*./tools/wineinstall*进行快速安装
![quickstart](http://ocww00tfq.bkt.clouddn.com/install/wine/quickstart)
或者执行下面的步骤进行安装
![图片](http://ocww00tfq.bkt.clouddn.com/install/wine/compilation)
- 不走寻常路，看一下非快速安装，首先执行*sudo ./configure*检查环境，直到出现"configure: Finished.  Do 'make' to compile Wine."
- 执行*make*,这一步时间可能有点久，这时候可以喝杯68年中石油，耐心等待
- 最后出现"Wine build complete."，那么恭喜你，编译成功了
- 接着执行*make install*进行安装
- 安装完成后，根据README提示，执行*winecfg*进行配置，打开过程中提示有些东西没有安装，点击install即可
- 怎么运行程序呢：README中有说明
![running](http://ocww00tfq.bkt.clouddn.com/install/wine/running)

> **踩坑日记（执行./configure后的错误和解决方案）：**
> - error: Cannot build a 32-bit program, you need to install 32-bit development libraries
```
./configure --enable-win64
```
> - error: no suitable flex found. Please install the 'flex' package
```
sudo apt-get install flex
```
> - error: no suitable bison found. Please install the 'bison' package
```
sudo apt-get install bison
```
> - error: X 64-bit development files not found......
```
sudo apt-get install xorg-dev libx11-dev
```
> 哪里错误补哪里，麻麻再也不用担心我的学习啦------步步高补错机

## So...
对于喜欢使用Linux的筒子，总有一个纠结就是很多应用没有Linux版本。有了wine，在Linux上也能执行大多数Windows的程序了，再也不用纠结在Linux和Windows之间切换来切换去了（此处可以有掌声。。。）
另外，可以参考[wine官网的build过程](https://wiki.winehq.org/Building_Biarch_Wine_On_Ubuntu)

