---
title: javascript闭包，闭包的优点与弊端
description: javascript闭包，闭包的优点，闭包的弊端缺点
keywords: javascript闭包，闭包的优点，闭包的弊端缺点
date: 2017-01-23 09:30
tags: javascript
---
> JavaScript闭包

JavaScript闭包,前端必然会遇到的一个技术点

那么问题来了,什么是闭包?其存在必定有其原有

* 为什么要用闭包？
* 它的优点是什么？
* 经常会用在那些场景？
* 用了它之后,又有弊端有哪些？

下边我们以此来通俗易懂的来解答这个问题

**什么是闭包？**

就是函数嵌套函数，内部函数可以访问外部函数的参数和变量，并且外部函数的变量和参数不会被垃圾回收机制所收回

```javascript
var fn = (function (i) {
     return function () {
         i++;
         alert(i);
     };
})(1);
fn();    // 2
fn();    // 3
```
**闭包的好处？**

    1.使得变量长期处于内存
    2.避免了全局变量的污染
    3.将变量成员私有化
	
```javascript
var fn = (function () {
        var i = 1;
        function bbb() {
            i++;
            alert(i);
        }
        function ccc() {
            i++;
            alert(i);
        }
        return {
            bbb : bbb,
            ccc : ccc,
        }
})();
fn.bbb();
fn.ccc();
```

**闭包的场景？用法？**

     1.模块化代码
     2.在循环中直接找出对应元素的索引
	 3. ...

```javascript
 /*
    * 引子：
    * 不使用闭包的情况下，看看索引
    * 你会发现，全部为4，
    * 这是因为，for循环完毕了，才进行点击事件
    * */

window.onload = function () {
        var oUl = document.getElementById('ul1');
        var aLi = oUl.getElementsByTagName('li');
        for ( var i = 0; i < aLi.length; i++ ) {
            aLi[i].onclick = function () {
                alert(i);   // 全部为4
            }
        }
}

 /*
    * 我们通过闭包来传参，以达到找到对应元素索引
    *
    * */

window.onload = function () {
        var oUl = document.getElementById('ul1');
        var aLi = oUl.getElementsByTagName('li');
        for ( var i = 0; i < aLi.length; i++ ) {
            aLi[i].onclick = (function (i) {
                return function () {
                    alert(i);
                }
            })(i);
        }
}
```

**闭包的弊端**

闭包的弊端，最为著名的应该是内存泄漏了吧，因还未准备充分，那么请等待更新。