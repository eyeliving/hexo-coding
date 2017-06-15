---
title: js 中continue、break、return的区别
date: 2017-02-08 18:27
tags: javascript
---
> js 中continue、break、return的区别
  * continue:结束本次循环，循环变量继续递增或递减，开始下次循环
  * 
  * break:结束循环，执行循环后面的代码(如果有的话)
  * 
  * return:结束函数的执行，return后面的所有代码都不再执行

```javascript
 function inqiu() {
     for (var i = 0; i <= 10; i++) {
                if (i == 3) {
                    //test1.continue;
                    //test2.break;
                    //test3
					return;
                }
                document.write(i);
                document.write('</br>');
      }
      alert('inqiu() End');
}
```
test1.以次打印出：`1  2  4  5  6  7  8  9  10` 以及执行`alert('inqiu() End');`

test2.以次打印出：`1  2`

test3.以次打印出：`1  2`
