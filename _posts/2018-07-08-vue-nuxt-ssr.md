---
layout: post
title:  Vue + Nuxt 实现SSR及其部署方案
date:   2018-07-06 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - Vue
    - Nuxt
---

最近在开发Vue实现服务端渲染的时候，采用了Nuxt.js实现SSR（服务端渲染）。
用过react.js的童鞋可能都或多或少接触过Next.js，Nuxt.js 也是类似是一个
Next.js的基于 Vue.js 的通用应用框架。

如果应用需要有SEO的要求的话，Nuxt.js是一个很好的解决方案。但是如果不需要的话，建议
还是客户端渲染，服务端渲染目前还是有一些问题需要解决。这在后面会提到，本篇主要是介绍
一下其部署方案。[项目在线地址](https://github.com/vison123/vue-nuxt-template.git)

### 部署方式

Nuxt.js 提供了两种发布部署应用的方式：服务端渲染应用部署 和 静态应用部署。

#### 静态文件部署

可利用下面的命令生成应用的静态目录和文件：

```javascript
$ npm run generate
```

这个命令会创建一个 dist 文件夹，所有静态化后的资源文件均在其中。将打包后的静态文件
放到Nginx静态服务器即部署成功。

#### 服务端渲染应用部署

部署 Nuxt.js 服务端渲染的应用不能直接使用 nuxt 命令，而应该先进行编译构建，然后再启动 Nuxt 服务，可通过以下两个命令来完成：

```javascript
$ nuxt build
$ nuxt start
```

package.json配置如下：

```javascript
{
  "name": "vue-nuxt-template",
  "version": "1.0.0",
  "description": "Nuxt.js project",
  "author": "韦森 <723955399@qq.com>",
  "private": true,
  "scripts": {
    "dev": "nuxt",
    "build": "nuxt build",
    "start": "PORT=3002 nuxt start",
    "generate": "nuxt generate",
    "lint": "eslint --ext .js,.vue --ignore-path .gitignore .",
    "precommit": "npm run lint"
  },
  "dependencies": {
    "axios": "^0.18.0",
    "nuxt": "^1.0.0",
    "vue-notifications": "^0.9.0"
  },
  "devDependencies": {
    "babel-eslint": "^8.2.1",
    "eslint": "^4.15.0",
    "eslint-friendly-formatter": "^3.0.0",
    "eslint-loader": "^1.7.1",
    "eslint-plugin-vue": "^4.0.0"
  }
}
```

部署配置步骤如下：

* 第一步 Nginx 反向代理配置

端口配置3002即为启动脚本start中的指定端口

```javascript
server {
    listen 80;
    server_name c.visonsoft.cn;
    location / {
        ...
        proxy_pass http://127.0.0.1:3002; #反向代理
    }
}
```

* 第二步，打包，生成.nuxt/ 文件夹

```javascript
 $ npm run build
```

* 第三步，选择要部署的文件

可以选择将文件从本机复制到服务器运行，如果感觉太麻烦，直接克隆全部代码放到服务器运行一样的。
如果选择拷贝打包后文件到服务器运行，只需要下面3个目录：

  * .nuxt/ 文件夹
  * package.json 文件
  * nuxt.config.js 文件(如果你配置proxy等，则需要上传这个文件，建议把它传上去)

* 第四步，启动你的nuxt

```javascript
 $ npm install
 $ npm start
```

### 遗留问题

#### 内存问题

在阿里云低配机上出现内存膨胀的问题，当然也由于 Node.js 的特殊单线程异步机制，暂不解决。

#### 并发问题

瞬间高并发访问，会导致内存膨胀爆表宕机，经分析，是由于组件缓存引起的，将组件缓存减少至10，问题有所改观，但不明显；

更深原因是，每次用户访问，程序均会重新渲染组件输出，组件数据即在一段时间内驻存在内存中，直到 V8GC 回收。
最终的解决方案是：

使用官方推荐的"使用编码中的 Nuxt.js "方法，自定义Node.js程序的入口，对程序进行一些优化；
如果你对业务和程序都需要有深度掌控的话，我很推荐此方法，它可以使你以管理 Node.js 程序的方式管理应用。

具体的优化方法是使用了一个叫idle-gc的垃圾回收模块来优化内存管理，
idle-gc是在node早期版本中被废除的功能，主要负责空闲时的堆内存回收，然后早期被认为有 BUG，经常会导致 CPU 满载，于是从 Node.js 中移除了，此项目作者修复了这个 BUG，并发布了模块。
另外，如果机器配置足够，建议开启缓存，即cache选项，且适当往大的配置，cache 的意义在于使用内存常驻来减轻 CPU 的计算压力，这对于单线程的 Node.js 是很好的业务实践。

### 参考资料

* [Vue.js官网](https://cn.vuejs.org/)
* [Vue SSR 指南](https://ssr.vuejs.org/zh/)
* [nuxt.js官网](https://zh.nuxtjs.org/)
* [next.js、nuxt.js等服务端渲染框架构建的项目部署到服务器，并用PM2守护程序](https://segmentfault.com/a/1190000012774650)
* [实现基于Nuxt.js的SSR应用](https://juejin.im/post/58ff960ba22b9d0065b722cd)
