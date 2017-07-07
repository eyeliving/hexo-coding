---
title: redis在node.js的使用以及session
description: redis在服务器端node.js下的使用，session、以及持久化存储
keywords: redis，node.js，redis session，node.js session，ioredis的使用
date: 2016-09-24 01:56
tags: js node.js redis
---
<p>生产环境下，服务器宕机、故障、重启等等难免避免不了的。</p><p>而在这些未知的情况下，一些临时重要的数据很可能会丢失。</p><p>比如，这个情况，当某人，在客户端执行其认为很重要的操作时，突然出现bug，让其重新登陆</p><p>某人泪奔：尼玛！裤子都脱了，你让我去登录。。。</p><p>当然，这只是一个小小的特例。</p><p><br/></p><blockquote><p><span style="font-size: 16px; font-style: italic; font-weight: bold; line-height: 18px;"><span style="font-size: 16px; font-style: italic; font-weight: bold; color: rgb(51, 153, 204); line-height: 18px;">session</span></span></p></blockquote><p>现在我们利用redis的特性，持久化存储，在服务器端做session处理，</p><p>本人现用的是node.js V6，express4</p><pre class="brush:js;toolbar:false">var&nbsp;session&nbsp;=&nbsp;require(&#39;express-session&#39;);
var&nbsp;RedisStore&nbsp;=&nbsp;require(&#39;connect-redis&#39;)(session);
app.use(session({
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;secret:&nbsp;&quot;session_secret&quot;,
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;store:&nbsp;new&nbsp;RedisStore({
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;port:&nbsp;6379,
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;host:&nbsp;&quot;127.0.0.1&quot;,
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;db:&nbsp;0,
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;pass:&nbsp;&quot;&quot;,
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}),
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;resave:&nbsp;false,
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;saveUninitialized:&nbsp;false,
}));
//赋值，如果上述未成功，req.session是不存在的
req.session.user=&nbsp;{};
//使用
var&nbsp;user&nbsp;=&nbsp;req.session.user;
//...</pre><blockquote><p><span style="font-size: 16px; font-style: italic; font-weight: bold; line-height: 18px;"><span style="font-size: 16px; font-style: italic; font-weight: bold; color: rgb(51, 153, 204); line-height: 18px;"><br/></span></span></p><p><span style="font-size: 16px; font-style: italic; font-weight: bold; line-height: 18px;"><span style="font-size: 16px; font-style: italic; font-weight: bold; color: rgb(51, 153, 204); line-height: 18px;">利用redis在服务器端做缓存处理</span></span></p></blockquote><p>其中使用了ioredis，类似一个mongoose与mongo的存在<br/></p><p>连接redis：</p><pre class="brush:js;toolbar:false">var&nbsp;Redis&nbsp;=&nbsp;require(&#39;ioredis&#39;);

var&nbsp;client&nbsp;=&nbsp;new&nbsp;Redis({
&nbsp;&nbsp;port:&nbsp;&#39;6379&#39;,
&nbsp;&nbsp;host:&nbsp;&#39;127.0.0.1&#39;,
&nbsp;&nbsp;db:&nbsp;0,
&nbsp;&nbsp;password:&nbsp;&#39;&#39;,
});

client.on(&#39;error&#39;,&nbsp;function&nbsp;(err)&nbsp;{
&nbsp;&nbsp;if(err){
&nbsp;&nbsp;&nbsp;&nbsp;console.error(&#39;connect&nbsp;to&nbsp;redis&nbsp;error,&nbsp;check&nbsp;your&nbsp;redis&nbsp;config&#39;,&nbsp;err);
&nbsp;&nbsp;&nbsp;&nbsp;process.exit(1);
&nbsp;&nbsp;}
});</pre><p><br/></p><p>设置数据与获取数据</p><pre class="brush:js;toolbar:false">var&nbsp;get&nbsp;=&nbsp;function&nbsp;(key,&nbsp;callback)&nbsp;{
&nbsp;&nbsp;client.get(key,&nbsp;function&nbsp;(err,&nbsp;data)&nbsp;{
&nbsp;&nbsp;&nbsp;&nbsp;if&nbsp;(err)&nbsp;{
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return&nbsp;callback(err);
&nbsp;&nbsp;&nbsp;&nbsp;}
&nbsp;&nbsp;&nbsp;&nbsp;if&nbsp;(!data)&nbsp;{
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return&nbsp;callback();
&nbsp;&nbsp;&nbsp;&nbsp;}
&nbsp;&nbsp;&nbsp;&nbsp;data&nbsp;=&nbsp;JSON.parse(data);
&nbsp;&nbsp;&nbsp;&nbsp;callback(null,&nbsp;data);
&nbsp;&nbsp;});
};

//time&nbsp;参数可选，秒为单位
var&nbsp;set&nbsp;=&nbsp;function&nbsp;(key,&nbsp;value,&nbsp;time,&nbsp;callback)&nbsp;{
&nbsp;&nbsp;if&nbsp;(typeof&nbsp;time&nbsp;===&nbsp;&#39;function&#39;)&nbsp;{
&nbsp;&nbsp;&nbsp;&nbsp;callback&nbsp;=&nbsp;time;
&nbsp;&nbsp;&nbsp;&nbsp;time&nbsp;=&nbsp;null;
&nbsp;&nbsp;}
&nbsp;&nbsp;value&nbsp;=&nbsp;JSON.stringify(value);
&nbsp;&nbsp;if&nbsp;(!time)&nbsp;{
&nbsp;&nbsp;&nbsp;&nbsp;client.set(key,&nbsp;value,&nbsp;callback);
&nbsp;&nbsp;}&nbsp;else&nbsp;{
&nbsp;&nbsp;&nbsp;&nbsp;client.set(key,value,&#39;EX&#39;,time);
&nbsp;&nbsp;}
};
//现在我们把这些方法，放到一个文件(如redis_cache.js)，方便我们调用
exports.get&nbsp;=&nbsp;get;
exports.set&nbsp;=&nbsp;set;</pre><p><br/></p><p>在我们需要的地方使用，比如在缓存数据时</p><pre class="brush:js;toolbar:false">cache.get(&#39;my_data&#39;,function(err,data){
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;if(data){
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;//数据已经被缓存
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return&nbsp;data;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}else{
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;//从数据库中取，略。。。并设置缓存一天
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;cache.set(&#39;my_data&#39;,iodata,60*60*24*1);
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return&nbsp;iodata;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}
});</pre><p><br/></p><p>上述就是一些redis在node.js下的缓存处理</p><p>略些浅见，有失误之处，请诸位扶正</p>