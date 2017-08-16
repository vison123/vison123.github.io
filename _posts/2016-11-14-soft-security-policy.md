---
layout: post
title:  前后端安全策略以及防治措施
date:   2017-04-26 01:08:00 +0800
header-img: "img/post-bg-2015.jpg"
author:  "Vison"
catalog: true
tags:
    - 系统安全
---


# 后端

api安全等级

身份认证：密码，短信，u盾，

权限：角色和数据权限

token生产规则

token生命周期

token更换策略

访问限流

异常访问识别

输入参数校验：类型，边界，脚本，高权限html标签

配置参数管理：生产环境参数，最好动态从全局配置系统获取。

异常信息泄漏：应用，中间件不能泄漏没必要的异常信息，采用

访问日志：所有访问都需要有记录，关联对方真实ip，mac，用户id

敏感信息：密码，hash盐，日志，用户个人信息显示，

通信安全：https，evzhengs，hsts，TLS 1.2，ECDHE_RSA，AES_128_GCM

加密算法选择：

# web

- **浏览器安全**


 攻击类型          |     攻击方法        |     防御措施
----------        | ----------------  | ------------------  
跨站脚本攻击（XSS） | image请求窃取cookie，操作用户浏览器（构造请求）,XSS钓鱼（窃取密码，验证码），识别用户环境   | Cookie 设置 HttpOnly标记，Cookie与客户端IP绑定，输入检查(XSS Filter)，输出检查（安全编码）
跨站点请求伪造（CSRF）| 诱使用户跳转，伪造请求   | 验证码，Refer Check，Token（随机，不可预测）
点击劫持 | flash点击劫持，图片覆盖攻击，触屏劫持 | 禁止iframe的嵌套，跨域访问限制，H5新增属性sandbox进行限制
HTML5安全 | 新标签 video，audio，canvas（解析图片验证码）| 新增属性（iframe新增sandbox，为 a 和 area 标签定义新的LinkTypes： noreferrer）

- **服务器端安全**

 攻击类型         |     攻击方法        |     防御措施
----------       | ----------------  | ------------------  
注入攻击  |  SQL注入，XML注入,代码注入 ,CRLF 注入 | 使用预编译语句，统一字符集，检查数据类型，
文件上传漏洞 | web脚本语言，Flash策略文件 ，病毒木马文件，钓鱼图片（包含有脚本） | 文件目录不可执行，判断文件类型，修改文件名
认证与会话管理 | Session Fixation攻击，Session保持攻击 | 登陆完成后，重写SessionId；一定时间后，强制销毁Session
拒绝服务攻击（DDOS） |SYN Flood（伪造IP地址，三次握手），CC攻击（黑洞防御下，大量请求消耗资源，如查询数据库，读写文件）资源耗尽攻击 | 配置服务器配置文件（调小TimeOut、KeepAliveTimeOut，增大MaxClient），限制请求频率

 - 跨站脚本攻击（XSS）: Cookie 设置 HttpOnly标记；输入检查
 - 跨站点请求伪造（CSRF）:  验证码，Refer Check，Token（随机，不可预测）
 - 点击劫持：flash点击劫持，图片覆盖攻击，触屏劫持；禁止iframe的嵌套，跨域访问限制，H5新增属性sandbox进行限制
 - HTML5安全：新标签（video，audio，canvas（解析图片验证码）），新增属性（iframe新增sandbox，为 a 和 area 标签定义新的LinkTypes： noreferrer）

 - Cookie防止泄露：给Cookie加上HttpOnly的标签，如果使用了SSL，那么你需要仔细检查在SSL的请求中返回的Cookie值，是否指定了 Secure属性。

 - http请求参数加密：



# IOS



# 安卓

- **组件安全**

 - Activity安全，

 - Broadcase Reciver安全，

 - Service安全，

 - Content Provider安全

- **通信安全**
 - http请求参数加密（关键信息）

- **存储安全**
 -  内部存储安全：数据库加密，隐私数据加密（无论采用何种创建文件的模式，root后均可获取最高权限，关键信息必须加密）
 
 -  外部存储安全：不要将信息存放到外部存储，日志，保存图片等除外

- **发布安全**

 - 防止反编译：代码混淆，app加固(使用爱加密、乐固等)

 - 监测调试器：当检测到调试器的连接时终止运行

 - 检测模拟器：检测到应用运行在模拟器环境中，终止运行

 - 防止重编译：检查签名，校验保护（检测classes.dex文件的Hash值）

 - 防止权限攻击：android权限检查机制，串谋权限攻击，极限攻击监测

 - 渠道打包：不要再用签名来区分渠道，在 Android manifest 文件中使用 meta-data 来区分渠道
