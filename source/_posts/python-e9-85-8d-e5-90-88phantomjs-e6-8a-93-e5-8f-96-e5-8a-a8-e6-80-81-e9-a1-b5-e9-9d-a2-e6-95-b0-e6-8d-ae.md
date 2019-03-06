---
title: python配合phantomjs抓取动态页面数据
tags:
  - phantomjs
  - Python
url: 2051.html
id: 2051
categories:
  - 编程随想
date: 2015-09-08 01:32:04
---

首先事先准备js页面，方便phantomjs调用。路径为 d:\\stdout.js 需要网页地址作为第一个参数

    var page = require('webpage').create(),
        args = require('system').args;
    if(args.length == 1){
        console.log(false)
        phantom.exit()
    }
    var url = args[1];
    page.settings = {
      javascriptEnabled: true,
      loadImages: false, //禁止加载图片，提供速度
      userAgent: 'Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.31 (KHTML, like Gecko) PhantomJS/19.0'
    };
    page.open(url, function (status) {
        if(status != "success"){
            console.log(false)
        }else{
            var result = page.evaluate(function(){
                return document.documentElement.innerHTML;
            });
            console.log(result);//标准输出，方便python获取
        }
        phantom.exit();
    });
    

python中则可利用截获命令行的标准输出获取到phantomjs的输出数据

    import subprocess
    
    url = r"http://www.baidu.com"
    cmd = "phantomjs d:\stdout.js %s" % url
    stdout,stderr = subprocess.Popen(cmd,shell=True,stdout=subprocess.PIPE,stderr=subprocess.PIPE).communicate()
    print(stdout)