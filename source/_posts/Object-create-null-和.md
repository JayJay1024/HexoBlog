---
title: 'Object.create(null)和{}'
date: 2019-11-25 11:10:00
tags:
categories: 攻城狮那些事儿
---

`let a = {}`和`let b = Object.create(null)`有什么区别呢？

<!--more-->

在浏览器开发者工具查看`console.log(a)`和`console.log(b)`可以看到，`a`继承了`Object`的一些东西(基于原型链)，而`b`是干净的空的。

所以`b`的方式不用担心自己定义属性的话会和原型链上的冲突；另外某些循环中，不需要去遍历原型链上的属性，可以提升一丢丢性能。
当然，这么做另一方面是丢掉了原型链上提供的所有属性或功能。

参考：[https://juejin.im/post/5acd8ced6fb9a028d444ee4e](https://juejin.im/post/5acd8ced6fb9a028d444ee4e)
