---
layout: post
title:  Android 自定义控件
date:   2016-03-24 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - Android
---

> Android 自定义控件不外乎三种，自绘控件、组合控件、以及继承控件

例如项目中需要一个圆形头像，很明显Android原生库中并没有这一组件直接解决问题，那么我们现在就有三种方案去解决这一问题
* 直接继承View，重写onDraw()方法;
* 继承ImageView，给ImageView设置形状;
* 在ImageView外面包裹布局，布局和ImageView配合使用来确定形状;
下面详细说明一下三种自定义控件的定制方法:

### 自绘控件

自绘控件的意思就是该控件是完全使用Paint（画笔）和Canvas（画布）在View中进行绘制出的控件，该控件应该直接继承View（Android中所有组件都继承于View），
实现 onDraw() 方法，主要绘制逻辑都是在这一方法中完成。


### 继承控件

### 组合控件



