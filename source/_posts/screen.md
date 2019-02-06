---
title: screen
date: 2019-02-06 22:15:16
tags:
---

## 简介：
当我们远程登录服务器，正在执行某些操作，比如正在编译源代码到60%，如果因为网络等各种原因断开了与服务器的链接，那么编译过程可能会被中断，我们重新链接服务器时需要从从头进行编译。
如果执行编译前使用了`screen`，链接断开时编译工作不会中断，重连后登录之前的`screen`会话，就可以回到当时的环境。

<!--more-->

## 使用：
- screen -S XXX：创建名为XXX的后台会话
- screnn -ls：查看所有后台会话
- screen -r XXX：恢复名为XXX的后台会话

## 参考：
[http://www.cnblogs.com/mchina/archive/2013/01/30/2880680.html](http://www.cnblogs.com/mchina/archive/2013/01/30/2880680.html)
