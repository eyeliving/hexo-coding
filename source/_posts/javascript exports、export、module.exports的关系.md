---
title: javascript exports、export、module.exports的关系
description: javascript exports、export、module.exports的关系总结
keywords: javascrip、exports、export、module.exports
date: 2017-09-11 18:00
tags: javascript
---
> **常见的用法**

export：

```javascript
export function aa(){
     console.log('export aa')
}

export default function bb(){
     console.log('export default bb')
}
```

exports：
```javascript
exports.cc = function() {
    console.log('exports cc');
};
```

module exports：
```javascript
module.exports.dd = function(){
    console.log('module exports dd')
}
//or
//module.exports = {dd}
```
export与exports之间的关系就不再说明了

exports 和 module.exports的关系是:

* exports 初始化时是一个指向`module.exports`对象的变量，是它的引用，即：`exports => module.exports => {}`

  真正的接口是`module.exports`，exports只是辅助性的，直白点，就是个备胎。

* `module.exports`如果没有被修改，则`exports`与`module.exports`还是引用同一个对象。反之，则两者之间的关系就断开了。

* `exports`是es7的规范，而`module.exports`是commonjs的规范。

* 当你搞不明白`export`和`module.export`的关系的时候，就一直使用`module.export`好了

> **怎么调用这些方法**

```javascript
//定义test.js
export function aa(){
     console.log('export aa')
}
export default function bb(){
    console.log('default bb')
}
//调用test.js
import {aa} from './test.js'
import bb from './test.js'

//定义test.js
exports.cc = function() {
    console.log('exports.cc ');
};
exports.dd = function() {
    console.log('exports.dd ');
};
//调用test.js
import _obj from './test.js'
//_obj是一个包含cc、dd方法的对象

//定义test.js
module.exports.ee = function(){
   console.log('module.exports.ee fn')
}
module.exports.pp = function(){
   console.log('module.exports.pp fn')
}
//调用test.js
//引入test.js的全部方法，_obj是一个包含ee、pp方法的对象
import _obj from './test.js'

//调用test.js中的pp方法
import {pp} from './test.js'

//定义test.js
function ee(){
        console.log('module.exports.ee fn')
}
function pp(){
        console.log('module.exports.pp fn')
}
module.exports = {
    ee,pp
}

//调用test.js
//调用test.js中的pp方法
import {pp} from './test.js'
```