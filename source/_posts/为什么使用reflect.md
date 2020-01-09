---
title: 为什么使用reflect
date: 2020-01-09 20:41:13
tags: [Golang]
categories: 攻城狮那些事儿
---

## 前情

Golang是一门静态语言，也就决定了一般情况下不能在程序运行中改变其行为。好在Golang也是一门面向反射的语言，通过反射机制，可以弥补这部分不足。

<!-- more -->

## 反射

借用维基百科的一段话：

> 在计算机学中，反射（英语：reflection）是指计算机程序在运行时（runtime）可以访问、检测和修改它本身状态或行为的一种能力。用比喻来说，反射就是程序在运行的时候能够“观察”并且修改自己的行为。

## Go中的反射

`reflect`是Go的反射包。一个对象包含的最主要信息就是名称和值(key, value)，知道了名称和值，那么等于知道了对象的所有。

`reflect`中有两个重要的对象：
- type Value struct
- type Type interface

我们平时用到的`reflect`基本上都是围绕`Type`和`Value`。`reflect`提供可以获得这两个对象的方法：
- func TypeOf(i interface{}) Type
- func ValueOf(i interface{}) Value

得到`Value`和`Type`实例后，`Value`和`Type`提供很多方法供我们操作。

举例来说，我们可以通过`reflect`获取运行中某个变量是struct、interface还是func等类型，也可以获取变量的字段和值。

总之，通过`reflect`我们可以获取一个对象的一切，如果是struct，我们可以知道它的所有字段和值；如果是func，我们可以知道参数列表，然后构造一个函数，然后运行。
