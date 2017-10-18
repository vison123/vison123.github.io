---
layout: post
title:  Jekyll + GitPages 搭建博客
date:   2017-09-20 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - Jekyll
---

之前一直没有好好整理写的一些笔记，只是存放在本地markdowm，查找和分享都不是很方便。前段时间工作不是很忙所以有空在本地搭建了Jekyll的环境，
然后推到github.io上去。这不得不夸一下github了，对jekyll的支持以及自动构建完全不用我们做多余的操作。最方便的一点就是写文章更加方便了，
只需要一个浏览器，把零碎时间利用起来。建议大家不妨试一下github.io + Jekyll来搭建自己的个人博客。

#### Ruby环境

  Mac自带Ruby环境，其他操作系统请自行百度。
  Jekyll Requires Ruby version >= 2.1， 如不满足需要先安装更新，[Ruby更新教程](http://www.jianshu.com/p/d99b5662d8a0)
  
#### Gem安装更新

  Gem是Ruby第三方插件管理器，是后面安装Jekyll时用到的，Ruby环境自带，查看到版本号后可以更新到最新版本。注意更新需要翻墙，否则会出现404错误。

  更新命令如果不加sudo会出现报错，You don't have write permissions for the /Library/Ruby/Gems/2.0.0 directory.
  
  如果翻墙还是更新不了的话，那只能换成国内的源了，换源后重新执行更新命令即可：

  ```
    检查gem版本
    $ gem -v
    更新Gem(提示权限)
    $ sudo gem update --system

    $ gem sources --add https://gems.ruby-china.org/ --remove https://rubygems.org/
    $ gem sources -l
    *** CURRENT SOURCES ***

    https://gems.ruby-china.org
    请确保只有 gems.ruby-china.org
  ```
#### 安装jekyll

这时候上面安装的Gem就派上用场了，到这一步基本不会有报错，会很快下载完

  ```
    安装jekyll(提示权限)
    $ gem install jekyll
    安装成功之后，查看版本号
    $ jekyll -v
  ```

#### 使用jekyll

 jekyll安装成功以后就可以使用jekyll了，可以自己根据Jekyll规则自己搭建个人网站或博客。当然网上有很多模版，如果没太多时间自己设计的话，
 可以直接使用网上的模版[Jekyll Themes](http://jekyllthemes.org/)。

 我使用的模版是[Huxpro 提供的模版](https://github.com/Huxpro/huxpro.github.io),感谢[Huxpro](https://github.com/Huxpro);

 下载模版并修改风格后，可以在本地运行 jekyll serve即可

 #### GithubPages

 GithubPages: Websites for you and your projects.
 Hosted directly from your GitHub repository. Just edit, push, and your changes are live.

 GitHub 提供了一种功能，叫 GitHub Pages, 利用这个功能，我 们可以为项目建立网站，当然，这也意味着我们可以通过 GitHub Pages 建立自己的网站。
 GitHub Pages 直接支持Jekyll,使用 GitHub 和 Jekyll，我们可以打造自己的独立博客，可以自由地定制网站的风格，并且这一切都是免费的。

 在自己的github中新建项目，例如我的用户名是vison123，那么则新建项目vison123.github.io项目，将本地搭建的jekyll项目push到该仓库，
 会触发github自动构建，构建失败邮箱会收到提醒。若成功，则打开http://vison123.github.io 即可访问该jekyll项目。

#### 绑定域名

  若是想将域名绑定在你github.io项目根目录下新建一个文件，文件名为CNAME，将你要绑定的域名写在文件中。比如我要绑定visonsoft.cn域名，
  那就在文件中就写一行“visonsoft.cn”。

  然后在域名解析中进行配置，如下图所示：
 ![](/img/in-post/post-jekyll-build/cname-resolve.png)


  配置生效后即可通过域名访问自己的GithubPages
  ![](/img/in-post/post-jekyll-build/visonsoft.cn.png)

#### 添加评论功能

   刚开始我的博客并没有评论功能，主要是没找到好的工具，多说停止维护，其他插件如畅言有需要网站备案，可选方案变少，很多评论国外的一些
   评论插件又不是很方便使用和维护。无意中看到Gitment可以提供评论功能，并且使用issue来进行评论点赞的管理，只需要使用github账号登陆即可。
   现在博客中已经成功加入了评论功能，就是用Gitment实现的，使用方法参考[在Jekyll博客添加评论系统](https://wenku.baidu.com/view/b25ff654f01dc281e53af089.html)


