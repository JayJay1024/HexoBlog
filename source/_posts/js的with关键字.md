---
title: js的with关键字
date: 2019-11-23 15:22:39
tags:
categories: 攻城狮那些事儿
---

## 使用
```
let obj = {
	a: 'aaa',
	b: 'bbb',
	c: 'ccc'
}
console.log(obj.a, obj.b, obj.c);

// 使用 with
with (obj) {
	console.log(a, b, c);
}
```

<!--more-->

`with`用来改变作用域，上面的`a, b, c`首先会被认为是局部变量，然后会查`obj`中有没有同名的，有就指向该同名属性

## 然而
一般是不推荐使用`with`的，而且`strict`模式下是不能使用的，为什么呢？主要是性能问题，以及语意不明容易出现`bug`。
```
let obj = {
	a: 'aaa',
	b: 'bbb',
	c: 'ccc'
}

function fn1() {
	console.time("without");
	for (let i = 0; i < 10000; i++) {
		let ta = obj.a;
		let tb = obj.b;
		let tc = obj.c;
	}
	console.timeEnd("without");
}
function fn2() {
	console.time("with");
	for (let i = 0; i < 10000; i++) {
		with (obj) {
			let ta = a;
			let tb = b;
			let tc = c;
		}
	}
	console.timeEnd("with");
}


fn1();  // 0.343ms
fn2();  // 6.114ms
```
因为`js`代码执行有一个编译阶段，使用`with`后`js`引擎不知道这些`a, b, c`是`obj`上的属性，无法对它们进行静态分析，就无法对这段代码进行优化；另外，一些`js`压缩工具也无法对这段代码进行压缩。这些是影响其性能的主要原因。

```
let objName = {
	name: "apple"
}
let objColor = {
	color: "white"
}

function foo(obj) {
	with (obj) {
		name = "dog"
	}
}

// console.log(name);  // 此时将出错，ReferenceError: name is not defined

console.log(objName.name);  // apple
foo(objName);
console.log(objName.name);  // dog

console.log(objColor.color);  // white
foo(objColor);
console.log(objColor.color);  // white

console.log(objColor.name);  // undefined
console.log(name);  // dog
```
上面这段代码很容易出错和难以调试。
