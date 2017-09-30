---
layout: post
title:  CSS Modules
date:   2017-09-30 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - CSS Modules
---

讲一下写这篇文章的背景，之前工作一直在围绕ReactNative Android原生以及后台，对React Web端接触的较少。对React和Redux以及ES6语法倒是较
为熟悉，但是对React Web端开发还是有些盲区。加上最近刚入职新公司，接下来一段时间可能会重点放在React Web端开发，所以最近进行了一些扫盲，对
Web端开发进行了一些实验和思考，在这个过程中发现对CSS Modules可能不是很理解。做了一些试验，对CSS Modules有了一些新的感悟吧，所以对这些想
法做一些整理。

### 什么是 CSS Modules？

CSS Modules？是CSS吗？那你就错了，CSS Modules不是CSS，它只是将CSS中加入局部作用域和模块依赖。使得CSS能够应用到组件中去，并且将CSS的
全局作用域改成局部作用域，这样组件样式之间就会进行隔离。样式文件形成局部作用域，和其他文件之间有相同的classname也不会互相影响。所以，
CSS Modules只是一种CSS在组件编程中的一种解决方案。CSS Modules 能最大化地结合现有 CSS 和 JS 模块化能力，API 简洁到几乎零学习成本。
发布时依旧编译出单独的 JS 和 CSS。它并不依赖于 React，只要你使用 Webpack，可以在 Vue/Angular等任何框架中使用。当然CSS 模块化的解决
方案有很多，但CSS Modules是目前最好的 CSS 模块化解决方案之一。

### 如何产生局部作用域？

大家都知道CSS规则是全局的，CSS .class 选择器选取带有指定类 (class) 的元素，如果样式中出现两个相同命名的class选择器，他们将会互相影响。

```
<!DOCTYPE html>
<html>
    <head>
        <style>
        .intro{
            background-color:yellow;
            text-align:center;
        }
        </style>
        <style>
        .intro{
            background-color:red;
        }
        </style>
    </head>
    <body>
        <div class="intro">
            <p>我是唐老鸭。</p>
        </div>
    </body>
</html>

```
大家运行上面例子后可以发现，p标签中文字为红色居中，这就说明了CSS规则是全局的，class命名相同的话会有样式的冲突，即使打包在不同的style中
也会互相影响。那么问题来了，如何让CSS规则产生局部作用域呢？其实我们只要打包时将class命名给改掉，用一个独一无二的class的名字进行替换就好
了啊，这样就不会与其他class命名冲突了。

### 解决办法

上述例子是为了引发大家对CSS Modules的思考，下面就会对React中CSS Modules的解决方案进行分析。写了一个React组件如下：

```
import React from 'react';
import { connect } from 'react-redux';
import style from './index.css';

class Home extends React.Component {

  constructor(props) {
    super(props);
    this.state = {
    };
  }

  render() {
    return (
      <div className={style.title}>
        首页
      </div>
    );
  }
}

export default connect()(Home);
```
组件中国年引用的样式文件index.css如下：

```
.title {
  size: 20px;
  text-align: center;
}
```
该组件在项目中使用Webpack build以后界面元素如下所示：

```
.byzpuW3R6MEgvP-zXxPHu {
    size: 20px;
    text-align: center;
}

<div class="byzpuW3R6MEgvP-zXxPHu">
    首页
</div>
```

大家可以发现，.title在被Hash过以后变成了.byzpuW3R6MEgvP-zXxPHu，这样就能保证该class是唯一的。而style.title的引用也被编译成了Hash后
的字符串，这样以来就能保证只有引用该路径下的CSS中的title才会使用该样式，就达到了隔离的目的。

### 如何实现的呢？

上面例子中Hash .class是因为项目中使用了Webpack，不止Webpack，CSS Modules 也提供其他各种[插件](
https://github.com/css-modules/css-modules/blob/master/docs/get-started.md)，Browserify、JSPM、NodeJS等。
我们使用的是 Webpack 的css-loader插件，比较容易使用，下面是一个Webpack 使用 css-loader的一个例子。

```
module.exports = {
  entry: __dirname + '/index.js',
  output: {
    publicPath: '/',
    filename: './bundle.js'
  },
  module: {
    loaders: [
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        loader: 'babel',
        query: {
          presets: ['es2015', 'stage-0', 'react']
        }
      },
      {
        test: /\.css$/,
        loader: "style-loader!css-loader?modules&localIdentName=[name]__[local]-[hash:base64:5]"
      },
    ]
  }
};
```

### 全局作用域怎么办？

一直在讲CSS如何实现局部作用域，搞到最后发现css文件都变成局部的了，那全局可怎么办啊 ::>_<:: 。不用担心，其实CSS Modules早就考虑到这个问题了。
其实使用了 CSS Modules 后，就相当于给每个 class 名外加加了一个 `:local`，以此来实现样式的局部化，如果你想切换到全局模式，使用对应的 `:global`。
使用:global(.className)的语法，声明一个全局规则，使用这种方式声明的class都不会被编译成哈希字符串。 另外要说明一下，CSS Modules
只会转换 class 名相关样式， id 选择器、伪类、标签选择器将不被转换，原封不动的出现在编译后的 css 中。
```
.link {
  color: green;
}

/* 以上与下面等价 */
:local(.normal) {
  color: green;
}


/* 可定义多个全局样式 */
:global {
  .link {
    color: green;
  }
  .box {
    color: yellow;
  }
}
```

### 使用Compose 来组合样式

对于样式复用，CSS Modules 只提供了唯一的方式来处理：`composes` 组合

```
/* components/Button.css */
.base { /* 所有通用的样式 */ }

.normal {
  composes: base;
  /* normal 其它样式 */
}

.disabled {
  composes: base;
  /* disabled 其它样式 */
}
```

```
import styles from './Button.css';

buttonElem.outerHTML = `<button class=${styles.normal}>Submit</button>`

```
生成的 HTML 变为

```
<button class="button--base-fec26 button--normal-abc53">Submit</button>
```

由于在 `.normal` 中 composes 了 `.base`，编译后会 normal 会变成两个 class。

composes 还可以组合外部文件中的样式。

```
/* settings.css */
.primary-color {
  color: #f40;
}

/* components/Button.css */
.base { /* 所有通用的样式 */ }

.primary {
  composes: base;
  composes: primary-color from './settings.css';
  /* primary 其它样式 */
}
```

### CSS Modules 使用技巧

CSS Modules 使用建议遵循如下原则：

* 不使用选择器，只使用 class 名来定义样式
* 不层叠、不嵌套多个 class，只使用一个 class 把所有样式定义好
* style 文件中使用了 id 选择器，伪类，标签选择器等
* 不要对一个元素使用多个 class
* 使用 `composes` 组合来实现复用

作为一个CSS本来就不怎么熟练的开发者来说，前两项对我倒没声明大问题，但是CSS大神们可能就接受不了了。明明很简单就能定义出来，为什么要这么麻烦。
这种做法可能会抛弃掉CSS中最精华的部分，但是这样会让CSS Modules更加简单易用，而且有一个缺点项目中class数量将成倍上升。

