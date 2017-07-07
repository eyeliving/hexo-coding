---
title: 了解http、https、http1、http2
description: 了解http
keywords: 了解http
date: 2017-07-06 22:17
tags: http
---
## 知其然知其所以然

### URL(Uniform Resource Locator) 地址用于描述一个网络上的资源，基本格式如下
```javascript
	schema://host[:port#]/path/.../[;url-params][?query-string][#anchor]
	
　　scheme               指定低层使用的协议(例如：http, https, ftp)
　　host                 HTTP服务器的IP地址或者域名
　　port                 HTTP服务器的默认端口是80，这种情况下端口号可以省略。如果使用了别的端口，必须指明，例如 http://www.inqiu.com:8080/
　　path                 访问资源的路径
　　url-params			 请求参数
　　query-string         发送给http服务器的数据
　　anchor               锚点

//URL 的一个例子
http://www.inqiu.com/blog/test?id=8079&name=sviergn&x=true#stuff
Schema: http
host: www.inqiu.com
path: /blog/test
URL params: id=8079
Query String: name=sviergn&x=true
Anchor: stuff
```


### http
1.http协议
协议是指计算机通信网络中两台计算机之间进行通信所必须共同遵守的规定或规则，超文本传输协议(HTTP)是一种通信协议，它允许将超文本标记语言(HTML)文档从Web服务器传送到客户端的浏览器
目前我们使用的是HTTP/1.1 版本

http协议是无状态的，同一个客户端的这次请求和上次请求是没有对应关系，对http服务器来说，它并不知道这两个请求来自同一个客户端。 为了解决这个问题， Web程序引入了Cookie机制来维护状态.

Http协议定义了很多与服务器交互的方法，最基本的有4种，分别是GET,POST,PUT,DELETE. 一个URL地址用于描述一个网络上的资源，而HTTP中的GET, POST, PUT, DELETE就对应着对这个资源的查，改，增，删4个操作。 我们最常见的就是GET和POST了。GET一般用于获取/查询资源信息，而POST一般用于更新资源信息.

### http协议

### http1 http2

### https
HTTP访问，用户只需要完成TCP三次握手，建立TCP连接就能够直接发送HTTP请求，获取应用层数据。
而HTTPS=HTTP+SSL，也就是说HTTPS比HTTP多消耗的服务器资源主要就是看SSL/TLS消耗了多少服务器资源。

http请求的生命周期