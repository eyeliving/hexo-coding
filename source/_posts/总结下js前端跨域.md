---
title: 总结下js前端跨域
description: 总结下js前端跨域各种方案
keywords: js，前端，跨域
date: 2017-01-27 21:19
tags: js
---
## 总结下js前端跨域

1.jsonp
直接举个栗子
```javascript
//得到航班信息查询结果后的回调函数
var flightHandler = function(data){
	alert('你查询的航班结果是：票价 ' + data.price + ' 元，' + '余票 ' + data.tickets + ' 张。');
};
// 提供jsonp服务的url地址（不管是什么类型的地址，最终生成的返回值都是一段javascript代码）
var url = "http://flightQuery.com/jsonp/flightResult.aspx?code=CA1998&callback=flightHandler";
var script = document.createElement('script');
script.setAttribute('src', url);
document.getElementsByTagName('head')[0].appendChild(script); 

//然后服务器端拼接callback并返回
flightHandler({
    "code": "CA1998",
    "price": 1780,
    "tickets": 5
});
```
2.html5 postMessage
```javascript

//发送
window.parent.postMessage(data||{},domain||'*');

//接收
if(window.addEventListener){
	window.addEventListener("message", function(r){
		//do code[r.data]
	},false);
}else if(window.attachEvent){
	window.attachEvent("onmessage", function(r){
		//do code[r.data]
	});
}
```
3.CORS 
CORS是一个W3C标准，全称是"跨域资源共享"（Cross-origin resource sharing）
CORS需要浏览器和服务器同时支持。
目前，所有浏览器都支持该功能，IE浏览器不能低于IE10。
整个CORS通信过程，都是浏览器自动完成，不需要用户参与。

对于开发者来说，CORS通信与同源的AJAX通信没有差别，代码完全一样。

浏览器一旦发现AJAX请求跨源，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。
因此，实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信。

```javascript
function createCORS(method, url){
    var xhr = new XMLHttpRequest();
    if('withCredentials' in xhr){
        xhr.open(method, url, true);
    }else if(typeof XDomainRequest != 'undefined'){
        var xhr = new XDomainRequest();
        xhr.open(method, url);
    }else{
        xhr = null;
    }
    return xhr;
}
var request = createCORS('get', 'http://www.baidu.com');
if(request){
    request.onload = function(){
        ......
    };
    request.send();
}
```
4.websocket
实际开发中没用过，不做说明了。


## 总结
JSONP只支持GET请求，JSONP的优势在于支持老式浏览器。
CORS与JSONP的使用目的相同，但是比JSONP更强大。CORS支持所有类型的HTTP请求。
