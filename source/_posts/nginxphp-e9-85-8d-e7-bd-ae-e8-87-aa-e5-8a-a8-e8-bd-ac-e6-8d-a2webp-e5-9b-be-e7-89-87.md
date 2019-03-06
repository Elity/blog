---
title: Nginx+PHP配置自动转换webp图片
tags:
  - nginx
  - PHP
  - webp
url: 2104.html
id: 2104
categories:
  - 编程随想
date: 2016-11-16 20:04:50
---

但如今对于 JPEG、PNG 和 GIF 这些图片格式的优化几乎已经达到了极致， 若想改变现状开辟新局面，便要有釜底抽薪的胆量和气魄，而 Google 给了我们一个新选择：WebP。 WebP 的优势体现在它具有更优的图像数据压缩算法，能带来更小的图片体积，而且拥有肉眼识别无差异的图像质量；同时具备了无损和有损的压缩模式、Alpha 透明以及动画的特性，在 JPEG 和 PNG 上的转化效果都非常优秀、稳定和统一。 科技博客 Gig‍‍‍aOM 曾报道：YouTube 的视频略缩图采用 WebP 格式后，网页加载速度提升了 10%；谷歌的 Chrome 网上应用商店采用 WebP 格式图片后，每天可以节省几 TB 的带宽，页面平均加载时间大约减少 1/3；Google+ 移动应用采用 WebP 图片格式后，每天节省了 50TB 数据存储空间。http://isux.tencent.com/introduction-of-webp.html 目前，也仅仅只有Chromium系浏览器支持该图片格式。不过，由于Android队伍的不断壮大，Chromium内核浏览器的全球份额已经达到了70%+，数据详见：http://caniuse.com/#feat=webp 所以，对于图片占据大部分流量的网站来说，有针对性的提供webp格式的图片不但能极大提升用户体验，亦能为网站省下不小流量费用。 本来应该采用Nginx+Lua来实现的，但我工作环境用的是Windows，然后搜索了一下发现Windows下为Nginx安装Lua模块颇为麻烦，所以退而求其次采用了Nginx+PHP来完成。 翻了下[《OpenResty最佳实践》](https://www.gitbook.com/book/moonbingbing/openresty-best-practices)，作者告诫尽量不要在Nginx配置里面使用if，甚至可以说[if是邪恶的](https://moonbingbing.gitbooks.io/openresty-best-practices/content/ngx/if_is_evil.html)。但目前由于没法用上Lua+水平有限，依然没能避免用一些`if`，SO，就当记个日志在这，方便自己查阅，以后再来好好折腾。

    location ~ (/img/[^\.]+)(\.(?:jpg|jpeg|png|gif))$ {
     if (!-f $request_filename) { # 原始图片文件不存在
     break;
     }
     expires 15d;
     if ($http_accept !~* "webp") { # 浏览器不支持webp
     break;
     }
     try_files $uri.webp @webphandle;
    
    }
    
    # 用其它服务处理转换webp
    #location @webphandle {
     #proxy_pass http://127.0.0.1/webp.php?img=$uri;
    #}
    
    # 用fastcgi把转换请求转给php
    location @webphandle {
     set $webp "/webp.php";
     fastcgi_pass 127.0.0.1:9000;
     fastcgi_param SCRIPT_FILENAME $document_root$webp;
     fastcgi_param QUERY_STRING img=$uri;
    }
    

PHP部分。没有过滤文件名，暂不清楚有没有被远程执行命令的可能.

    <?php
    
    if(!isset($_GET['img']) || !file_exists(substr($_GET['img'],1))){
     header("HTTP/1.1 404 Not Found");
     echo "<h1>Not Found!</h1>";
     exit; 
    }
    
    $img = substr($_GET['img'],1);
    $img_webp = $img.'.webp';
    if(!file_exists($img_webp)){
     `C:\webp\bin\cwebp -q 100 $img -o $img_webp`;
    }
    
    header('Content-type: image/webp');
    readfile($img_webp);
    

最后是webp转换程序的下载地址： https://developers.google.com/speed/webp/docs/precompiled 2018.09.29 update：这一年多的体验：阿里云的OSS好用到哭啊，对于任意格式的图片，加个特定的后缀，拿到的就是webp格式了，而且还支持各种姿势的裁切、压缩。人生苦短，少折腾，有现成的就用吧。