---
title: JS原型和原型链
date: 2019-11-22 23:23:07
tags:
categories: 攻城狮那些事儿
---

## 背景
有时代码敲得再多，项目做的再多，面试时还是会被一些简单的问题难倒

<!--more-->

## JS原型和原型链
读取某个属性，首先会在对象里面看看有没有，没有就会去对象的原型里面看看，以此直到最上层的`Object`对象的原型
```
function Animal() {
	// 实例化后属于实例的属性
	// 如果这里不定义，则和 let animal = new Animal(); animal.name = "I am an animal" 是一样的
	this.name = "I am an animal";
}
Animal.prototype.name = "Hey, this is an animal";  // 对象原型的属性

let animal = new Animal();
console.log(animal.name);  // 打印 "I am an animal"

delete animal.name;
console.log(animal.name);  // 打印 "Hey, this is an animal"

console.log(animal.__proto__ === Animal.prototype);  // true
console.log(Animal.prototype.__proto__ === Object.prototype);  // true  Animal的上一层是Object
console.log(Animal.prototype.constructor === Animal);  // true


// 一般地
let obj = new Object();
console.log(obj.__proto__ === Object.prototype);  // true
console.log(Object.prototype.__proto__ === null);  // true  原型链顶层
console.log(Object.prototype.constructor === Object);  // true
```
