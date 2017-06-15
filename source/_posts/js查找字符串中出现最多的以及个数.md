---
title: js查找字符串中出现最多的以及个数
date: 2017-02-06 12:00
tags: javascript
---
一个非常常见的前端面试题，后端也应该会经常遇到吧。

在这里，总结下，希望正在找工作的朋友们会用到。

如果有更好的方法，也请下边留言。

如：找出下面字符串中出现最多的是哪一个，以及出现了几次。

```javascript
var str = 'fdgdfgsdfdgtertreuujjktyitidssadrt';
/*
 * 解法一：字符串方式
 * */
(function (str) {
      var obj = {};
      var num = 0;
      var value = '';
      for ( var i = 0; i < str.length; i++ ) {
          // 如果obj中不存在某个字符属性，就把它添加进去
          if (!obj[str[i]]) {
              obj[str[i]] = [];
          }
          // 把值一一填入到obj[a[i]]中
          obj[str[i]].push(str[i]);
      }
      // 找出最多项的那个
      for ( var i in obj ) {
           if (num < obj[i].length) {
                num = obj[i].length;
                value = i;
           }
       }
        var tips = '字符最多的是：' + value + '，出现了：' + num + '次';
        alert(tips);
})(str);
```

2016-02-06日 inqiu.com更新
```javascript
/*
 * 解法二：正则方式
 * */
var str="vbbbbhybbfhbbgbbb";
var arr=str.split("");
str=arr.sort().join("");
var value=""; //存放出现最多的字符
var index=0; //存放次数
var re=/(\w)\1+/g;
str.replace(re,function($0,$1){ //$0代表整体，$1代表第一个子项
 if(index<$0.length){ //$0:每一组重复的元素
  index=$0.length;
  value=$1;
 }
})
alert('出现最多的字符是：'+value+',出现次数是'+index);	
```
