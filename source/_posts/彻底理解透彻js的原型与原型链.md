---
title: 彻底理解透彻js的原型与原型链
description: 彻底理解透彻js的原型与原型链
keywords: 彻底理解透彻js的原型与原型链
date: 2017-06-15 18:53
tags: js
---
## 前言也即是初衷
对于js的原型、原型链一直是迷迷糊糊的，大概似乎可能是这样的。。。好吧，是有点不清楚，去了解了之后，，
过个三月、半年的，对于她的概念又模糊了。
这次决定彻底搞懂她！

### 一、原型是什么？原型链又是什么？
因为每个对象和原型都有原型，对象的原型指向原型对象，
而父的原型又指向父的父，这种原型层层连接起来的就构成了原型链。

1.JavaScript中，万物皆对象！但对象也是有区别的。分为普通对象和函数对象
每个对象都有 __proto__ 属性，但只有函数对象才有 prototype 属性。

__proto__与prototype的区别？
prototype是function对象中专有的属性。 
__proto__是普通对象的隐式属性，在new的时候，会指向prototype所指的对象； 
__ptoto__实际上是某个实体对象的属性
而prototype则是属于构造函数的属性。
__ptoto__只能在学习或调试的环境下使用。


a.JS 在创建对象（不论是普通对象还是函数对象）的时候，都有一个叫做__proto__ 的内置属性，用于指向‘创建它’的构造函数的【原型对象】。
对象 person1 有一个 __proto__属性，创建它的构造函数是 Person，构造函数的原型对象是 Person.prototype ，所以如下代码：

函数对象的prototype 属性，这个属性指向函数的【原型对象】

在默认情况下，所有的【原型对象】都会自动获得一个 constructor（构造函数）属性，
这个属性（是一个指针）指向 prototype 属性所在的函数（Person）。

```javascript
Person.prototype.constructor == Person;
person1.__proto__ == Person.prototype;
person1.constructor == Person;

obj.constructor === Object
obj.__proto__ === Object.prototype


var b = new Array();
b.constructor === Array;
b.__proto__ === Array.prototype;

var c = new Date(); 
c.constructor === Date;
c.__proto__ === Date.prototype;

var d = new Function();
d.constructor === Function;
d.__proto__ === Function.prototype;




var num = [1];
console.log(num.hasOwnPrototype()) // false (输出布尔值而不是报错)
//当你用num.hasOwnPrototype()时，JS 会先查一下它的构造函数 （Array） 的原型对象 Array.prototype 有没有有hasOwnPrototype()方法，没查到的话继续查一下 Array.prototype 的原型对象 Array.prototype.__proto__有没有这个方法。
```
普通对象的__proto__指向这个对象（this）的构造函数的prototype；
函数对象的__proto__全部都是指向Function的prototype。
然后无论是Function的prototype还是构造函数的prototype都指向object.prototype，然后最终object.prototype指向null


```javascript
function Person() {}
Person.prototype.name = 'Zaxlct';
Person.prototype.age  = 28;
Person.protetype.job  = 'Software Engineer';
Person.prototype.sayName = function() {
  alert(this.name);
}
//原型对象
Person.prototype = {
   name:  'Zaxlct',
   age: 28,
   job: 'Software Engineer',
   sayName: function() {
     alert(this.name);
   }
}
```

2.
```javascript
function A(){
    this.mark = "A";
    this.changeMark = function(){
        this.mark += "_changed";
    }
}

A.prototype.mark2 = "A2";
A.prototype.changeMark2 = function(){
    this.mark2 += "_changed";
}

var a = new A();
console.log(a.mark2);  //"A2"
```
当使用对象(a)的属性或调用对象的方法时，如果对象(a)的属性或方法不存在时，对象会在一步一步通过__proto__向上寻找，找到最近的则是最终的获取到的方法或属性。
这就是js中的原型链。

所有的__proto__的终点站最终都是指向Object对象，而Object的原型对象（Object.prototype）是不继承自任何属性的对象，即Object.prototype没有__proto__，是原型链的顶层。

## 二、她的作用什么？经常用来解决哪一方面的问题？
## 三、怎样用她？
## 四、总结

```javascript
function Person(){}
var p = new Person()
p.__proto__ --> Person.protetype
Person.protetype.__proto__ --> Function.protetype
Function.protetype.__proto__ --> Object.protetype

/**
很简单的一段代码，我们来看看这个new究竟做了什么？我们可以把new的过程拆分成以下三步：
var p={}; 也就是说，初始化一个对象p。
p.__proto__=Person.prototype;
Person.call(p);也就是说构造p，也可以称之为初始化p。


那么__proto__是什么？我们在这里简单地说下。
每个对象都会在其内部初始化一个属性，就是__proto__，当我们访问一个对象的属性 时，如果这个对象内部不存在这个属性，那么他就会去__proto__里找这个属性，这个__proto__又会有自己的__proto__，于是就这样 一直找下去，也就是我们平时所说的原型链的概念。

*/

```

做个测试
```javascript
var fun = function(){}  
  
fun.prototype = {      
    name : 'peter',      
    age : 25      
}  
  
var a = new fun();  
var b = new fun();  
console.log(a.name, b.name);//peter peter  
fun.prototype.name = 'jack';  
console.log(a.name, b.name);//jack jack  
fun.prototype = {};  
fun.prototype.name = 'tom';  
console.log(a.name, b.name);//jack jack  
b.constructor.prototype.name = 'kitty';  
console.log(a.name, b.name);//jack jack 
```
问：若想要输出tom该怎么改，为什么不能输出kitty?
答案：
(1)、`fun.prototype ={}`是重写原型，重写后跟重写前就已经实例化的对象没有关系的，所以Tom自然不生效。
若要输出tom,添加以下代码`a.__proto__.name=tom`，无法通过fun来实现，因为原型链已断。
			  
(2)、 不能输出kitty是因为`b.contructor!=fun`，在刚开始的时候fun的原型就被重写了。
可以改为
```javascript
fun.prototype={
	name:''peter'',
	constructor:fun,
	age:25
}
```
