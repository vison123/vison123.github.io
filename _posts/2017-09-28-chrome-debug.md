---
layout: post
title:  Chrome调试
date:   2017-09-27 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - Chrome
---

### 调试界面各Tab页功能

![](/img/in-post/post-chrome-debug/chrome-debug.png)

#### 箭头图标
用于在页面选择一个元素来审查和查看它的相关信息，当我们在Elements这个按钮页面下点击某个Dom元素时，箭头按钮会变成选择状态；

#### 设备图标
点击它可以切换到不同的终端进行开发模式，移动端和pc端的一个切换，可以选择不同的移动终端设备，同时可以选择不同的尺寸比例，
chrome浏览器的模拟移动设备和真实的设备相差不大，是非常好的选择；

#### Elements标签页
查看网页布局，前段页面调试时可以在Elements页面进行动态修改添加元素，便于界面调试；

#### Console标签页
打印日志，提供日志搜索，执行，根据Level过滤；

#### Sources标签页
调试代码专用，提供断点调试，watch，调用栈等功能；

#### Performance标签页
可以显示JS执行时间、页面元素渲染时间，不做过多介绍；

#### Network 网络请求标签页
可以看到所有的资源请求，包括网络请求，图片资源，html,css，js文件等请求，可以根据需求筛选请求项，
一般多用于网络请求的查看和分析，分析后端接口是否正确传输，获取的数据是否准确，请求头，请求参数的查看；

#### Memory标签页
可以查看CPU执行时间与内存占用，不做过多介绍；

#### Resources标签页
会列出所有的资源，以及HTML5的Database和LocalStore等，你可以对存储的内容编辑和删除 不做过多介绍；

#### Security标签页
可以告诉你这个网站的安全性，查看有效的证书等；

#### Audits标签页
可以帮你分析页面性能，有助于优化前端页面，分析后得到的报告；


### Js代码调试技巧

#### 源代码断点
通过左边的内容源，打开对应的 JavaScript 文件，鼠标点击文件的行号就可以设置和删除断点。添加的每个断点都会出现在右侧调试区的Breakpoints
列表中，点击列表中断点就会定位到内容区的断点上。如果你有多个文件、多个断点的话，利用Breakpoints 列表中的断点快速定位非常方便。
在Breakpoints 列表中每个断点前面都有一个复选框，取消选中就将禁用该断点。断点位置的右键菜单中也可以禁用断点。
也可以在右侧功能区上面按钮临时禁用所有已添加的断点，再点一下恢复原状态。在断点调试时，可以用鼠标选择想要查看的变量或表达式，
然后右键菜单执行“Evaluate in Console”，就可以看到该表达式的当前的值了。
![](/img/in-post/post-chrome-debug/debug-breakpoint.png)

#### 条件断点
在断点位置的右键菜单中选择“Edit Breakpoint...”可以设置触发断点的条件，就是写一个表达式，表达式为 true 时才触发断点。
![](/img/in-post/post-chrome-debug/if-breakpoint.png)

#### DOM 元素上设置断点
在Chrome浏览器中，打开开发者工具，先选中一个页面元素，然后点击鼠标右键，依次点击菜单中的”Break on …”——勾选“Attributes modifications”。
刷新页面，当该元素的属性发生变化时，就会暂停脚本的执行，并且定位到改变发生的地方。
![](/img/in-post/post-chrome-debug/dom-breakpoint.png)

#### Watch

查看变量的值，在代码中选中变量右击选择Add to watch，该变量即出现在Watch列表中。变量页可以手动添加，并且支持表达式操作。
使用Watch功能以后，打断点以后可以直接在右侧查看，节约调试时间。
![](/img/in-post/post-chrome-debug/debug-watch.png)

#### Call Stack

在断点停下来时，右侧调试区的 Call Stack 会显示当前断点所处的方法调用栈，比如有一个函数 g() 其中又调用了函数 f() ，
而我在 f() 中设置了一个断点，那么我在 console 中执行函数 g() 的时候会触发断点，最上面是 f()，然后是 g()。
调用栈中的每一层叫做一个 frame，点击每个 frame 可以跳到该 frame 的调用点上。此外，还可以在 frame 上用右键菜单重新开始执行当前 frame，
也就是从该 frame 的开始处执行。比如在函数 f() 的 frame 上 Restart Frame， 断点就会跳到 f() 的开头重新执行，context 中的变量值也会还原。
这样结合变量修改和编辑代码等功能，就可以在当前 frame 中反复进行调试，而不用刷新页面重新触发断点了。
![](/img/in-post/post-chrome-debug/call-stack.png)

#### Scope Variables

在这里可以查看此时局部变量和全局变量的值。

![](/img/in-post/post-chrome-debug/debug-scope.png)

