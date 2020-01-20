---
layout: post
title:  前端面试题汇总
date:   2018-07-30 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - CSS
    - JS
---

### CSS

* 盒子模型？

* 垂直居中？

* 清除浮动？

* 块级元素和行内元素？

* CSS选择器及其权重？

* px、em、rem？

* CSS & LESS & SCSS

### HTTP

* 跨域请求？

   JsonP跨域、iframe跨域、postMessage跨域、服务端设置CORS、Nginx代理跨域

* http请求方式、请求头、返回头、状态码？

* 浏览器输入网址到打开页面的所有步骤

### 浏览器

### JS

* 为什么 Javascript 要是单线程的？

这是因为 Javascript 这门脚本语言诞生的使命所致!JavaScript 为处理页面中用户的交互,
以及操作 DOM 树、CSS 样式树来给用户呈现一份动态而丰富的交互体验和服务器逻辑的交互处理。

如果 JavaScript 是多线程的方式来操作这些 UI DOM,则可能出现 UI 操作的冲突。

如果 Javascript 是多线程的话,在多线程的交互下,处于 UI 中的 DOM 节点就可能成为一个临界资源,
假设存在两个线程同时操作一个 DOM,一个负责修改一个负责删除,那么这个时候就需要浏览器来裁决如何生效哪个线程的执行结果。

当然我们可以通过锁来解决上面的问题。但为了避免因为引入了锁而带来更大的复杂性,Javascript 在最初就选择了单线程执行。

* 为什么 JS 阻塞页面加载？

由于 JavaScript 是可操纵 DOM 的,如果在修改这些元素属性同时渲染界面（即 JavaScript 线程和 UI 线程同时运行）,那么渲染线程前后获得的元素数据就可能不一致了。

因此为了防止渲染出现不可预期的结果,浏览器设置 GUI 渲染线程与 JavaScript 引擎为互斥的关系。

因此如果 JS 执行的时间过长,这样就会造成页面的渲染不连贯,导致页面渲染加载阻塞的感觉。

* js事件循环？

* JS ES5语法利用原型链实现继承？

* 使用js实现单例模式

* 数组去重最快的方法

```javascript
  Array.from(new Set(array))
``` 
### css

* 加载会造成阻塞吗 ？

DOM 和 CSSOM 通常是并行构建的,所以 CSS 加载不会阻塞 DOM 的解析。

然而,由于 Render Tree 是依赖于 DOM Tree 和 CSSOM Tree 的,
所以他必须等待到 CSSOM Tree 构建完成,也就是 CSS 资源加载完成(或者 CSS 资源加载失败)后,才能开始渲染。

因此,CSS 加载会阻塞 Dom 的渲染。

由于 JavaScript 是可操纵 DOM 和 css 样式 的,如果在修改这些元素属性同时渲染界面（即 JavaScript 线程和 UI 线程同时运行）,那么渲染线程前后获得的元素数据就可能不一致了。
因此为了防止渲染出现不可预期的结果,浏览器设置 GUI 渲染线程与 JavaScript 引擎为互斥的关系。

因此,样式表会在后面的 js 执行前先加载执行完毕,所以css 会阻塞后面 js 的执行。

### React

* React多次调用this.setState()是否会影响渲染性能

    this.setState(newState)
     ==>  newState存入pending队列   
       ==>  是否处于batchUpdate   
	        ==>     yes   保存组件于dirtyComponennts
	        ==>      no    遍历所有dirtyComponennts， 更新state

### Vue

* Vue如何实现MVVM

* Vue如何封装组件实现v-model绑定

* Vue extends & mixin

