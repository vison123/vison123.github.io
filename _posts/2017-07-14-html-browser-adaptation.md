#html浏览器适配

<!--[if lt IE9]> 
<script src="http://cdn.static.runoob.com/libs/html5shiv/3.7/html5shiv.min.js"></script>
<![endif]-->
将上代码复制到head部分，记住一定要是head部分（因为IE必须在元素解析前知道这个元素，所以这个js文件不能在其他位置调用，否则失效）

载入后，初始化新标签的CSS：主要是让这些html5标签成块状，像div那样。
article,aside,dialog,footer,header,section,footer,nav,figure,menu{display:block}
