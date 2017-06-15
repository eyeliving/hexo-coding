---
title: nginx设置301跳转 并检测是否设置成功
description: 利用nginx设置301跳转，并检测是否设置成功
keywords: 检测301设置，检测301成功，301跳转设置
date: 2016-09-24 10:53
tags: nginx
---
<p><strong>如题：inqiu.com &nbsp;www.inqiu.com</strong></p><p>现在我们把inqiu.com设置301跳转到www.inqiu.com，也就是说，当用户输入inqiu.com后自动跳转到www.inqiu.com<br/>
 反之，如果想把www.inqiu.com跳转到inqiu.com下，下边的情况同样适用。</p><pre class="brush:python;toolbar:false">server&nbsp;{
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;listen&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;80;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;server_name&nbsp;&nbsp;inqiu.com&nbsp;www.inqiu.com;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;if&nbsp;($host&nbsp;!=&nbsp;&#39;inqiu.com&#39;&nbsp;)&nbsp;{
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;rewrite&nbsp;^/(.*)$&nbsp;http://inqiu.com/$1&nbsp;permanent;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;location&nbsp;~&nbsp;.*&nbsp;{
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;proxy_pass&nbsp;http://localhost:3000;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;proxy_set_header&nbsp;X-Real-IP&nbsp;$remote_addr;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}
&nbsp;&nbsp;&nbsp;&nbsp;}</pre><p><br/></p><p>设置成功之后，重启reload nginx。</p><p>在浏览器里输入测试，结果现在已经实现了301的跳转。</p><p>如果还不放心，还可以用工具测试一下，比如用curl，或者推荐个在线工具http://tool.chinaz.com/pagestatus/</p><p>在命令行下输入：curl -I www.inqiu.com</p><pre class="brush:python;toolbar:false">$&nbsp;curl&nbsp;-i&nbsp;www.inqiu.com
HTTP/1.1&nbsp;301&nbsp;Moved&nbsp;Permanently
Server:&nbsp;nginx
Date:&nbsp;Sat,&nbsp;24&nbsp;Sep&nbsp;2016&nbsp;01:42:47&nbsp;GMT
Content-Type:&nbsp;text/html
Content-Length:&nbsp;178
Connection:&nbsp;keep-alive
Location:&nbsp;http://inqiu.com/</pre><p><br/></p><p>再试试不带3w的情况下，输入：curl -I inqiu.com</p><pre class="brush:python;toolbar:false">$&nbsp;curl&nbsp;-I&nbsp;inqiu.com
HTTP/1.1&nbsp;200&nbsp;OK
Server:&nbsp;nginx
Date:&nbsp;Sat,&nbsp;24&nbsp;Sep&nbsp;2016&nbsp;02:35:52&nbsp;GMT
Content-Type:&nbsp;text/html;&nbsp;charset=utf-8
Content-Length:&nbsp;2997
Connection:&nbsp;keep-alive
Vary:&nbsp;Accept-Encoding
ETag:&nbsp;W/&quot;QyWVldqXm6Vqkyk2lCU66w==&quot;
Vary:&nbsp;Accept-Encoding</pre><p><br/></p><p>如上图所示，分别返回 301 Moved Permanently、200 OK，说明nginx的301跳转已经设置成功。</p><p>备注：其中的参数permanent表示返回301永久重定向, 地址栏显示重定向后的url，爬虫会更新此url。</p><p>与此对应有&nbsp;redirect返回302临时重定向，地址栏显示重定向后的url，爬虫不会更新此url。</p>