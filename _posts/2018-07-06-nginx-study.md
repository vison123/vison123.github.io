---
layout: post
title:  Nginx学习笔记
date:   2018-03-05 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - Nginx
---


最近在开发Vue实现服务端渲染的时候，需要使用Nginx验证一下其部署方案的可行性，果断本地
部署了一套验证一下，在此做一下记录

### Nginx常用命令

```
nginx: 启动nginx
nginx -c /path/to/nginx.conf: 启动nginx
nginx -s reload: 修改配置后重新加载生效
kill -HUP 主进程号: 平滑重启nginx
sudo -i && nginx: 以root权限启动

nginx -s stop :快速停止nginx
quit ：完整有序的停止nginx
kill -QUIT 主进程号 ：从容停止Nginx
kill -TERM 主进程号 ：快速停止Nginx
pkill -9 nginx ：强制停止Nginx


nginx -s reopen ：重新打开日志文件
nginx -t -c /path/to/nginx.conf 测试nginx配置文件是否正确

```

### 本地多web应用部署

因为前端项目大多都是静态文件，基本都是一台机器部署很多个应用，各应用启用不同的端口，
配置如下：nginx.conf为Nginx配置根目录，web.conf和mobile.conf分别为两个web 应用，
通过include引入进来。

* nginx.conf配置

```javascript
#user  nobody;
worker_processes  2;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       8080;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html/mobile;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
    include servers/*;
}


```

* web.conf配置

```javascript
server {
    listen       8081;
    server_name  localhost;

    #charset koi8-r;

    #access_log  logs/host.access.log  main;

    location / {
        root   html/vue-nuxt;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }
}
```

* mobile.conf配置

```javascript
server {
    listen       8082;
    server_name  localhost;

    #charset koi8-r;

    #access_log  logs/host.access.log  main;

    location / {
        root   html/vue-mobile;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }
}
```

### 反向代理

web应用默认端口80端口，所以生产环境多个web应用对应多个域名，都要访问80端口才能实现。
Nginx反向代理就可以解决这个问题，这也是Nginx很重要的一个功能。在上面配置的基础上进行修改，
只需要修改nginx.conf即可

* nginx.conf配置

```javascript
...

http {
     ...
     server {
         listen 80;
         server_name a.visonsoft.cn;
         location / {
             proxy_pass http://127.0.0.1:8081;
             #反向代理
         }
     }

      server {
         listen 80;
         server_name b.visonsoft.cn;
         location / {
             proxy_pass http://127.0.0.1:8082;
             #反向代理
         }
     }
     ...
}
...

```

ps：a.visonsoft.cn、b.visonsoft.cn两个域名是通过修改hosts文件本地模拟的.