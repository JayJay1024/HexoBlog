---
title: js实现继承
date: 2019-11-24 17:18:23
tags:
categories: 攻城狮那些事儿
---

## 一、`js`继承问题
`es6`引入了`class`和`extends`，从而可以像`c++`那样理解继承，但本质上这只是一个语法糖。

<!--more-->

```
class Parent {
	constructor(name) {
		this.name = name;
	}
}
class Children extends Parent {
	constructor(name) {
		super(name);  // 子类没有自己的this，不进行super，得不到继承自父类的this，将出错
	}
}
const children = new Children("children");
console.log(children.name);  // children
```

## 二、自己如何实现继承
```
function Animal(color) {
	this.name = "animal";
	this.color = color;
	this.type = ['pic', 'cat'];
}
Animal.prototype.sayName = function() {
	console.log(`Hey, I am ${this.name}`);
}
```

#### 1、继承方式1：原型链
```
function Dog() {}
Dog.prototype = new Animal("white");
const dog = new Dog();
console.log(dog.name, ":", dog.color);  // animal : white
```
此种方式缺陷1：引用缺陷
```
const dog1 = new Dog();
dog1.type.push('dog');
const dog2 = new Dog();
console.log(dog2.type);  // [ 'pic', 'cat', 'dog' ]
```
此种方式缺陷2：对于继承来的属性，无法为不同的实例初始化为不同的值
```
function Cat() {}
Cat.prototype = new Animal("black");  // 所有的Cat实例初始化时都是black色
```

#### 2、继承方式2：借用构造函数(也叫经典继承)
```
function Pic(color) {
	Animal.apply(this, arguments);
}
const pic = new Pic("white");
console.log(pic.name, ":", pic.color);  // animal : white
```
此种方式缺陷：无法获取父类通过原型绑定的方法
```
pic.sayName();  // TypeError: pic.sayName is not a function
```

#### 3、继承方式3：组合继承(就是组合上面两种，可以避免上面的缺陷。氮素，会调用两次父类构造函数)
```
function Monkey(color) {
	Animal.apply(this, arguments);
}
Monkey.prototype = new Animal();
const monkey = new Monkey("gray");
console.log(monkey.name, ":", monkey.color);  // animal : gray
monkey.sayName();  // Hey, I am animal
```

#### 4、继承方式4：寄生组合继承(避免调用两次父类构造函数的问题)
```
function Tiger(color) {
	Animal.apply(this, arguments);
}
Tiger.prototype = Object.create(Animal.prototype);  // 使用Object.create进行浅拷贝，少了一次调用父类的构造函数
Tiger.prototype.constructor = Tiger;  // 上面浅拷贝后Tiger.prototype的constructor被重写了，所以要修复一下
```
