---
title: JS的call方法
date: 2019-11-23 01:39:43
tags:
categories: 攻城狮那些事儿
---

## 概述
`foo.call(this, args...)`，指明`foo`函数或方法的`this`和参数，然后运行`foo`。

<!--more-->

```
function Animal(name) {
	console.log(`animal's name is ${name}, color is ${this.color}`);
}
function Dog() {
	this.color = "dog's color is white";
	Animal.call(this, "dog");
}
new Dog();  // 打印 "animal's name is dog, color is dog's color is white"
```

## 为自己打`call`
```
Function.prototype.mycall = function(ctx) {
	// ctx也就是传过来的this(同arguments[0])，传null则为window(nodejs可以用global)
	ctx = ctx || (typeof window === "undefined" ? global : window);
	ctx.foo = this;  // 这个this是调用mycall的那个函数或方法

	let args = [];
	for (let i = 1; i < arguments.length; i++) {
		args.push(arguments[i]);
	}
	ctx.foo(...args);
	delete ctx.foo;
}

function Animal2(name) {
	console.log(`animal's name is ${name}, color is ${this.color}`);
}

function Dog2() {
	this.color = "black";
	Animal2.mycall(this, "dogdog");
}
new Dog2();  // 打印 "animal's name is dogdog, color is black"

let Cat = {
	color: "white"
}
Animal2.mycall(Cat, "catcat");  // 打印 "animal's name is catcat, color is white"
```

## `apply`呢
上面的`call`，调用的时候，需要对应每个参数，而`apply`一共只有两个参数，第一个和`call`是一样的，而第二个参数传的是参数数组，这用于不确定参数时很方便。
```
function fn(arg1, arg2) {
	console.log.call(console, arg1, arg2);
}
fn("what", 666);  // 打印 "what 666"

function fn2() {
	console.log.apply(console, arguments);
}
fn2(12, "haha", 1234);  // 打印 "12 haha 1234"
fn2(12, "haha", 1234, "what", "why");  // 打印 "12 haha 1234 what why"
```

## 总的
总的来说，`call`和`apply`都是为了动态改变`this`的指向。
