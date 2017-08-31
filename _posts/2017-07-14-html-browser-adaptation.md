---
layout: post
title:  浏览器兼容
date:   2017-07-14 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - Html
---

#### h5标签向下兼容

```
<!--[if lt IE9]> 
<script src="http://cdn.static.runoob.com/libs/html5shiv/3.7/html5shiv.min.js"></script>
<![endif]-->
将上代码复制到head部分，记住一定要是head部分（因为IE必须在元素解析前知道这个元素，所以这个js文件不能在其他位置调用，否则失效）
```

载入后，初始化新标签的CSS：主要是让这些html5标签成块状，像div那样。
{% highlight bash %} 
article,aside,dialog,footer,header,section,
footer,nav,figure,menu{display:block}
{% endhighlight %}

#### CSS Reset
CSS重设就是由于各种浏览器解释CSS样式的初始值有所不同，导致设计师在没有定义某个CSS属性时，不同的浏览器会按照自己的默认值来为没有定义的样式赋值，所以我们要先定义好一些CSS样式，来让所有浏览器都按照同样的规则解释CSS，这样就能避免发生这种问题。
* 最简化的CSS Reset
```
* {padding: 0;margin: 0;}
```
* meyerweb.com CSS Reset
```
html, body, div, span, applet, object, iframe,
h1, h2, h3, h4, h5, h6, p, blockquote, pre,
a, abbr, acronym, address, big, cite, code,
del, dfn, em, img, ins, kbd, q, s, samp,
small, strike, strong, sub, sup, tt, var,
b, u, i, center,
dl, dt, dd, ol, ul, li,
fieldset, form, label, legend,
table, caption, tbody, tfoot, thead, tr, th, td,
article, aside, canvas, details, embed, 
figure, figcaption, footer, header, hgroup, 
menu, nav, output, ruby, section, summary,
time, mark, audio, video {
	margin: 0;
	padding: 0;
	border: 0;
	font-size: 100%;
	font: inherit;
	vertical-align: baseline;
}
/* HTML5 display-role reset for older browsers */
article, aside, details, figcaption, figure, 
footer, header, hgroup, menu, nav, section {
	display: block;
}
body {
	line-height: 1;
}
ol, ul {
	list-style: none;
}
blockquote, q {
	quotes: none;
}
blockquote:before, blockquote:after,
q:before, q:after {
	content: '';
	content: none;
}
table {
	border-collapse: collapse;
	border-spacing: 0;
}
```
* YUI CSS Reset
To use CSS Reset, include the following source file in your web page header:
```
<link rel="stylesheet" type="text/css" href="http://yui.yahooapis.com/3.18.1/build/cssreset/cssreset-min.css">
```


