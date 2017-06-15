---
title: js的执行过程，预解析与执行
description: js的执行过程，预解析与执行
keywords: js的执行过程，预解析与执行，js的执行步骤原理
date: 2016-11-22 18:51
tags: javascript
---
先来个定义吧

JavaScript是一种描述型脚本语言。

它不同于java或C#等编译性语言,它不需要进行编译成中间语言,而是由浏览器进行动态地解析与执行。

好了，下面说说 JavaScript 的执行顺序



> 预解析与执行期

先举个例子

```javascript
alert(inqiu);//Uncaught ReferenceError: inqiu is not defined

alert(a); //undefined
var a = 1;
```
再举个例子

```javascript
var arg = 1;
function foo(arg) {
    alert(arg);
    var arg = 2;
}
foo(3);//undefined
```

Why? 解释几句吧

首先，JavaScript引擎会优先解析var变量和function定义。

在代码预解析完成后，才会执行代码。

如果一个文档流中包含多个script代码段（用script标签分隔的js代码或引入的js文件）

大概的运行顺序是：

* step1. 读入第一个代码段
* step2. 做语法分析，有错则报语法错误（比如括号不匹配等），并跳转到step5
* step3. 对var变量和function定义做“预解析”（永远不会报错的，因为只解析正确的声明）
* step4. 执行代码段，有错则报错（比如变量未定义）
* step5. 如果还有下一个代码段，则读入下一个代码段，重复step2
* step6. 结束

***
> 最后再来个测试吧，您能解释下原因吗？

```javascript
//test 1
var tmp = new Date();

function f() {
  console.log(tmp);
  if (false) {
    var tmp = "hello world";
  }
}

f(); // undefined


//test 2  ES5的浏览器环境
function me() { console.log('I am outside!'); }
(function () {
  if (false) {
    // 重复声明一次函数f
    function me() { console.log('I am inside!'); }
  }

  f();//“I am inside!”
}());
```


