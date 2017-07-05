---
title: js面向对象小结js面向对象小结（工厂模式，构造函数，原型方法，继承）
description: js面向对象小结js面向对象小结（工厂模式，构造函数，原型方法，继承）
keywords: js面向对象小结js面向对象小结（工厂模式，构造函数，原型方法，继承）
date: 2017-07-05 19:53
tags: js
---
最近过了一遍尼古拉斯泽卡斯的高级程序设计第三版（红皮书）第六章：面向对象程序设计，现在把总结出来的东西和大家分享一下。

主要内容如下：
1.工厂模式
2.构造函数模式
3.原型模式
4.继承

1. 工厂模式

工厂模式中的函数中会创建一个对象，最后return这个对象，通过每次调用时传入的参数不同来解决创建多个相似对象的问题。
```javascript
//工厂模式
function creatPerson(name, age, job) {
	var o = {};
	o.name = name;
	o.age = age;
	o.job = job;
	o.sayName = function() {
		console.log(this.name);
	}
	return o;
}
var tianjiao = creatPerson('tj',22,'fe');
console.log(tianjiao)
```

2. 构造函数模式

构造函数本身也是函数，只不过是一个创建对象的函数
```javascript
//构造函数
function Person(name, age) {
	this.name = name;
	this.age = age;
	this.sayName = function() {
		console.log(this.name)
	};
}
var tj2 = new Person('tj2', 23)
console.log(tj2)
```	
要创建person新实例，必须使用new操作符，调用构造函数会经历以下四步：
1.创建一个新对象
2.将构造函数的作用域给了新对象（this指向新对象）
3.为新对象添加属性
4.返回新对象
创建自己定义的构造函数意味着将来可以将它的实例标识为一种特定的类型，而工厂模式则都是object，无法区别。
构造函数和其他函数的唯一区别是调用方式不同，而任何函数只要用new操作符调用，那他就可以作为构造函数。

3. 原型模式

每个函数都有一个prototype属性，这个属性是一个指针，指向一个对象，这个对象的用途是包含可以由特定类型的实例共享的属性和方法。

使用原型对象的好处是可以让所有对象实例共享他所包含的属性和方法。不必在构造函数中定义对象实例的信息，而是可以将这些信息直接添加到原型对象中。
```javascript
//原型方法
function Person() {

}
Person.prototype.name = 'tj3';
Person.prototype.age = 24;
Person.prototype.sayName = function(){
	alert(this.name)
}
var tj3 = new Person();
console.log(tj3)
//更简单的原型方法
function Person() {

}
Person.prototype = {
	name:'tj4',
	age:25,
	sayName : function() {
		alert(this.name)
	}
};
var tj4 = new Person();
console.log(tj4)
```
	
4. 组合使用构造函数和原型模式

构造函数模式用于定义实例属性，而原型模式定义方法和共享的属性。这种混合模式还支持向构造函数传递参数。
```javascript
//组合使用构造函数和原型模式
function Person(name, age, job) {
	this.name = name;
	this.age = age;
	this.job = job;
	this.sayName = function() {
		alert(this.name)
	}
}

Person.prototype = {
	sayJob : function() {
		console.log(this.job);
	}
}

var person1 = new Person('tj', 22 , 'fe');
```
	
5. 原型链继承

原型链继承的主要思想是利用原型让一个引用类型继承另一个引用类型的属性和方法。

警告：通过原型链继承是，不能使用对象字面量创建原型方法，这样会重写原型链！
```javascript
//原型链继承
function SuperType() {
	this.color = ['red', 'blue'];
}
function SubType() {

}
SubType.prototype = new SuperType();// 继承了SuperType
var instance1 = new SubType();
console.log(instance1);
```
	
6. 借用构造函数继承

其实就是用call和apply实现继承
```javascript
//借用构造函数继承
function wanda() {
	this.money = [1,2,3]
}
function sicong() {
	wanda.call(this);
}
var sc = new sicong();
sc.money.push(666)
console.log(sc)
```

7. 组合继承

将原型链和借用构造函数的技术组合起来一起用，好处是既能实现函数的复用，又能保证每个实例有自己的属性。
```javascript
//组合继承
function SuperType(name) {
	this.name = name;
	this.color = ['red', 'blue'];
}
SuperType.prototype.sayName = function() {
	console.log(this.name);
};
function SubType(name, age) {
	SuperType.call(this, name);//继承属性
	this.age = age;
}
SubType.prototype = new SuperType();//继承方法
var instance1 = new SubType('tj', 22);
instance1.sayName();
```

最后还有寄生式继承和寄生组合式继承，自己工作中也没有用过，这里就不赘述了。

摘自 http://www.imooc.com/article/17370