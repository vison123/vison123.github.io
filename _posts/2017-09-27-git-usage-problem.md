---
layout: post
title:  Git使用与Chrome调试分享
date:   2016-09-27 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - Git
---

### Git的使用问题

#### Git工作原理

如下图所示， git的工作总共分四层，其中三层是在自己本地也就是前面说的git仓库，包括了工作目录，暂存区和本地仓库，工作目录就是我们执行命令git init时所在的地方，也就是我们执行一切文件操作的地方，暂存区和本地仓库都是在.git目录，因为它们只是用来存数据的。远程仓库在中心服务器，也就是我们做好工作之后推送到远程仓库，或者从远程仓库更新下来最新代码到我们的git仓库。
![](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1506494614783&di=64d663b648da92cb34a9669fcdad8755&imgtype=0&src=http%3A%2F%2Fimages2015.cnblogs.com%2Fblog%2F492092%2F201704%2F492092-20170401154338789-1814759422.png)

#### Fork + MergeRequest使用


#### Git常用命令


#### Webstorm Git使用


### Chrome调试

#### 调试界面各Tab页功能含义

1.Elements：查看网页布局，前段页面调试时可以在Elements页面进行动态修改添加元素，便于界面调试；

2.Console：打印日志，提供日志搜索，执行，根据Level过滤；

3.Sources：调试代码专用，提供断点调试，watch，调用栈等功能；

4.Performance标签页可以显示JS执行时间、页面元素渲染时间，不做过多介绍；

5.Network 网络请求标签页：可以看到所有的资源请求，包括网络请求，图片资源，html,css，js文件等请求，可以根据需求筛选请求项，一般多用于网络请求的查看和分析，分析后端接口是否正确传输，获取的数据是否准确，请求头，请求参数的查看；

6.Memory标签页可以查看CPU执行时间与内存占用，不做过多介绍；

7.Resources标签页会列出所有的资源，以及HTML5的Database和LocalStore等，你可以对存储的内容编辑和删除 不做过多介绍；

8.Security标签页 可以告诉你这个网站的安全性，查看有效的证书等；

9.Audits标签页 可以帮你分析页面性能，有助于优化前端页面，分析后得到的报告；


#### Js代码调试技巧
