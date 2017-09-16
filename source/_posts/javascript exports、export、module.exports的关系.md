---
title: javascript exports、export、module.exports的关系
description: javascript exports、export、module.exports的关系总结
keywords: javascrip、exports、export、module.exports
date: 2017-09-11 18:00
tags: javascript
---
> 常见的用法

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

> 怎么调用这些方法

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

> require/exports与import/export

require/exports是CommonJS(在Node中实现), import/export是es6的模块

node目前为止还为支持import


规范不一样，require是CommonJs,AMD,CMD规范中定义的模块请求方式，import是es6规范定义的模块请求方式，从规范与实现定义来说 require是运行时加载，import是编译时加载、静态加载，从底层的运行来讲，require是在程序运行的时候去解析而import是在编译的时候去做解析请求包，require是请求整个包对象而import是只请求模块中需要的请求的部分。现在import应该还只能算是ES6的语法规范，大多数浏览器还未支持，babel打包出来最终还是require。

注：import命令是编译阶段执行的，在代码运行之前，同一个import只会执行一次
```javascript
foo();

import { foo } from 'my_module';
```
上面的代码不会报错，因为import的执行早于foo的调用。

这种行为的本质是，import命令是编译阶段执行的，在代码运行之前。

```javascript
import { foo } from 'my_module';
import { bar } from 'my_module';

// 等同于
import { foo, bar } from 'my_module';
```

