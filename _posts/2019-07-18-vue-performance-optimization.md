---
layout: post
title:  Vue + Element中后台单页应用性能优化
date:   2019-07-18 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - Vue
---


### 项目中存在问题
目前前端应用普遍采用客户端加载，所以js、css文件大小将直接影响到页面响应速度，下图是优化目标项目的analyze结构图以及体积过大的静态文件：

 ![](/img/in-post/post-vue-performance-optimization/analyze.png)
 
 ![](/img/in-post/post-vue-performance-optimization/staticjs.png)
 
从图中可以看出目前项目存在以下问题：
* 所有js、css未进行压缩
    
    js、css压缩后可大大减小文件体积，提升加载速度

* vendor.js、app.js等文件过大
    
    最大的js有将近900k，影响浏览器加载速度

* 打包后js文件过多

    按需引入过多，打包后js数量远远大于路由数量
    
* 静态文件中存在三方静态资源包

    文件多为三方组件库，超过1M，对服务器带宽消耗多大

针对项目中的这些问题，以下优化方案可以解决

#### 三方库不打包到vendor.js,利用CDN加载

vendor.js、app.js文件过大会造成两方面影响：

* 消耗服务器带宽
* 增加网络请求时间，降低页面响应速度

static中静态文件放置到CDN，三方库如vue.js、element.js采用官方CDN加载，不占用静态服务器资源和带宽；

采用七牛云、阿里云全站CDN加速，缓存网站资源，减少占用静态服务器资源和带宽；

#### gzip压缩js、css文件

```javascript
    new CompressionWebpackPlugin({
      filename: '[path].br[query]',
      test: /\.(js|css|html|svg)$/,
      compressionOptions: { level: 11 },
      threshold: 10240,
      minRatio: 0.8,
      deleteOriginalAssets: false,
    })
```

gzip后可以看出js文件体积大大降低，不过真正做到gzip加速还需要nginx开启gzip加速

 ![](/img/in-post/post-vue-performance-optimization/ungzip.png)
 
 ![](/img/in-post/post-vue-performance-optimization/gzip.png)
 
