---
layout: post
title:  Android MVP模式
date:   2017-04-26 01:08:00 +0800
categories: Android
tag: 开发模式
---

* content
{:toc}

对于MVP（Model View Presenter）模式，做Android的小伙伴应该都不陌生，可以说是基于MVC的一个变种。利用接口将Model和View完全解耦，将业务逻辑完全剥离出来。使用MVP模式做过一个项目，就业务逻辑的抽离以及可维护性来说是一次非常好的体验。

# 传统MVC模式
MVC可以说是一种理念但绝不是一个可以直接应用的架构，对Android代码来说，MVC的对应关系如下所示：

* Model: 业务逻辑和实体模型
* View: 对应于Layout布局文件
* Controller: 对应于Activity 

![](http://upload-images.jianshu.io/upload_images/1233754-5f7d98f12dc2496d.png!web?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


看起来的确像那么回事，但是细细的想想这个View对应于布局文件，其实能做的事情特别少，实际上关于该布局文件中的数据绑定的操作，事件处理的代码都在Activity中，造成了Activity既像View又像Controller。最终结果就是Activity好像承担了MVC中的所有角色，导致界面交互、业务逻辑、网络请求全部杂糅在一起，代码极难维护。

当应用逐渐变大以后业务逻辑以及界面交互混杂在一起以后，就会出现Activity越来越庞大，处理的逻辑越来越多，最后结果就是：

![](http://upload-images.jianshu.io/upload_images/1233754-41f3e3d839c950fc.png!web?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 使用MVP以后
Presenter的出现，将Actvity视为View层，Model层视为业务逻辑层，Presenter负责完成View层与Model层的交互。

* Model: 依然是业务逻辑和实体模型
* View: 对应于Activity，负责View的绘制以及与用户交互
* Presenter: 负责完成View于Model间的交互

![](http://upload-images.jianshu.io/upload_images/1233754-eb5b4bc4fbf757be.png!web?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 遵循原则


