---
title: YY游戏云平台在AngularJS上的实践总结
description: YY游戏云平台在AngularJS上的实践总结
keywords: YY游戏云平台在AngularJS上的实践总结
date: 2016-12-06 18:19
tags: js
---
> 导语

云平台控制台，是一个典型的管控CRUD系统，用于管理各种IaaS资源。

为了使前端能达到仿客户端体验，同时保障代码架构清晰规范，易维护，最终多玩YY选择了AngularJS（1.x）作为云控制台的前端框架。

本文主要围绕AngularJS（1.x），介绍多玩YY在开发控制台过程中的点点滴滴。

> 为什么选择AngularJS

轻松构建单页面应用

可以说，这是我们最终选择AngularJS的重要原因，如果你希望构建一个结构清晰、可维护、开发效率高、体验好的单页应用，AngularJS是相当不错的框架。

单页面应用的魅力

**什么是单页面应用？**

单页应用，指一种基于Web的应用或者网站，页面永远都是局部更新元素，而不是整页刷新，给用户的感觉就像整个网站都是一个页面。

当用户点击某个菜单或者按键时，不会跳转到其他页面，前端会从后端获取对应页面的数据而不是HTML，之后在页面中需要更新内容的地方，局部动态刷新，而如果是传统的多页网站，当用户访问不同的页面时，服务器会直接返回一个HTML，然后浏览器负责将这个HTML展现给用户。目前，

大部分云控制台，都是单页应用架构，单页应用能带来一种更近似客户端，而不是网页的体验。

单页面应用网站，在体验方面，具有如下优点：

做“页面跳转”时，永远都是局部动态刷新，用户不会感觉整个屏幕闪了一下，而仅仅是需要变化的区域做了局部刷新。

例如两个不同的页面，假设页面元素都是一样的，只是元素中的文字不一样（例如每个页面都有一个面包屑，一排按钮及一个表格，这几个元素的布局也是一样的），当用户跳转到另外一个页面时，会看到整个页面并没有重新渲染，只是文字发生了变化。简单地说，这有点类似使用App，永远都是局部发生变化，你见过哪个App，当点击到不同的功能视图时，整个屏幕会白屏闪一下的么？

URL可收藏，可回退。

如果浏览器的URL一直不变，那还不能称为真正的单页应用。不同的功能，不同的资源页面，对应的URL是不一样的。

当用户跳转到另外的功能时，会发现URL会变成对应的值；通过回退键，也可以回到之前浏览的页面。

这个特性有什么用呢？如果URL不会随着功能而变化，当用户刷新当前页面时，就会回到之前的默认页面，而不是预期的当前功能页面，同样的，URL也不具备可收藏性，因为点开之前收藏的URL，永远都是网站的默认页面。这对一些强交互的管控系统来说，体验不好。

功能切换时快速流畅。快速流畅主要因为两个原因：

	一是页面都是局部刷新，从用户感官来说更快
	
	二是和后台通信的内容，都是数据，而不是页面模板，请求量更少；而传统网站，在访问不同页面时，服务端返回的是HTML，体积更大，而且还需要一直重复加载JavaScript、CSS文件。
	
因为网站的单页化，可以更好地使用全局类的交互（做页面切换时，需要一直保持不变的交互）。

例如，页面上需要显示某次耗时操作的进度，例如上传文件进度，耗时操作的当前状态等，你可以在页面最右侧固定显示进度。当用户访问单页应用时，他会明白，当点击其他模块时，这个右侧的通知栏不会消失，会固定显示。

而如果是多页网站，用户则会困惑，担心自己跳转到其他页面后，进度通知就会消失。

AngularJS是开发单页应用的利器

单页面应用对于代码分层，结构清晰有更高的要求，而AngularJS是一个MVVM框架，其自身的约定，减少了我们写出“一锅粥”代码的可能性（在下面讨论“编写更易维护的代码”时会详述）。

AngularJS的著名第三方组件UI-Router，是一个控制页面路由的组件，它支持我们快速搭建单页应用（AngularJS本身的路由功能也可以，但功能会稍微弱一些）。

AngularJS的检查更新机制，使页面刷新时更加快速自然。 当数据“可能”发生变化时，AngularJS会检查出所有变化的元素，再“一次性”刷新所有变化的UI元素。


**编写更易维护的代码**

很多人经常会抱怨，不同水平的人凑在一起写JavaScript，到最后项目经常就是一锅粥，同一个JavaScript文件里面，各种各样的逻辑都混在一起，要增删功能，简直是恶梦。无规矩不成方圆。

作为框架，AngularJS无疑能大大改善这种状况，使得项目整体的分层明了，职责清晰。

在笔者看来，AngularJS能够帮助我们编写更易维护的代码，

	一是因为其“关注点分离”的理念
  
	二是因为其强大的特性为项目节省了不少代码量，从而降低程序员犯错的概率。

**关注点分离**

关注点分离是AngularJS的一大设计哲学。

所谓关注点分离，指的是各个逻辑层职责清晰明确。

例如，当你需要修改甚至替换展现层时，无需关注业务层是怎么实现的。

在AngularJS中，服务层（Ajax请求）- 业务层（Controller）- 展现层（HTML 模板）- 交互层（animation）这些都有对应的基础组件。

不同组件职责不同，也很难将本属于B组件的职责放到A组件上去实现。

举几个例子：

HTML及Controller需要协同工作，但职责分明。视图、交互层面的逻辑，例如展示隐藏某些元素，是HTML模板的职责，Controller只能用于数据初始化。

如果你反其道而行，想在Controller中做HTML模板做的事情，将会非常别扭。这一点非常重要，传统的JavaScript代码，经常会出现的情况，就是JavaScript里面会有大量DOM操作的逻辑，同时还有大量数据操作相关的逻辑，这些逻辑耦合到一起，当需要单独重构数据层或者视图层时（例如项目UI改版），都会捉襟见肘，同时，由于JavaScript代码量的迅速膨胀，维护起来也会很麻烦。

你无法将后台通信逻辑放到Controller中实现，而要放到Factory中。

后台通信逻辑，一般要做成公用的。而由于Controller之间是不能相互调用的，所以你也不可能将后台通信逻辑放到其中一个Controller，然后其他Controller来调用这个Controller暴露的接口。

唯一的办法，就是将后台通信逻辑放到Factory或者Service中。

Filter及Directive看似都可以用于数据转换，但实则不同。由于Filter只能做数据格式化，不支持引入模板，所以公用的UI交互，涉及到DOM元素或者需要引入HTML模板时时，也只能通过Directive来实现。

综上所述，AngularJS项目，其展现层、交互层的逻辑，都是在HTML或者指令中，服务层（后台通信），只适合出现在Factory（或者Service）中，而业务层则由Controller来负责。这样每层的逻辑都是轻薄的，而不是纠结在一起。 如果你只是要优化展示逻辑，那改改HTML就可以了，不用去管Controller是怎么写的。这

一点我们有亲身体会。项目开发过程中，我们重构了视觉效果，所有的HTML都要重写。但在重构时，我们的Controller、后台通信（Service）、Filter基本都不用改，只要改HTML就行了。而如果项目是用jQuery写的，显然不可能做到，你需要重新为新的HTML增加一些可供jQuery选择器使用的class或id，然后需要在JavaScript里面绑定事件，根据新的CSS样式名来写新的交互效果，而在AngularJS上，有些不用做了（例如为了jQuery选择器，而为HTML元素增加新的class、id；在JavaScript中绑定事件），有些（例如交互效果）则只要改HTML就行，而不是改JavaScript。


**AngularJS为我们省去的代码量**

对于任何项目来说，代码臃肿、冗余是可维护性的天敌。

因此，实现同样的功能，代码量越少，抽象度越高，冗余度越低，在某种程度上意味着项目更方便维护。而能减少代码量，也是AngularJS被推崇的一大优点。让我们来看看，它是如何减少了代码量的：

首先，作为一个大而全的框架（双刃剑，有利有弊)，AngularJS提供的诸多特性，使我们可以更专注于业务代码的编写。

其次，AngularJS双向数据绑定的特性，将我们从大量的值绑定代码中解放出来。

和jQuery对比，AngularJS不用为了选择某个元素，而刻意为HTML加上一些跟样式无关的class、2-2-2. id；

不用写一堆从HTML元素中取值，设值的代码；不用在JavaScript代码中绑定事件；不用在JavaScript值发生变化时写代码去更新HTML对应的值。

双向数据绑定，让我们告别很多简单无趣的绑定事件、绑定值的代码。
		
Directive、Filter、Factory等，天然的就是一个个可以复用的组件，减少了冗余重复代码。一些需要公用的逻辑，如果放在Controller中，都会相当别扭，就这样被AngularJS“逼着”，把公用逻辑都放到Directive、Filter、Factory中去。


***
> 开发心得总结

**我理解的AngularJS基础组件**

化繁为简，几大基础组件的使用场景

首先我们需要理清AngularJS几个组件的使用场景。

AngularJS的一个毛病，就是新概念，新特性太多，新手一下子要了解这么多，学习曲线略陡。

为了帮助大家理解，总结下我理解的几大组件使用场景。
请求资源与数据缓存的东西放进Service。Factory、Service本质上都是Provider的语法糖，两者只是使用方式有所不同，建议大家直接都用Service，ES6 class更容易，之后想平滑迁移到Angular2也会更容易，同时也能避免团队成员在选择Service还是Factory时产生困惑。

数据需要格式化的东西用Filter处理。例如把status值转化为中文值，把时间戳转成时间字符串之类。

需要公用的DOM操作，放在指令中去写。另外，如果需要引入jQuery组件，也可以写个指令把jQuery组件初始化代码放进去。

Controller与视图按照一对一的关系维护，在Controller内初始化Scope对象与在Scope上添加方法（行为），为ViewModel做赋值。其他所有过程都不应该出现在Controller中。

Controller中不应该出现和页面展示、交互相关的代码。例如展示隐藏某些元素之类，这些应该是HTML模板或者指令负责。代码越薄越好。

全局常量值放到Constant。

指令（Directive）魔法

指令这个特性，用“魔法”一词来形容它，都不为过。

解决的痛点：一言以蔽之，指令提供了一套前端组件化的方法及约定，这使得编写，使用UI组件更加方便了。相对于jQuery，它解决了以下痛点：

动态生成了HTML元素后，不用再手动去为其加上JavaScript特性。

举个例子：HTML原生的checkbox框比较丑，在jQuery时代，可以将checkbox替换成自定义的效果，如果是页面一开始就有的checkbox，我们可以在document.ready的时候调用自定义checkbox的初始化方法。但是，如果这个checkbox是动态生成的，在每个动态生成checkbox的地方，我们都得去调用checkbox的初始化方法，相当麻烦。但用了AngularJS的指令，就不会有这个问题了，只要在模板的chceckbox中加上指令，不管这个模板是动态变化的还是静态的，无需通过业务代码来逐个调用初始化方法，呈现给用户的，就已经是AngularJS替换后的checkbox效果。

一个组件的HTML和JavaScript，是一个整体，而不是割裂的（题外话，这一点React做得比Angular1.x还要好）。

基于jQuery的UI组件，其引入方法，经常是这样的，首先，要求你自己copy一段指定的HTML，然后再调用初始化方法。而指令则支持定义对应的模板HTML，用户在引入时，可能只要写一个指令标签，就会自动生成N行的HTML及绑定对应的JavaScript效果。

当然，理论上jQuery也能做到这样，但是会比Angular的实现麻烦许多。

应用、移除UI特性时方便直观。

假设有这么一个需求，给一个普通输入框增加输入限制，只能输入特定字符（如字母数字），写好对应指令，只要给这个input输入框加上这个指令标签，就能马上应用这个特性，之后要移除，只要把标签去掉就好。

相比之下，jQuery就会麻烦多。jQuery下，一般是通过元素选择器来绑定JavaScript效果。因此，在添加该特性时，你需要考虑给对应的输入框指定一个合适的元素选择器。移除特性时，你要考虑：有可能你在动态生成输入框的地方，都加了这些初始化代码，这些JavaScript都需要移除；如果元素选择器用的是class，得考虑是不是其他输入框也有这个class，如果是，那么移除代码时也会影响到其他输入框。

**技巧**

如果你迫不得已需要引入jQuery组件，你可以写一个指令把它包装起来，在该指令中初始化组件。

要注意require参数中的值是驼峰的，在HTML中就得转成对应的中划线命名，例如有require参数phoneKey，那么HTML中应为phone-key=”xxx”。

虽然这个道理很浅显，但经常一不小心就会弄错了，然后发现在指令内部怎么着都拿不到require参数。

如果你在link中加了elm.bind(‘click’)，当click回调函数中，作用域的值发生变化，记得调用scope.$apply()，否则值变化不会生效。

**文件、目录约定**


*目录结构*

第三方库、CSS、图片放置到哪个目录，不在本文讨论范围，这里略过。需要进一步说明的，是业务代码目录。

我们将系统自身的JavaScript、HTML模板都放在pages目录，其中子目录common放置公用的JavaScript及模板；
其他子目录，以功能模块名作为目录名，然后将这个模块相关的JavaScript及模板放在其中。

这样开发同个模块功能时，可以方便地在HTML及对应JavaScript之间切换。

有些代码规范可能还会建议在模块这一级目录下，再根据AngularJS的几大组件Controller、Filter、Service等，创建不同的子目录，例如模块A/controller，模块A/service之类，我们则将所有JavaScript及HTML放在同一级，这样做主要有几个原因：

我们的项目，平均每个模块只有十来个文件，特别是每个模块一般只有一个Filter及Service，为了这一个文件创建一个目录，显得多此一举。

通过文件名，已经可以很方便地区分不同的JavaScript组件类型及HTML模板类型，同时，由于IDE一般会按照文件名字母排序，所以相同功能的JavaScript及HTML会挨在一起，查找对应的模板或JavaScript代码会方便很多。


*文件名约定*

这个约定对于Angular来说，特别重要。具体的约定是：

例如，为“防火墙”模块开发“创建防火墙”的功能，它的Controller，对应的JavaScript为：firewall.create.ctrl.js，对应的HTML模板为：

firewall.create.ctrl.HTML。为了文件名书写的方便，定义了组件的简写：controller -> ctrl ；factory,service -> svr ； filter-> fil ； directive -> dire。

这样约定有两个显而易见的优点：

通过文件名，就能知道对应模块、AngularJS基本组件类型、是模板HTML还是JavaScript 。

相同功能的JavaScript及HTML，会挨在一起（如果IDE是按照文件命名排序）。


*与后端服务器通信*

根据后台接口规范，结合AngularJS自身能力，我们做了一些封装，使接口请求逻辑变得非常简单。具体问题具体分析，先看看我们的后台接口的标准响应格式是怎样的，前端会按照这个接口返回格式做一些定制：

```javascript
{ errno:0, errmsg:"", data:[ { id:"test", name:"test" }
    ]
}
```

我们项目服务端的标准响应是一个json，通过errno描述此次请求的结果码，通过errmsg描述出错的原因（假如请求出错的话），通过data返回正常数据。

*出错处理*
当接口返回的errno!=0时，说明接口返回异常（系统异常或用户输入错误），这时我们希望能弹框提示用户“出错了”。显然，如果在每个接口请求逻辑中，都去写这个逻辑，会非常累赘，所幸AngularJS提供了拦截器的功能，我们只要写一个拦截器，就可以对所有的异常返回做统一处理。 

首先，我们需要显式地抛出HTTP错误。因为当后台逻辑出错或者用户输入参数有误时，返回的HTTP状态码都是200（这只是我们项目的约定），AngularJS并不会认为200是出错的情况，因此，我们需要做点小动作。

```javascript
$httpProvider.defaults.transformResponse.push(function (responseData) {
            if (responseData.errno != 0) {
                throw responseData;
            }
            ……
      });
AngularJS的
httpProvider.interceptors捕获这个异常并弹框，代码如下：
$httpProvider.interceptors.push(function () {
  return {
     responseError: function (response) {
       if (response) {
          if (response.hasOwnProperty("errmsg")) {
              if (response.errno > 0) {
                                alert(response.errmsg);
            } else {
             alert("系统维护中，请稍候重试");
            }

         }
         else {
            if (response.status == 404) {
               alert("抱歉，后台服务出错,找不到对应的接口");
            }
            else {
                alert("抱歉，后台服务出错");
            }
          }
        }
       }
     }
    });
```

有些人可能会问，为啥不直接在一开始的transformResponse函数中写错误处理逻辑呢？

这是因为，接口正常时，AngularJS会依次调用transformResponse函数，再调用interceptors的responseError。

但是，某些异常情况，并不会调用transformResponse逻辑，例如，当URL不存在时，Web容器默认返回的404页面，或者当程序出错时，系统代码未处理这个错误，Web容器会返回默认的500页面，这时均会直接进入interceptors的responseError中。

因此，为了覆盖所有的异常情况，需要在transformResponse中抛出异常，然后由responseError统一处理。

2 响应内容格式化

由于前端关心的数据，是放在响应内容的data属性中。

而另外两个属性errno、errmsg，当返回正常数据时，前端是不关心的，为了取数据时更加方便，可以进一步优化transformResponse中的处理。当errno==0时，都返回responseData.data，这样，在业务逻辑里面，就可以直接使用data了，而不用取xxx.data。

```javascript
$httpProvider.defaults.transformResponse.push(function (responseData) {
        if (responseData && responseData.hasOwnProperty("errno") && responseData.hasOwnProperty("errmsg") && responseData.hasOwnProperty("data")) {
            if (responseData.errno == 0) {
                if (Angular.isArray(responseData.data) || Angular.isObject(responseData.data)) {
                    return responseData.data;
                } else {
                    return responseData
                }
            }
            else {
                throw responseData;
            }
        }
        else {
            return responseData;
        }
    });
```

3 对$resource做进一步封装

项目中每个模块，都创建了对应的service文件，用于与后台进行通信。例如“硬盘”模块，对应DiskSvr，“防火墙“模块，对应FirewallSvr。这样划分后，前端所有的后台请求逻辑，找起来会很方便。

在封装后台通信逻辑时，我们用到$resource，这是AngularJS自身的一个组件，当你的后台接口符合RESTFul规范时，你可以很方便地使用resource和后台进行通信。

而由于我们的后台并不是完整的RESTFul实现，我们需要做一些简单的封装。示意代码如下：

```javascript
app.factory("DiskSvr", function () {

    var url = "schedule/disk";

    var customAPI = {
        clone: {
            method: "post"
        }
    }

    return getApi(url, customAPI);
});

//公用的，每个Svr都可以用
getApi = function (path, customAPI) {

    Angular.forEach(customAPI, function (value, key) {
        if (!value.url) {
            value.url = util.connectPath(path, key);
        } else {
            value.url = util.connectPath(path, value.url);
        }
    });

    //所有的
    var defaultAPI = {
        detail: {
            url: baseUrl + "/get"
        },
        delete: {
            url: baseUrl + "/delete",
            method: "delete"
        },
        create: {
            url: baseUrl + "/create",
            method: "post"
        },
        update: {
            url: baseUrl + "/update",
            method: "put"
        }
    }

    return $resource(path, {}, Angular.extend(defaultAPI, customAPI));
}
```

上面的代码，主要做了这些事情：

为所有的svr注入了每个模块接口都必备的，最基础的增删改查四个接口。这样就无需在每个svr中加入这些接口。例如，在业务逻辑中调用DiskSvr.create()，就会用post请求调用schedule/disk/create接口。

简化了新增接口的配置。新增一个接口，只要配置对应的HTTP方法及名字即可。

通过引用$resource组件、进一步封装及svr文件，在业务Controller中，不会看到任何的和后台通信的基础代码，如果在这个Controller中你需要和后台通信，你只需注入响应的svr，然后调用对应方法即可。


4 注入请求header头

在我们的项目中，约定了所有的请求都要在header中带上一些相同的信息，这对AngularJS来说，是非常简单的事情： 执行以下代码后，之后所有的HTTP请求都会带上名为project的header信息：

Controller间通信问题

Controller调用

假设controllerA希望调用controllerB的某个函数，告诉同伴Controller，我的某个你所关心的东西改变了，要怎么做呢？举具体业务场景，有两个Controller，一个是主页Controller，另外主页上有个弹框表单，这个弹框表单也有个Controller，用户成功提交了这个表单后，弹框Controller需要告知

主页Controller，“哥们，请更新主页上的某项数据”。建议的做法，是用AngularJS的消息机制。例如，上面的例子中，弹框Controller是主页Controller的子Controller。

那么弹框Controller可以往上冒泡传递消息：

其父Controller去捕获这个消息：

这是一种很好的解耦办法，假设这两个Controller是由两个开发负责的，那么我开发我的Controller，你开发你的，我不用去关心你那边的逻辑。


2 数据共享

多个Controller之间要共享数据，要怎么做呢？ 

最简单但也不推荐的一个做法，就是把数据塞到rootscope中，但是，这就像JavaScript的全局变量，野蛮不好控制。 

这里推荐下我们的做法：写一个专门用于存储、设置共享数据的共享数据Facoty。在里面定义set方法，所有的共享变量，都需要经过set方法来设置。然后取数据则通过DATA变量获取。 

伪代码如下：

```javascript
app.factory(‘ShareSvr’, function(){
var shareData = {
peopleNum
}
return {
DATA:shareData,
setPepleNum:function(num){
shareData.peopleNum = num;
}
}
});
app.controller(‘TestController’,function(ShareSvr){
var self = this;
this.DATA = ShareSvr.DATA;
}
```

这里并没有要求DATA值只能通过g alert("Hello CSDN"); et方法获取，是为了之后在Controller对应的视图HTML中取值方便些。


第三方 库/资源 推荐

UI Router

路由（route），几乎所有的MVC框架都应该具有的特性，它是前端构建单页面应用（SPA）必不可少的组成部分。相比原生的ngRouter，UI Router功能更加强大，具备多视图，嵌套路由等特性，可以解决路由大部分的应用场景。


ngDialog

一个弹框控件，功能强大，我比较喜欢的地方，是它没有写死弹框的HTML、可以很方便地定义自己想要的弹框模板。例如，我们项目就通过它做了两种弹框，一种是普通弹框，一种是侧拉框（从屏幕右侧滑出，占满浏览器高度，宽度占满一半屏幕（或者其他自定义宽度）。


AngularJS代码规范

`3-1. https://github.com/johnpapa/angular-styleguide/blob/master/a1/i18n/zh-CN.md`

`3-2. https://github.com/mgechev/Angularjs-style-guide/blob/master/README-zh-cn.md`


Angular-filter

提供了很多实用的filter，string类、math类，集合类等。


w5c-validator

基于Angular.js原有的表单验证，统一验证规则和提示信息，在原有的基础上扩展了一些错误提示的功能，让大家不用在每个表单上写一些提示信息的模板，专心的去实现业务逻辑，国人出品。


ng-table

轻量，功能强大的表格组件。可以很方便地修改表格的样式、交互效果。