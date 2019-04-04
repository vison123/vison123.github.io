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

### JS

* js事件循环？

* JS ES5语法利用原型链实现继承？

* 使用js实现单例模式

* 数组去重最快的方法

```javascript
  Array.from(new Set(array))
``` 

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

