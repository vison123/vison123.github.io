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
