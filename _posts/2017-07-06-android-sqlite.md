
---
layout: post
title:  Android SQLite 使用
date:   2017-04-26 01:08:00 +0800
header-img: "img/post-bg-2015.jpg"
author:  "Vison"
catalog: true
tags:
    - 数据库
    - Android
---

## 创建数据库

## 升级数据库
  数据库升级主要有两种情况：
   * 从未安装过该App
     直接执行SQLitehelper 中的onCreate()方法
     onCreate()方法中只执行第一个版本的数据库脚本，然后手动调用onUpgrade()，onUpgrade()中进行for循环和switch...case
     for循环是进行数据库的连续升级，switch...case根据版本号执行相应的升级方法
   * 安装过老版本App（更新App）
     执行SQLitehelper 中的onUpgrade()方法

## 备份数据库
  * 修改表名为临时表
  * 创建新表
  * 将临时表数据插入到新表（新增字段用空格表示）
  * 在新表中更新新增字段（根据条件update或直接给出默认值）
  
## 提升数据库版本号
  数据库版本若没有提升到最新，下次启动应用还会进行升级，影响效率及对数据库数据造成影响
