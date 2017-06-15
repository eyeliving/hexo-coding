---
title: CSS基础常规面试（一）
description: css一些80%会被问到的问题，面试题库
keywords: css考题、css基础、css常规面试题库、css面试答案
date: 2016-09-29 16:53
tags: css
---
> 盒子模型差异 ？  

IE8以下版本的浏览器的盒模型，定义的元素的宽高不包括padding和border。

标准的css盒子模型：他的宽度=内容的宽度+border+padding

比如：.box{width:100px;border:10px solid #ccc;padding:10px}

那么标准的css盒子模型中的box这个的总的宽度就是
140px=100+10+10+10+10；

但是低版本ie的盒子模型box的宽度还是100px。  

---
> div居中定位的常规考法     

1.如何居中div？

2.如何居中一个浮动元素？

3.如何让绝对定位的div居中？

4.如何让一个div水平和垂直居中？

下边以此对应答案  

答案1：
```html
<div class="div1" style="background:red;">1.如何居中div？（inqiu.com）</div>
<style>
.div1{width:200px;margin: 0 auto;}
</style>
```
答案2：
```html
<div class="div2" style="background:green;float:left;">2.如何居中一个浮动元素？（inqiu.com）</div>
<style>
.div2{width:200px;position:relative;left:50%;margin-left:-100px;}
/*position:relative;相对定位，根据父级定位，无父级则以BODY*/
</style>
```

答案3：
```html
<div class="div3" style="background:blue;position:absolute;">3.如何让绝对定位的div居中？（inqiu.com）</div>
<style>
.div3{width:200px;margin:0 auto;top:0;right:0;left:0;bottom:0;height:30px;}
</style>
```
答案4：
```html
<div class="div4" style="background:black;position:absolute;">4.如何让一个div水平和垂直居中？（inqiu.com）</div>
<style>
.div4{width:200px;top:50%;left:50%;margin-left:-100px;margin-top:-15px;height:30px;}
</style>
```

> 伪类和伪元素的区别是？分别写出几个

这点说出个大概就可以了，但是一定要写出来几个分别有代表性的！

* 伪类用于向某些选择器添加特殊的效果。（如链接的各个状态，:hover，:vistited，:active等，还有一些常用:first-child，:nth-child()，:focus等等）

* 伪元素用于将特殊的效果添加到某些选择器。（如:before，:after，:first-line，:first-letter）

伪类一开始单单只是用来表示一些元素的动态状态，典型的就是链接的各个状态(LVHA)。

随后CSS2标准扩展了其概念范围，使其成为了所有逻辑上存在但在文档树中却无须标识的“幽灵”分类。

伪元素则代表了某个元素的子元素，这个子元素虽然在逻辑上存在，但却并不实际存在于文档树中。

伪类前面是一个冒号，伪元素前面是两个冒号。

但因为兼容性的问题，所以现在大部分还是统一的单冒号。



