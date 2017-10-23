---
layout: post
title:  JavaScript优化
date:   2017-08-30 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - JavaScript
---

> 尽管JavaScript编译器以及引擎已经足够强大，性能依然需要关注;

#### 优化脚本位置
JavaScript文件在Html中的加载和执行都会阻塞浏览器渲染页面，因为脚本的执行过程可能会改变页面内容，所以script标签的位置就尤为重要了。
现在许多新版本的浏览器都允许并行下载JavaScript文件，也就是说script标签之间的资源下载不会互相影响。但是JavaScript文件下载过程仍然
会阻塞其他资源的下载，比如图片。而且JavaScript文件还需要执行过程，这个过程仍然会阻塞界面渲染。因此尽可能将script标签放到body标签的底部：
    
```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
	<title>Title</title>
	<link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
<body>
	<--推荐脚本存放位置-->
	<script type="text/javascript" src="file1.js"></script>
	<script type="text/javascript" src="file2.js"></script>
	<script type="text/javascript" src="file3.js"></script>
</body>
</html>
```

* 不要把内嵌脚本紧跟在link标签之后，因为会导致页面阻塞去等待样式表的下载；
* 限制HTTP请求数，减少script标签的数量，因为额外的HTTP请求会带来性能开销，可以利用打包工具合并JavaScript文件；
* 减少JavaScript文件的大小；

#### defer和async
首先，看一下三种情况下浏览器对脚本解析的方式：
```
<script src="script.js"></script>
<script async src="asyncscript.js"></script>
<script defer src="defermyscript.js"></script>
```

* 没有 defer 或 async，浏览器会立即加载并执行指定的脚本，“立即”指的是在渲染该 script 标签之下的文档元素之前，也就是说不等待后续载入的文档元素，读到就加载并执行。
* 有 async，加载和渲染后续文档元素的过程将和 script.js 的加载与执行并行进行（异步）。
* 有 defer，加载后续文档元素的过程将和 script.js 的加载并行进行（异步），但是 script.js 的执行要在所有元素解析完成之后，DOMContentLoaded 事件触发之前完成。

![](https://sfault-image.b0.upaiyun.com/28/4a/284aec5bb7f16b3ef4e7482110c5ddbb_articlex
)

此图告诉我们以下几个要点：

* defer 和 async 在网络读取（下载）这块儿是一样的，都是异步的（相较于 HTML 解析）；
* 它俩的差别在于脚本下载完之后何时执行，显然 defer 是最接近我们对于应用脚本加载和执行的要求的；
* 关于 defer，此图未尽之处在于它是按照加载顺序执行脚本的，这一点要善加利用；
* async 则是一个乱序执行的主，反正对它来说脚本的加载和执行是紧紧挨着的，所以不管你声明的顺序如何，只要它加载完了就会立刻执行；
* 仔细想想，async 对于应用脚本的用处不大，因为它完全不考虑依赖（哪怕是最低级的顺序执行），不过它对于那些可以不依赖任何脚本或不被任何脚本依赖的脚本来说却是非常合适的；
* 操作 DOM 的脚本尽量不要使用 async / defer，可以把需要操作 DOM 的部分放在 DOMContentLoaded 事件回调中执行，很多前段框架的启动机制都是这样做的；

#### 前端渲染加速方法

* HTTP1.0时代，合并静态文件(JS、CSS)，常见的手段比如Nginx的第三方模块nginx-http-concat，减少请求加速访问。静态请求文件(JS、CSS)，
在没有CDN的情况下建议使用单独的域名请求访问。因为在同一时间针对同一域名下的请求有一定数量限制，超过限制数目的请求会被阻塞。多个域名会加
大请求的并发量；

* 异步加载JS，使用HTML 5 属性async，当页面继续进行解析时，脚本将被执行，async 属性仅适用于外部脚本（只有在使用src 属性时）；

* 使用CSS控制背景图的方式，比如我们把常见的图标放置在一张图片上，CSS控制显示，减少请求数量；

* 压缩静态文件(JS、CSS)，减少网络传输，本地压缩方式如UglifyJS 、YUI compressor以及JsMin，服务端压缩手段如Nginx的GIZP；

* 图片延时加载，对于首页满屏的图片TT猫应该是采用滚动加载，使用延迟加载在可提高网页下载速度。在某些情况下，它也能帮助减轻服务器负载；

* 尽量少的使用cookie存储大量数据，建议使用本地存储代替，减少不必要的数据请求；

* JS引入方式，建议非预加载的JS在网页底部引入，浏览器渲染网页是从上往下，页面是用户能看到的也能直观感受到的，而js代码是用户看不到的，
属于“幕后”的东西， 把“幕后”的东西放到最下面。

* CDN加速，其基本思路是尽可能避开互联网上有可能影响数据传输速度和稳定性的瓶颈和环节，使内容传输的更快、更稳定。更有打不死CDN ，
无攻击时使用加速节点，有攻击时自动切换高防节点，加速与防护同时享有。

* 开启HTTPS安全访问，按理说应该会影响访问速度，但是HTTP2.0时代这个事情不存在了，新特性多路复用、压缩头信息以及二进制格式传输
数据带给飞一样的感觉。

* 最后说一下带宽，再牛逼的优化没有基础保障也是白扯，带宽的作用也就不言而喻了。就好比六车道和单车道的公路，单位时间内的车流量就能很好的
衬托出老司机此时此刻的心情。
