---
title: 了解angular1、angular2+，vue，react
description: 了解angular1、angular2+，vue，react
keywords: 了解angular1、angular2+，vue，react
date: 2017-07-10 21:17
tags: js angular react vue
---
## 知其然知其所以然
任何一门语言的学习都需要了解它的核心概念，处理流程和实现原理，这样才能在编译出现问题或者没有达到我们想要的结果时，及时准确的发现问题所在。

### angular1.*
区分：angular1.* 全称是angular.js，而之后的版本（采用语义化）angular2，angular3，angular4统称为angular
数据绑定采用什么机制？

脏检查机制。

Angular 在 scope 模型上设置了一个监听队列，用来监听数据变化并更新 view 。每次绑定一个东西到 view 上时 AngularJS 就会往 $watch 队列里插入一条 $watch，用来检测它监视的 model 里是否有变化的东西。当浏览器接收到可以被 angular context 处理的事件时，$digest 循环就会触发，遍历所有的 $watch，最后更新 dom。

举个栗子：
```javascript
<button ng-click="val=val+1">increase 1</button>
```
ng-click 时会产生一次更新的操作（至少触发两次 $digest 循环）
	1. 按下按钮
	2. 浏览器接收到一个事件，进入到 angular context
	3. $digest 循环开始执行，查询每个 $watch 是否变化
	4. 由于监视 $scope.val 的 $watch 报告了变化，因此强制再执行一次 $digest 循环
	5. 新的 $digest 循环未检测到变化
	6. 浏览器拿回控制器，更新 $scope.val 新值对应的 dom

$digest 循环的上限是 10 次（超过 10次后抛出一个异常，防止无限循环）。

如果在controller当中，使用原生的方法（比如原生的onchange事件），是不会进行脏检查的。
需要执行`$scope.$apply()` 另使用`$timeout` 也可。
Angular 的 digest 的触发不是定时的，只有在指定的事件触发之后才会进入 $digest（ng-click、$timeout等）。

依赖注入原理：***

***

### angular2+
数据绑定采用什么机制？


###vue2.*
数据绑定的原理
vue的数据双向绑定是通过数据劫持和发布者、订阅者结合实现的。

