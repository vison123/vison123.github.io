---
layout: post
title:  高性能JavaScript
date:   2017-08-30 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - JavaScript
---

> 尽管JavaScript编译器以及引擎已经足够强大，性能依然需要关注;

#### 脚本位置
JavaScript文件在Html中的加载和执行都会阻塞浏览器渲染页面，因为脚本的执行过程可能会改变页面内容，所以<script>标签的位置就尤为重要了。
现在许多新版本的浏览器都允许并行下载JavaScript文件，也就是说<script>标签之间的资源下载不会互相影响。但是JavaScript文件下载过程仍然
会阻塞其他资源的下载，比如图片。而且Js文件还需要执行过程，这个过程仍然会阻塞界面渲染。因此尽可能将<script>标签放到<body>标签的底部。
    
```
<!DOCTYPE html>
<html>
<head> 
<meta charset="utf-8"> 
	<title>Title</title>
	<link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
<body>
	<script type="text/javascript" src="file1.js"></script>
	<script type="text/javascript" src="file2.js"></script>
	<script type="text/javascript" src="file3.js"></script>
</body>
</html>
```
