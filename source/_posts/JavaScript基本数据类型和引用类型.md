---
title: JavaScript基本数据类型和引用类型
description: js中的变量分为两种类型：  1. 基本数据类型 2. 引用数据类型  JS基本数据类型的变量存放的是基本类型数据的实际值；而引用数据类型的变量保存对它的引用，即指针。  说白了就是：**基本数据类型传的是数值，引用类型传的是变量堆内存的地址**  这句在下边最后的几个例子当中尤为重要。  JS基本数据类型：null undefined number boolean  string比较特殊  引用数据类型：function object array
keywords: JavaScript基本数据类型、JavaScript引用类型、JavaScript浅拷贝、JavaScript深拷贝
date: 2017-01-23 14:44
tags: javascript
---
**js中的变量分为两种类型：**

  1. 基本数据类型
  2. 引用数据类型

JS基本数据类型的变量存放的是基本类型数据的实际值；而引用数据类型的变量保存对它的引用，即指针。

说白了就是：**基本数据类型传的是数值，引用类型传的是变量堆内存的地址**

这句在下边最后的几个例子当中尤为重要。

JS基本数据类型：null undefined number boolean

string比较特殊

引用数据类型：function object array

```javascript

/*
* 1.基本数据类型是复制关系
* 一个值的改变不会影响到另一个值的变化
* */

var a = 5;
var b = a;
b += 3;
alert(b);   // 8
alert(a);   // 5



/*
* 2.对象和函数是引用关系
* 就是说，两个值使用同一个对象或函数，
* 其中一个改变了函数，另一个也会发生变化
* */

var a = [1,2,3];
var b = a;
b.push(4);
alert(b); //1,2,3,4
alert(a); //1,2,3,4

var a = [1,2,3];
// 此时的a和b指向同一个数组
var b = a;
// 这里b重新指向了一个新数组，与之前的a指向的数组断开了关系
b = [1,2,3,4];
alert(b); //1,2,3,4
alert(a); //1,2,3

/*
* 思考？
* 像对象引用这个情况，两个共享一份内存，这不是我们想要的
* 我们要的是复制一份新的数据到新内存中去，这样两个就不会相关了
* 因此我们需要拷贝
* */

/*
* 3.浅拷贝
* */
var obj = { a : 10 };

function copy(obj) {
    var newObj = {};
    for ( var attr in obj ) {
        newObj[attr] = obj[attr];
    }
    return newObj;
}
var obj2 = copy(obj);
obj2.a = 20;
alert(obj2.a); //20
alert(obj.a);  //10

/*
* 这样的化，当obj2.a的值发生了变化，obj.a的值不会变
*
* 但是，这仅仅是浅拷贝
* 浅拷贝仅仅拷贝的是基本数据类型，对于对象和函数就会出问题
* 对于对象引用拷贝的值，两个对象还是指向同一个内存，两者相关
* */


var obj = { a : { b : 10 } };

function copy(obj) {
    var newObj = {};
    for ( var attr in obj ) {
        newObj[attr] = obj[attr];
    }
    return newObj;
}
var obj2 = copy(obj);
obj2.a.b = 20;
alert(obj2.a.b); //20
alert(obj.a.b);  //20




/*
* 对于上述的情况，浅拷贝并不能解决对象引用的问题
* 这是我们应该编写一个深拷贝的函数
* 这里用到了递归：自己(函数)调用自己
* */


var obj = { a : { b : 10 } };

function deepCopy(obj) {
    // 最后一次判断终止条件，开始归的动作
    // 如果obj为基本数据类型时，就直接返回，只需要深拷贝
    if (typeof obj !== 'object' && typeof obj !== 'function') {
        return obj;
    }
    var newObj = {};
    for ( var attr in obj ) {
        newObj[attr] = arguments.callee(obj[attr]);
    }
    return newObj;
}
var obj2 = deepCopy(obj);
obj2.a.b = 20;
alert(obj2.a.b); //20
alert(obj.a.b);  //10
```
