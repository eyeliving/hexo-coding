---
title: 学习ES6时for遇到循环的困惑
description: 学习ES6时for遇到循环的困惑
keywords: 学习ES6时for遇到循环的困惑，ES6，js，let，for循环
date: 2016-11-16 12:56
tags: js
---
在读 《ECMAScript 6 入门》篇头就遇到了一个比较基础的问题，尴尬。。。

```javascript
var a = [];
for (var i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6](); //out： 10
```
为什么是10呢？不应该是9吗？这点困惑不解。

为此多做了几次测试
```javascript
var a = [];
for (var i = 0; i < 10; i++) {
   a[i] = function () {
      console.log(i); 
   };
}

console.log(a)//our function...

a[1]();//out： 10
a[9]();//out： 10
a[10]();//out：err a[10] is not a function
```

> 结论


后面的 a[1]、a[9] 会再一次循环，而 i 变量此时已经是9，for 循环执行完毕了。

因为 在这里 var  i 声明的变量为全局变量，for循环之外还能调用。

在执行 a[1]() 时，for 中的 i 已经是 9，结果输出的就为10了。

这是个很基础的问题，记录下。

如果作者的结论是错的，请下方留言指正。

***

再看看let的情况如何

```javascript
var a = [];
for (let i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6](); //out： 6
```

上面代码中，变量 i 是let声明的，当前的i只在本轮循环有效，所以每一次循环的 i 其实都是一个新的变量，所以最后输出的是6。
