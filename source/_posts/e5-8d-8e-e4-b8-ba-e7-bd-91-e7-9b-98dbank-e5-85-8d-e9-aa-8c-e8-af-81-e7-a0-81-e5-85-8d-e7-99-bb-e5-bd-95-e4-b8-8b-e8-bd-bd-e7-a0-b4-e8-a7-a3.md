---
title: 华为网盘(dbank)免验证码/免登录下载破解 (提供chrome扩展)
tags:
  - dbankm
  - 免登录
  - 免验证码
  - 华为网盘
  - 破解
url: 1597.html
id: 1597
categories:
  - 编程随想
date: 2012-11-11 15:18:05
---

很早就想去解决这个问题了，经常想搞又忘记了，今天光棍节嘛！！！在寝室无聊着，找了它的js文件 ，发现获取地址还是挺简单的 ，粗看一下，直接找到了迅雷地址获取的函数，也许还有原地址，懒得去找了~~~直接用迅雷地址然后Base64解码得到原地址。当然，Base64解码的函数它已经定义了 dbank.base64.decode()

关键文件 [http://st1.dbank.com/netdisk/js/custom-link2.js](http://st1.dbank.com/netdisk/js/custom-link2.js)

具体代码

dbank.crt = {}; 
(function() {
    var c = function(h, l) {
        var k = \[\],
        e = 0,
        d,
        g = "";
        for (var f = 0; f < 256; f++) {
            k\[f\] = f
        }
        for (f = 0; f < 256; f++) {
            e = (e + k\[f\] + h.charCodeAt(f % h.length)) % 256;
            d = k\[f\];
            k\[f\] = k\[e\];
            k\[e\] = d
        }
        f = 0;
        e = 0;
        for (var m = 0; m < l.length; m++) {
            f = (f + 1) % 256;
            e = (e + k\[f\]) % 256;
            d = k\[f\];
            k\[f\] = k\[e\];
            k\[e\] = d;
            g += String.fromCharCode(l.charCodeAt(m) ^ k\[(k\[f\] + k\[e\]) % 256\])
        }
        return g
    };
    var b = function(d, e) {
        var h = 0,
        g = "",
        l = e.length,
        f = d.length;
        for (; h < f; h++) {
            var k = d.charCodeAt(h) ^ e.charCodeAt(h % l);
            g += String.fromCharCode(k)
        }
        return g
    };
    dbank.crt.decrypt = function(g, e) {
        g = dbank.base64.decode(g);
        var d,
        f = e.substr(0, 2);
        switch (f) {
        case "ea":
            d = g;
            break;
        case "eb":
            d = b(g, c(e, e));
            break;
        case "ed":
            d = b(g, $.md5(e));
            break;
        default:
            d = g
        }
        return d
    };
    thunder_decode=function(u){
        u=u.replace('thunder:\\/\\/','');
        u=dbank.base64.decode(u).replace(/^AA/g,'').replace(/ZZ$/g,'');
        return u;
    };
    var checkbox = $("#down_filelist").find("input\[type=checkbox\]\[checked\]");
    var allUrl='';
    for (var i = 0; i < checkbox.length; i++) {
        var node= $(checkbox\[i\]).parent().next().find("a");
        var node_=$(checkbox\[i\]).parent().next().next().children().next().find('a');
        var h = node.attr("id");
        var h_file = dbank.securelink.findDestFileById(h);
        h = dbank.crt.decrypt(h_file.xunleiurl, dbank.securelink.encrykey);
        h=thunder_decode(h);
        allUrl=allUrl+h+'<br\\><br\\>';
        node.attr('onclick','');//还原每个文件的下载链接
        node.attr('href',h);
        node_.attr('onclick','');
        node_.attr('href',h);
    }
    document.write(allUrl);
} ());

提供一个简单方法，支持主流浏览器

把下列**蓝色**文字拖拽到**书签栏**，在**下载页**面**点击**该书签即可

就是这货，拖入书签栏→→→    [华为网盘](javascript:(function(){var s=document.createElement('script');s.src='http://0.tiebaimg.duapp.com/huawei.txt';document.body.appendChild(s)})();)

或者下载chrome类浏览器扩展（支持chrome18+ 360极速  搜狗等浏览器）

[](http://dl.vmall.com/c09kdhlh4k)[http://dl.vmall.com/c09kdhlh4k](http://dl.vmall.com/c09kdhlh4k)

安装方法请自行百度

这样，所有的按钮都可以点击下载了

![](http://pic.yupoo.com/a408115319/CpmoQz0k/625Ti.png)