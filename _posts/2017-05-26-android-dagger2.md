---
layout: post
title:  Android Dagger2
date:   2017-05-26 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - Android
---


### 添加依赖

在项目根目录下的build.gradle中的dependencies代码块中加入依赖:

```java
classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
```

加完以后的build.gradle如下：

```java
buildscript {
    repositories {
        jcenter()
        mavenCentral()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.1.0'
        classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
        //配置DBFlow
    }
}

allprojects {
    repositories {
        maven { url "https://www.jitpack.io" }
        jcenter()
        mavenCentral(）
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}
```

项目下的build.gradle文件顶部 apply plugin: ‘com.Android.application’ 下方添加：

```java
apply plugin: 'android-apt'
```

dependencies代码块中添加Dagger2的依赖关系


```java
//使用APT生成工具，生成需要的DI代码
apt 'com.google.dagger:dagger-compiler:2.5'
//JSR250的jar包，使用这个和使用glassFish的那个一样，仅为了使用@Inject 和@Named注解
provided 'javax.annotation:jsr250-api:1.0'
//Dagger2 的依赖
compile 'com.google.dagger:dagger:2.5'
```
