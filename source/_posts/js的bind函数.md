---
title: js的bind函数
date: 2019-11-23 18:24:05
tags:
categories: 攻城狮那些事儿
---

## 概述
由`Function.prototype.bind()`，所以`bind()`是函数或方法的一个属性。`bind()`将创建一个新的函数，调用`bind()`的时候，新函数的`this`被`bind()`的第一个参数指定，`bind()`的其余参数将作为新函数的参数供调用时使用。

<!--more-->

```
const obj = {
	x: 12,
	getX: function() { return this.x; },
	setX: function(x) { this.x = x; }
}

console.log(obj.getX());  // 12

const unboundGetX = obj.getX;
// console.log(unboundGetX());  // TypeError: Cannot read property 'x' of undefined

const boundGetX = unboundGetX.bind(obj);
console.log(boundGetX());  // 12

const boundSetX = obj.setX.bind(obj, 89);
boundSetX();  // 这时传不传参数不影响obj.x=89的事实
console.log(boundGetX());  // 89

const boundSetX2 = obj.setX.bind(obj);
boundSetX2(999);  // 这时如果不传参数obj.x将是undefined
console.log(boundGetX());  // 999
```

## 实现自己的`bind()`
```
const obj2 = {
	name: "this is obj2"
}

function foo(color) {
	console.log(color);
	return this.name;
}

const bindFoo = foo.bind(obj2, "black");
// black
// this is obj2
console.log(bindFoo());

// 构造函数时
const nbfoo = new bindFoo();  // black，传入的参数依然生效
console.log(nbfoo.name);  // undefined，传入的obj2失效，this指向的是nbfoo而不是obj2了

// TODO: new bindFoo()的时候，this指向的是new出来的而不是obj2，这个效果
Function.prototype.mybind = function(ctx) {
	const fn = this;
	const args = Array.prototype.slice.call(arguments, 1);  // 去掉第0个
	return function() {
		return fn.apply(ctx, args);
	}
}

const mybindFoo = foo.mybind(obj2, "white");
// white
// this is obj2
console.log(mybindFoo());
```
