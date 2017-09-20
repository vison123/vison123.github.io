---
layout: post
title:  Jekyll 环境搭建
date:   2017-09-20 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - Jekyll
---

之前一直没有好好整理写的一些笔记，只是存放在本地markdowm，查找和分享都不是很方便。前段时间工作不是很忙所以有空在本地搭建了Jekyll的环境，
然后推到github.io上去。这不得不夸一下github了，对jekyll的支持以及自动构建完全不用我们做多余的操作。最方便的一点就是写文章更加方便了，
我只需要一个浏览器。建议大家不妨试一下github.io+Jekyll来搭建自己的个人博客。

## Jekyll环境搭建

#### Ruby环境

  Mac自带Ruby环境，其他操作系统请自行百度。
  
#### Gem安装更新

  Gem是Ruby第三方插件管理器，是后面安装Jekyll时用到的，Ruby环境自带，查看到版本号后可以更新到最新版本。注意更新需要翻墙，否则会出现404错误。
  ```
  检查gem版本
  gem -v
  更新Gem(提示权限)
  sudo gem update --system
  ```
  更新命令如果不加sudo会出现报错，You don't have write permissions for the /Library/Ruby/Gems/2.0.0 directory.
  
#### 安装jekyll

这时候上面安装的Gem就派上用场了，
  ```
    安装jekyll(提示权限)
    $ gem install jekyll
    安装成功之后，查看版本号
    $ jekyll -v
  ```
