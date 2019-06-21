---
layout: post
title:  Vue + Element中后台单页应用换肤实践
date:   2019-06-21 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - Vue
---

### 换肤需求及方案制定

Vue + Element管理后台项目需要用到换肤功能，指定多个主题色进行切换，项目中UI颜色两部分：
* Element 组件
* 自定义组件及页面


#### Element组件换肤

获取theme-chalk的css文件，根据主题色动态生成相关颜色，利用正则表达式替换后动态生成style。
由于根据Element-ui的版本号网络请求获取css比较耗时，`https://unpkg.com/element-ui@${version}/lib/theme-chalk/index.css`，
后面改成用字符串维护在js中，加快换肤速度。
```javascript
<template>
  <el-color-picker
    v-model="theme"
    :predefine="themeArr"
    class="theme-picker"
    popper-class="theme-picker-dropdown"
  />
</template>

<script>
  // const version = require('element-ui/package.json').version // element-ui version from node_modules
  const ORIGINAL_THEME = '#409EFF' // default color
  // const version = require('element-ui/package.json').version // element-ui version from node_modules
  import themeChalk from './theme'

  export default {
    data () {
      return {
        chalk: themeChalk, // content of theme-chalk css
        theme: '',
        themeArr: ['#1A5FCD', '#FF8706']
      }
    },
    computed: {
      defaultTheme () {
        return ''
      }
    },
    watch: {
      defaultTheme: {
        handler: function (val, oldVal) {
          this.theme = val
        },
        immediate: true
      },
      async theme (val) {
        this.changeSkin(val)
      }
    },
    mounted () {
      this.theme = sessionStorage.getItem('cvf_theme')
      this.changeSkin(this.theme)
    },
    methods: {
      changeSkin (theme) {
        sessionStorage.setItem('cvf_theme', theme)
        const oldVal = this.chalk ? this.theme : ORIGINAL_THEME
        if (typeof theme !== 'string') return
        const themeCluster = this.getThemeCluster(theme.replace('#', ''))
        const originalCluster = this.getThemeCluster(oldVal.replace('#', ''))

        const getHandler = (variable, id) => {
          return () => {
            const originalCluster = this.getThemeCluster(ORIGINAL_THEME.replace('#', ''))
            const newStyle = this.updateStyle(this[variable], originalCluster, themeCluster)

            let styleTag = document.getElementById(id)
            if (!styleTag) {
              styleTag = document.createElement('style')
              styleTag.setAttribute('id', id)
              document.head.appendChild(styleTag)
            }
            styleTag.innerText = newStyle
          }
        }

        const chalkHandler = getHandler('chalk', 'chalk-style')

        chalkHandler()

        const styles = [].slice.call(document.querySelectorAll('style'))
          .filter(style => {
            const text = style.innerText
            return new RegExp(oldVal, 'i').test(text) && !/Chalk Variables/.test(text)
          })
        styles.forEach(style => {
          const { innerText } = style
          if (typeof innerText !== 'string') return
          style.innerText = this.updateStyle(innerText, originalCluster, themeCluster)
        })

        this.$emit('change', theme)
        // 自定义样式主题覆盖
        let index = this.getArrayIndexByValue(this.themeArr, theme)
        document.body.classList = []
        document.body.classList.add(`theme-${index + 1}`)
        // 流媒体播放器主题覆盖
        let styleTag = document.getElementById('player-style')
        if (!styleTag) {
          styleTag = document.createElement('style')
          styleTag.setAttribute('id', 'player-style')
          document.head.appendChild(styleTag)
        }
        styleTag.innerText = `
          .km-app-container {
            --km-themeColor: ${theme} !important;
          }`
      },
      updateStyle (style, oldCluster, newCluster) {
        let newStyle = style
        oldCluster.forEach((color, index) => {
          newStyle = newStyle.replace(new RegExp(color, 'ig'), newCluster[index])
        })
        return newStyle
      },
      getThemeCluster (theme) {
        const tintColor = (color, tint) => {
          let red = parseInt(color.slice(0, 2), 16)
          let green = parseInt(color.slice(2, 4), 16)
          let blue = parseInt(color.slice(4, 6), 16)

          if (tint === 0) { // when primary color is in its rgb space
            return [red, green, blue].join(',')
          } else {
            red += Math.round(tint * (255 - red))
            green += Math.round(tint * (255 - green))
            blue += Math.round(tint * (255 - blue))

            red = red.toString(16)
            green = green.toString(16)
            blue = blue.toString(16)

            return `#${red}${green}${blue}`
          }
        }

        const shadeColor = (color, shade) => {
          let red = parseInt(color.slice(0, 2), 16)
          let green = parseInt(color.slice(2, 4), 16)
          let blue = parseInt(color.slice(4, 6), 16)

          red = Math.round((1 - shade) * red)
          green = Math.round((1 - shade) * green)
          blue = Math.round((1 - shade) * blue)

          red = red.toString(16)
          green = green.toString(16)
          blue = blue.toString(16)

          return `#${red}${green}${blue}`
        }

        const clusters = [theme]
        for (let i = 0; i <= 9; i++) {
          clusters.push(tintColor(theme, Number((i / 10).toFixed(2))))
        }
        clusters.push(shadeColor(theme, 0.1))
        return clusters
      },
      getArrayIndexByValue (arr, str) {
        for (let i = 0; i < arr.length; i++) {
          if (arr[i] === str) {
            return i
          }
        }
      }
    }
  }
</script>

<style>
  .theme-message,
  .theme-picker-dropdown {
    z-index: 99999 !important;
  }

  .theme-picker .el-color-picker__trigger {
    height: 26px !important;
    width: 26px !important;
    padding: 2px;
  }

  .theme-picker-dropdown .el-color-dropdown__link-btn {
    display: none;
  }

  .el-color-picker {
    display: inline-block !important;
    position: relative !important;
    line-height: normal !important;
    height: 64px !important;
    margin-top: 40px;
    margin-right: 5px;
  }

  .el-color-dropdown__main-wrapper {
    display: none;
  }
</style>

```

#### 自定义组件及页面换肤

页面自定义颜色通过在body上面添加class 控制颜色的变化
```javascript
   // 自定义样式主题覆盖
   let index = this.getArrayIndexByValue(this.themeArr, theme)
   document.body.classList = []
   document.body.classList.add(`theme-${index + 1}`)
```
```scss
$--theme-color-1: #1A5FCD;
$--theme-color-1-menu: #0E4AAA;
$--theme-color-1-menu-active: #07409b;
.theme-1 {
  /*头部menu根据主题覆盖*/
  .header {
    background-color: $--theme-color-1-menu;
    .menu{
      .el-menu {
        background-color: $--theme-color-1-menu;
        .iconfont {
          display: inline !important;
          color: #fff;
          position: relative;
          top: -1px;
          right: 1px;
        }
        @media (max-width: 1350px) {
          .el-submenu__title,.el-menu-item{
            padding: 0px 10px;
          }
        }
        .el-submenu{
        }
      }
    }
    .el-menu--horizontal {
      height: 64px;
      border-bottom: none !important;
      .el-menu-item {
        font-family: PingFangSC-Medium;
        border-bottom: none;
        height: 64px;
        &.is-active {
          border-bottom: none;
          height: 64px;
          background: $--theme-color-1-menu-active !important;
        }
        &:hover {
          border-bottom: none;
          height: 64px;
          background: $--theme-color-1-menu-active !important;
        }
      }
      > .el-submenu {
        height: 64px;
        border-left: 1px solid mix(#FFFFFF, $--theme-color-1, 10%);
        .el-submenu__title {
          border-bottom: none;
          height: 64px;
          font-family: PingFangSC-Medium;
        }
        &.is-active .el-submenu__title {
          border-bottom: none;
          background: $--theme-color-1-menu-active !important;
        }
        &:hover {
          border-bottom: none;
          background: $--theme-color-1-menu-active !important;
          .el-submenu__title {
            border-bottom: none;
            background: $--theme-color-1-menu-active !important;
          }
        }
      }
    }
  }
  /*二级菜单下拉窗*/
  .el-menu--popup {
    background: #fff !important;
    padding: 0;
    min-width: 180px;
    .el-menu-item,.el-submenu__title {
      background-color: #fff !important;
      color: #666 !important;
      height: 40px !important;
      font-family: none;
      padding-left: 30px !important;
      &:not(:first-child) {
        border-top: 1px solid #E6E9EF;
      }
    }
    .el-menu-item:hover,.el-submenu__title:hover {
      background: mix(#FFFFFF, $--theme-color-1, 95%) !important;
      color: $--theme-color-1 !important;
    }
    .el-menu-item.is-active,.el-submenu.is-active > .el-submenu__title {
      background: mix(#FFFFFF, $--theme-color-1, 95%) !important;
      color: $--theme-color-1 !important;
      height: 40px;
    }
  }
  /*tab根据主题覆盖*/
  .tab-list {
    >span {
      &.active {
        color: $--theme-color-1;
        border-bottom: 3px solid $--theme-color-1;
      }
    }
    >span:hover {
      color: $--theme-color-1;
      border-bottom: 3px solid $--theme-color-1;
    }
  }
  /*地图左侧menu样式主题覆盖*/
  .map-tab-list {
    .map-tab-item {
      &.active {
        background-color: mix(#FFFFFF, $--theme-color-1, 85%);
      }
      &:hover {
        background-color: mix(#FFFFFF, $--theme-color-1, 85%);
      }
    }
  }

  /*标准详情页左侧menu主题覆盖*/
  .el-menu-vertical-demo{
    .el-menu-item.is-active {
      background: #fff3e6;
      color: $--theme-color-1;
      border-right: 3px solid $--theme-color-1;
    }
  }
  /*标准详情页顶部title主题覆盖*/
  .all-things-top-title {
    &:before {
      background-color: $--theme-color-1;
    }
  }
  /*列表操作按钮主题覆盖*/
  .el-table {
    .el-table__body-wrapper .el-table__row {
      td {
        // 列表中图标样式
        .iconfont {
          &:hover {
            color: $--theme-color-1;
          }
        }
      }
    }
    .el-table__body-wrapper .current-row {
      background: mix(#FFFFFF, $--theme-color-1, 90%);
    }
  }
  /*数据字典左侧列表样式主题覆盖*/
  .dictionary-left-list {
    li:hover {
      background: mix(#FFFFFF, $--theme-color-1, 95%);
      i {
        color: $--theme-color-1;
      }
    }
    li.active {
      background: mix(#FFFFFF, $--theme-color-1, 95%);
      i {
        color: $--theme-color-1;
      }
    }
  }
  /*首页dashboard主题覆盖*/
  .rightContents {
    >span {
      &.selectTime {
        &.active {
          background: $--theme-color-1;
          border: 1px solid $--theme-color-1;
        }
      }
    }
  }
  .fourTab {
    >div {
      .go-detail{
        color: $--theme-color-1;
      }
      &:hover{
        background: mix(#FFFFFF, $--theme-color-1, 95%);
        border: 2px solid $--theme-color-1;
      }
    }
  }
  /*案事件详情弹窗主题覆盖*/
  .tab-list-relate {
    span {
      &.active {
        color: $--theme-color-1;
        border-bottom: 3px solid $--theme-color-1;
      }
    }
  }
  .case-event-detail-left-menu {
    .menu-name-active {
      color: $--theme-color-1;
    }
  }
  /*首页leftMenu主题覆盖*/
  .home-tree-wrap-index {
    .home-tree-box-wrapper {
      .left-menu {
        .left-menu-content {
          .menu-name-active {
            color: $--theme-color-1;
          }
        }
      }
    }
  }
  /*首页九宫格主题覆盖*/
  .video-control {
    .iconfont {
      color: #888888;
    }
    .icon-ic-smallscreen, .icon-ic-fullscreen {
      margin-right: 20px;
    }
    .icon-ic-smallscreen:hover {
      color: $--theme-color-1;
    }
    .icon-ic-fullscreen:hover {
      color: $--theme-color-1;
    }
    .icon-ic-delete1{
      margin-right: 18px;
      padding: 4px;
    }
    .icon-ic-delete1:hover {
      color: #FFFFFF;
      background: #FB4747;
      border-radius: 4px;
    }
  }
  .video-palace-wrapper {
    .control-panel {
      .control-left {
        .control-page {
          p {
            span:first-child {
              color: $--theme-color-1;
            }
          }
        }
        .iconfont:hover {
          color: $--theme-color-1;
        }
        .icon-selected {
          color: $--theme-color-1;
        }
      }
      .control-right {
        .iconfont:hover {
          color: $--theme-color-1;
        }
        .icon-selected {
          color: $--theme-color-1;
        }

      }
    }
  }
  /*地图leftMenu主题覆盖*/
  .map-tree-wrap-index {
    .map-tree-box-wrapper {
      .left-menu {
        .left-menu-content {
          .menu-name-active {
            color: $--theme-color-1;
          }
        }
      }
    }
  }
  /*地图警情列表主题覆盖*/
  .case-event-list-item {
    &:hover {
      background: mix(#FFFFFF, $--theme-color-1, 95%);
    }
  }
  /*地图设备弹窗主题覆盖*/
  .device-control {
    .control-buttons-wrapper {
      .control-button-item {
        .iconfont {
          color: $--theme-color-1;
        }
      }
    }
  }
  /*地图工具主题覆盖*/
  .mapcontrol>span:hover,.mapcontrol>.rail>.content>i:hover{
    color: $--theme-color-1;
  }
  .mapcontrol .active {
    color: $--theme-color-1 !important;
  }
  .mapcontrol {
    .search-list-wrap{
      .el-input-group__append{
        background: $--theme-color-1;
      }
    }
  }
  /*列表顶部提示*/
  .table-alert{
    background: mix(#FFFFFF, $--theme-color-1, 95%);
    border: 1px solid mix(#FFFFFF, $--theme-color-1, 80%);
    i {
      color: $--theme-color-1;
    }
  }
}
```
### 参考资料
[](https://github.com/PanJiaChen/vue-element-admin/blob/master/src/components/ThemePicker/index.vue)
