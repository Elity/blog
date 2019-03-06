---
title: nginx学习笔记
tags:
  - nginx
url: 2372.html
id: 2372
comments: false
categories:
  - 编程随想
date: 2018-09-24 22:12:24
---

变量
--

### 自定义变量

set $a 123; set $b "$a 456"; set $c "{$b}789"

> 自定义变量在配置被加载时创建，执行时才被赋值。定义全局有效，甚至可以跨vhost，但值仅在内部跳转间有效。[详见](http://openresty.org/download/agentzh-nginx-tutorials-zhcn.html#01-NginxVariables02)

### 常用内置变量

$request $request\_uri $uri $args $arg\_xxx $header\_xxx $cookie\_xxx [更多。。。](http://nginx.org/en/docs/http/ngx_http_core_module.html#variables) !!!任何时候都不应该去覆盖内置变量，有些内置变量被覆盖会导致报错甚至进程崩溃。

### map

对于配置CORS的域名白名单，使用map是比if更好的选择，毕竟[If Is Evil](https://www.nginx.com/resources/wiki/start/topics/depth/ifisevil/) map只能写于http模块，但不用担心访问任何location都会计算map，nginx的[ngx_map](http://nginx.org/en/docs/http/ngx_http_map_module.html)模块实现了只有在该用户变量被实际读取时才会执行，也就是惰性求值。[更多参考](http://openresty.org/download/agentzh-nginx-tutorials-zhcn.html#01-NginxVariables04)。

    # $http_origin是自变量，而$cors_header是因变量
    map $http_origin $cors_header {
        default "";
        "~^https?://(localhost|www\.abc\.com" "$http_origin";
    }
    
    server {
        ...
        location / {
            add_header Access-Control-Allow-Origin $cors_header;
            try_files $uri @other_location; # This try_files is working
        }
        ...
     }
    

rewrite
-------

server中的rewrite break和last没什么区别，都会去匹配location，所以没必要用last再发起新的请求，可以留空，以下针对location中的rewrite：

### last

用重写后的uri重新去匹配location，默认10次匹配不到报500错误

### break

终止rewrite阶段，执行后续的其它阶段，对比last，不会重新去匹配location

### redirect

302临时重定向

### permanent

301永久重定向

if
--

由于nginx中的配置是分阶段执行的，而非一般编程语言中的线性执行，所以执行顺序看上去会有些怪异，再加上nginx内部对于if处理的一些机制，让初学者很是不解。看文章：[How nginx "location if" works](http://damacheng009.iteye.com/blog/1583879)

location匹配
----------

语法：

     location [=|~|~*|^~] /uri/ { … }
     location @name { ... } 
    

demo

    location  = / {
      # 精确匹配 / ，主机名后面不能带任何字符串
      [ configuration A ]
    }
    
    location  / {
      # 因为所有的地址都以 / 开头，所以这条规则将匹配到所有请求
      # 但是正则和最长字符串会优先匹配
      [ configuration B ]
    }
    
    location /documents/ {
      # 匹配任何以 /documents/ 开头的地址，匹配符合以后，记住还要继续往下搜索
      # 只有后面的正则表达式没有匹配到时，这一条才会采用这一条
      [ configuration C ]
    }
    
    location ~ /documents/Abc {
      # 匹配任何以 /documents/Abc 开头的地址，匹配符合以后，还要继续往下搜索
      # 只有后面的正则表达式没有匹配到时，这一条才会采用这一条
      [ configuration CC ]
    }
    
    location ^~ /images/ {
      # 匹配任何以 /images/ 开头的地址，匹配符合以后，停止往下搜索正则，采用这一条。
      [ configuration D ]
    }
    
    location ~* \.(gif|jpg|jpeg)$ {
      # 匹配所有以 gif,jpg或jpeg 结尾的请求
      # 然而，所有请求 /images/ 下的图片会被 config D 处理，因为 ^~ 到达不了这一条正则
      [ configuration E ]
    }
    
    location /images/ {
      # 字符匹配到 /images/，继续往下，会发现 ^~ 存在
      [ configuration F ]
    }
    
    location /images/abc {
      # 最长字符匹配到 /images/abc，继续往下，会发现 ^~ 存在
      # F与G的放置顺序是没有关系的
      [ configuration G ]
    }
    
    location ~ /images/abc/ {
      # 只有去掉 config D 才有效：先最长匹配 config G 开头的地址，继续往下搜索，匹配到这一条正则，采用
        [ configuration H ]
    }