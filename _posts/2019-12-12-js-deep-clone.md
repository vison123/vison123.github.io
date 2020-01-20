---
layout: post
title:  JS中深拷贝的几种方法
date:   2019-12-12 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - JavaScript
---


### JS中深拷贝的几种方法

#### 循环拷贝

```javascript
deepClone (initialObj) {
  if (typeof initialObj !== 'object') {
    return initialObj
  }
  let obj = {}
  for (let key in initialObj) {
    if (typeof initialObj[key] === 'object') {
      if (Array.isArray(initialObj[key])) {
        // 数组
        obj[key] = initialObj[key].map(item => this.deepClone(item))
      } else {
        // 普通对象
        obj[key] = this.deepClone(initialObj[key])
      }
    } else if (typeof initialObj[key] === 'function') {
      // 方法
      obj[key] = initialObj[key].bind(obj)
    } else {
      // 基本数据类型
      obj[key] = initialObj[key]
    }
  }
  return obj
}

```

#### 通过 JSON 对象实现深拷贝
```javascript
deepClone (initialObj) {
  return JSON.parse(JSON.stringify(initialObj))
}
```
缺点是处理循环依赖对象会报错，如下：
```javascript
// 对包含循环引用的对象（对象之间相互引用，形成无限循环）执行此方法，会抛出错误。 
const obj = {
  name: "loopObj"
};
const loopObj = {
  obj
};
// 对象之间形成循环引用，形成闭环
obj.loopObj = loopObj;

// 执行深拷贝，抛出错误
deepClone(obj)
/**
 VM44:9 Uncaught TypeError: Converting circular structure to JSON
    --> starting at object with constructor 'Object'
    |     property 'loopObj' -> object with constructor 'Object'
    --- property 'obj' closes the circle
    at JSON.stringify (<anonymous>)
    at deepClone (<anonymous>:9:26)
    at <anonymous>:11:13
 **/

```

#### Object.assign()拷贝

```javascript
deepClone (initialObj) {
  return Object.assign({}, initialObj)
}
```

当对象中只有一级属性，没有二级属性的时候，此方法为深拷贝，但是对象中有对象的时候，此方法，在二级属性以后就是浅拷贝。

#### 第三方库实现深拷贝

lodash是很热门的函数库，提供了 lodash.cloneDeep()实现深拷贝
