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
