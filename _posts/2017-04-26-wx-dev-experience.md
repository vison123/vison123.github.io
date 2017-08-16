---
layout: post
title:  微信小程序开发总结
date:   2017-04-26 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - 微信小程序
---

# 小程序开发技术介绍


2017年1月9日，小程序如约而至，小程序的产品理念是“无处不在、用完即走“。上线以后获得了很多关注，京东，摩拜单车等App第一时间接入小程序。小程序的诞生对一些商家提供了新的营业渠道，同时对原生App产生了一定冲击。你可能会疑问为何小程序这么火，因为它有一个好爸爸－－有着8.06亿用户量的巨型App – 微信。

当然它也有一些优缺点：

* 优势：开发升本低、功能丰富、依托腾讯巨大的流量，这些优势非常重要，这些年app的开发成本和推广成本是呈倍增长，这导致了一大批创业者死在上面，小程序很大程度上解决了这一问题，由此得出创业者非常适合小程序；

* 缺陷：本质上是H5 依靠缓存等将会比web app的体验更好，但还在原生应用之下；张小龙的理念是做服务，用完即走，高频重要的应用显然不适合；

<span id="H1-h1">文件结构</span>
```
    + images
    + pages
      + 以*为名字的文件夹
        + *.js
        + *.json
        + *.wxml
        + *.wxss
    + util
      + *.js
    + app.js
    + app.json
    + app.wxss
```

小程序包含一个描述整体的app和多个描述各自的page，pages文件夹下面每一个文件夹即是一个页面的逻辑和页面实现；
一个小程序主体部分由三个文件组成，必须在项目根目录，如下：

| 文件 | 必填 | 作用 |
| :----- | :-----: | :----- |
| app.js | 是 | 小程序逻辑 |
| app.json | 是 | 小程序公共设置 |
| app.wxss | 否 | 小程序公共样式表 |

一个小程序页面由4个文件组成，分别是：
 
| 文件类型 | 必填 | 作用 |
| :----- | :-----: | :----- |
| *.js | 是 | 页面逻辑 |
| *.wxml | 是 | 页面结构 |
| *.wxss | 否 | 页面样式表 |
| *.json | 否 | 页面配置 |

**注意：为了方便开发者减少配置项，规定描述页面的四个文件具有相同的路径与文件名** 

<span id="H1-h2">WXML和WXSS简单介绍</span>
* [WXML](https://mp.weixin.qq.com/debug/wxadoc/dev/framework/view/wxml/)（WeiXin Markup Language）是框架设计的一套标签语言，结合基础组件、事件系统、可以构建出页面结构;
    * 数据绑定
    
    ```html
        <!--wxml-->
        <view> {{message}} </view>
    ```
    
    ```javascript
        //page.js
        page({
            data: {
                message: 'Hello world'
            }
        })
    ```
    
    * 事件绑定
    
    ```html
        <!--wxml-->
        <view bindtap="add"> {{count}} </view>
    ```
    
    ```javascript
        //page.js
        Page({
          data: {
            count: 1
          },
          add: function(e) {
            this.setData({
              count: this.data.count + 1
            })
          }
        })
    ```
    
* [WXSS](https://mp.weixin.qq.com/debug/wxadoc/dev/framework/view/wxss.html) (WeiXin Style Sheets)是一套样式语言，用于描述WXML的组件样式。就类似web应用的css层叠样式表
    * 尺寸单位
        微信小程序除了可以使用css的单位px和相对位置百分比的方式，还使用了一种新的尺寸单位rpx，rpx（responsive pixel）： 可以根据宽度进行自适应，规定屏幕宽为750rpx; 如在iPhone6上，屏幕宽度为375px，共有750个物理像素，则750rpx=375px=750px，1rpx=0.5px=1物理像素。
        **详细可参考blog：**[微信小程序新单位rpx与自适应布局](http://www.cnblogs.com/babyzone2004/p/5979198.html)
        
    * 样式导入
    
    ```css
        /** common.wxss **/
       .small-p {
         padding:5px;
       }
    ```
    
    ```css
        /** app.wxss **/
        @import "common.wxss";
        .middle-p {
          padding:15px;
        }
    ```
    
    * 内联样式
        * style: 静态的样式统一写到 class 中。style 接收动态的样式，在运行时会进行解析，请尽量避免将静态的样式写进 style 中，以免影响渲染速度。
        
        ```html
            <view style="color:{{color}};" />
        ```
        
        * class: 用于指定样式规则，其属性值是样式规则中类选择器名(样式类名)的集合，样式类名不需要带上.，样式类名之间用空格分隔。
        
        ```html
            <view style="color:{{color}};" />
        ```
        
<span id="H1-h3">app.json和page页面下的*.json简单配置</span>
* app.json
    小程序使用`app.json`文件对微信小程序进行全局配置，决定页面文件对路径、窗口表现、设置网络超时时间、设置多tab等
    
    | 属性 | 类型 | 必填 | 描述 |
    | :----- | :-----: | :-----: | :----- |
    | pages | String Array | 是 | 设置页面路径 |
    | window | Object | 否 | 设置默认页面的窗口表现 |
    | tabBar | Object | 否 | 设置底部tab的表现 |
    | networkTimeout | Object | 否 | 设置网络超时间 |
    | debug | Object | 否 | 设置是否开启debug模式 |

* pages.json
    每一个小程序页面也可以使用.json文件来对本页面的窗口表现进行配置。 页面的配置比app.json全局配置简单得多，只是设置 app.json 中的 window 配置项的内容，页面中配置项会覆盖 app.json 的 window 中相同的配置项。

    页面的.json只能设置 window 相关的配置项，以决定本页面的窗口表现，所以无需写 window 这个键。
    
    | 属性 | 类型 | 默认值 | 描述 |
    | :----- | :-----: | :-----: | :----- |
    | navigationBarBackgroundColor | HexColor | #000000 | 导航栏背景颜色，如"#000000" |
    | navigationBarTextStyle | String | white | 导航栏标题颜色，仅支持 black/white |
    | navigationBarTitleText | String | 1 | 导航栏标题文字内容 |
    | backgroundColor | HexColor | #ffffff | 窗口的背景色 |
    | backgroundTextStyle | String | dark | 下拉背景字体、loading 图的样式，仅支持 dark/light |
    | enablePullDownRefresh | Boolean | false | 是否开启下拉刷新 |
    | disableScroll | Boolean | false | 设置为 true 则页面整体不能上下滚动；只在 page.json 中有效，无法在 app.json 中设置该项 |

**备注：** 详细的配置请参考官方[API](https://mp.weixin.qq.com/debug/wxadoc/dev/framework/config.html);
    

# 小程序开发技术局限性和注意点

<span id="H2-h1">微信小程序原生架构局限</span>
微信小程序原生架构局限模块化，数据管理，复用性等皆较差，在此基础上直接开发，效率和代码规范性，模块化等问题突出，因而可以考虑集成一些开发的基础骨架和框架，类似webapp般的开发。现阶段Github上面已经有一部分优秀的骨架框架和集成redux管理开发数据等不错的框架，可以让开发微信小程序流程接近web级开发流程。
**框架推荐：**
[weapp-boilerplate](https://github.com/cuiyueshuai/weapp-boilerplate);
[wxapp-redux-starter](https://github.com/cuiyueshuai/wxapp-redux-starter);

<span id="H2-h2">微信小程序的发布代码包限制</span>
微信官方限制小程序的发布代码不能超过 1MB，因而在此处有两点要注意：
一、集成开发框架时，编译输出要上传的代码到dist文件夹，且不可以超出1MB(一周前该限制扩大为2M)；
二、实际际开发过程中，一般的小程序难免会有图片等富媒体文件，而这些富媒体文件会占用大量的存储空间；
关于第二点，一般项目不会遇到，这里提供了一种结合七牛服务提出了解决方案，可解决类似问题作为参考：[突破微信小程序存储限制的技巧与方法详解](http://www.jcodecraeer.com/a/qianduankaifa/2017/0123/7088.html)

<span id="H2-h3">微信小程序页面层级限制</span>
微信小程序官方限制页面使用类似router栈级结构层级不可以超过5层，因而页面间逻辑不可以过于复杂，在此基础上，开发者可以从页面逻辑设计、wx.redirectTo等API问题上尽量优化以求得规避该限制带来的最小影响。

<span id="H2-h4">微信小程序关于网络请求的限制</span>
微信小程序开发过程中未有appid时，网络请求未经过检验，基本不存在限制，只是简单的请求和响应，但是绑定appid和发布时，切记网络请求域名要在微信公众平台绑定，且域名使用https协议和后台需要 Nginx 配置 HTTPS 的加密标准为 TLS 1.2 及以上。(崔登攀老师处学习的知识点)

<span id="H3">小程序开发过程中填坑指南</span>
* 列表渲染问题
    微信小程序渲染列表方式：
    
        ```html
            <view wx:for="{{array}}" wx:for-index="idx" wx:for-item="itemName">
                {{idx}}: {{itemName.message}}
            </view>
        ```
    在列表界面和数据实现双向绑定时，切记绑定wx：key属性，否则会出现数据和界面无法对应问题，关于wx：key 属性和多层嵌套列表参考[列表渲染多层嵌套循环及wx:key的使用--微信小程序入门教程](http://lanchenglv.com/article/2016/1124/wxapp_list_foreach.html)

* 关于input的type属性
    建议设置该属性，且设置后赋值一定要正确，特别是该页面下多个input，否则可能引起真机下该input无法聚焦输入
    
* 关于网络请求状态>400处理
    微信小程序网络请求对400以上的状态码会当做请求成功处理，也因此微信小程序对于客户端session会话验证方式采用了一套自己的方式，关于微信session会话参考官方[API](https://mp.weixin.qq.com/debug/wxadoc/dev/api/api-login.html#wxloginobject), 这里建议在请求封装函数里面做一个对status>=400的特别处理以解决问题

* 小程序页面间数据传递和更新上一页面数据
    小程序页面间数据传递使用的是类似网络GET请求般传递参数，非常不方便，这里建议重新封装一个自己的跳转工具类，参考[小程序踩坑记《三》复杂数据的传递](http://www.acfunc.com/小程序踩坑记-长按与点击事件冲突/), 还有另外一种方法，在传递数据时，强转对象为字符串，再新页面使用JSON.parse()转回对象；

* 关于小程序事件冒泡
    小程序点击事件有和web开发类似的冒泡机制，这里可以去看[官方文档](https://mp.weixin.qq.com/debug/wxadoc/dev/framework/view/wxml/event.html)关于catchbind和bind的不同点, 以及关于事件参数target和currentTarget的区别，良好使用这些参数，可以更加灵活的使用小程序的事件冒泡机制。
    
* 关于小程序长按事件和单击事件的冲突
    微信小程序事件触发顺序:
    单击	touchstart → touchend → tap
    长按	touchstart → longtap → touchend → tap
    为了解决这个冲突，这里提供良好的解决方案解决问题，可看代码：
    
    ```html
        // wxml
        <view bindtouchstart="bindTouchStart" bindtouchend="bindTouchEnd" bindtap="bindTap">蹂躏我</view>
    ```
    
    ```javascript
         // js
         bindTouchStart: function(e) {
            this.startTime = e.timeStamp;
         }
         bindTouchEnd: function(e) {
             this.endTime = e.timeStamp;
         }
         bindTap: function(e) {
             if(this.endTime - this.startTime > 350) {
                console.log("长按");
             } else {
                console.log("点击")
             }
         }
    ```
    



