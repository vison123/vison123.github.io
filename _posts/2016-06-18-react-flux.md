---
layout: post
title:  ReactNative Flux模式
date:   2017-04-26 01:08:00 +0800
header-img: "img/post-bg-2015.jpg"
author:  "Vison"
catalog: true
tags:
    - ReactNative
    - React
    - 开发模式
---


接触ReactNative也有一年多的时间了，从最开始的硬编码数据管理混乱，到Flux的单向数据流，以及后来的Redux我们都有尝试过。硬编码自不用说，不同模块的同事完全按照自己的想法进行编码，界面以及效果出来就行，后期BUG修改以及交接工作就遇上了大问题。代码可读性差，没有统一的逻辑，针对这些问题我们开始引入Flux，使应用更易于开发和维护。

# Flux框架的结构
主要有以下四大组成部分：
{% highlight bash %}
 View(视图层)： 界面显示，业务逻辑;
 
 Action(动作)：视图层发出的消息(比如mouseClick);
 
 Dispatcher(转发器)：用来接收Actions、执行回调函数;
 
 Store(数据层)：用来存放应用的状态，一旦发生变动，就提醒Views要更新页面;
{% endhighlight %}


![](http://img.blog.csdn.net/20160823082435287?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

Flux存在多种实现，针对不同的业务场景可以进行适当的调整，适合自己的才是最好的。本文采用的是FaceBook官方的Flux框架。

## 单向数据流
Flux最大的特点就是单项数据流，不同View之间的数据不会相互干扰。下图更能体现单向数据流的特点，不同的Action不会相互干扰，代码变的更有可预测性。
因为所有的数据流向是相同的，这也就代表着业务逻辑是相似的，代码更加容易维护。

![](http://www.ruanyifeng.com/blogimg/asset/2016/bg2016011503.png)

整个完整流程主要有以下几个步骤：

{% highlight bash %}
 View触发一个方法产生一个Action

 Dispatcher 收到 Action，要求 Store 进行相应的更新

 Store 更新后，发出一个"change"事件

 View 收到"change"事件后，更新页面
{% endhighlight %}

## 优缺点
没有哪一种模式是完美的，只是看牺牲了哪些性能又带来了哪些便利。Flux也不例外，也有一些优缺点：
{% highlight bash %}
 单向数据流使代码编写更加模块化；

 移植现有代码比较困难，之前做过迁移，相当于重写业务逻辑；

 单元测试难以进行，在Flux中，组件开始依赖ActionCreators以及Store，以及其他的依赖项目，这将使得编写单元测试异常复杂
{% endhighlight %}




