---
title: CSS画Android机器人
tags:
  - Android
  - CSS
  - 安卓机器人
url: 1237.html
id: 1237
categories:
  - 编程随想
date: 2012-03-06 14:40:57
---

初学CSS   

  

蛋疼画个安卓机器人试试手

  

别用IE9以下的版本去试····很搓  理论支持IE9+   大多数chrome  火狐版本

  

演示：[http://www.ccc5.cc/demo/android.html](http://www.ccc5.cc/demo/android.html)

  

  
代码见下页

Html代码

 `CSS画安卓机器人

[返回上一页](javascript:history.go(-1);)`

CSS代码

`body{ background-color:#b2e5e4 } .android{ margin:100px 500px; border:1px solid #ccc; -webkit-box-shadow:7px 7px 8px 4px #999;//分别对应水平值 竖直值 模糊值 外延值 颜色 -moz-box-shadow:7px 7px 8px 4px #999; box-shadow:7px 7px 8px 4px #999; -moz-border-radius:20px; -webkit-border-radius:20px; border-radius:20px; background:-webkit-linear-gradient(top left,#eee,#888); } .shangbu{ width:300px; margin-left:90px; transform: rotate(-5deg) translate(-5px,-6px); -webkit-transform: rotate(-5deg) translate(-5px,-6px); -moz-transform: rotate(-5deg) translate(-5px,-6px); -o-transform: rotate(-5deg) translate(-5px,-6px); -ms-transform: rotate(-5deg) translate(-5px,-6px); } .tianxian{ position:relative; bottom:-22px; margin-left:10px; width:300px; height:40px; } .zuo{ width:8px; height:40px; background-color:#97C024; float:left; margin-left:2px; transform: rotate(-35deg); -webkit-transform: rotate(-35deg); -moz-transform: rotate(-35deg); -o-transform: rotate(-35deg); -ms-transform: rotate(-35deg); border-top-left-radius: 4px; -webkit-border-top-left-radius: 4px; -moz-border-radius-topleft: 4px; border-top-right-radius: 4px; -webkit-border-top-right-radius: 4px; -moz-border-radius-topright: 4px; } .you{ width:8px; height:40px; background-color:#97C024; float:left; margin-left:130px; transform: rotate(35deg); -webkit-transform: rotate(35deg); -moz-transform: rotate(35deg); -o-transform: rotate(35deg); -ms-transform: rotate(35deg); border-top-left-radius: 4px; -webkit-border-top-left-radius: 4px; -moz-border-radius-topleft: 4px; border-top-right-radius: 4px; -webkit-border-top-right-radius: 4px; -moz-border-radius-topright: 4px; } .toubu{ clear:left; width:170px; height:70px; background-color:#97C024; border-top-left-radius: 84px 68px; -webkit-border-top-left-radius: 84px 68px; -moz-border-radius-topleft:84px 68px; border-top-right-radius: 84px 68px; -webkit-border-top-right-radius: 84px 68px; -moz-border-radius-topright: 84px 68px; } .zuoyan{ background:#fff; width:20px; height:20px; float:left; margin:26px 0 0 45px; _margin:26px 0 0 19px; border-radius: 10px; -webkit-border-radius: 10px; -moz-border-radius: 10px; } .youyan{ background:#fff; width:20px; height:20px; float:left; margin:26px 0 0 38px; _margin:26px 0 0 19px; border-radius: 10px; -webkit-border-radius: 10px; -moz-border-radius: 10px; } .zhongbu{ margin-left:30px; margin-top:10px; width:300px; height:154px; } .zuoshou{ width: 40px; height: 114px; background: #97C024; float: left; border-radius: 20px; -webkit-border-radius: 20px; -moz-border-radius: 20px; } .shenzi{ margin-left:20px; width:170px; height:154px; background:#97c024; float:left; border-bottom-left-radius: 20px; -moz-border-radius-bottomleft: 20px; -webkit-border-bottom-left-radius: 20px; border-bottom-right-radius: 20px; -moz-border-radius-bottomright: 20px; -webkit-border-bottom-right-radius: 20px; } .youshou{ width: 40px; height: 114px; background: #97C024; margin-right:10px; float: right; border-radius: 20px; -webkit-border-radius: 20px; -moz-border-radius: 20px; } .xiabu{ margin-left:30px; clear:left; width:300px; height:70px; } .zuojiao{ width:40px; height:64px; background:#97c024; float:left; margin-left:80px;_margin-left:40px; border-bottom-left-radius: 20px; -moz-border-radius-bottomleft: 20px; -webkit-border-bottom-left-radius: 20px; border-bottom-right-radius: 20px; -moz-border-radius-bottomright: 20px; -webkit-border-bottom-right-radius: 20px; } .youjiao{ width:40px; height:64px; background:#97c024; float:left; margin-left:50px; border-bottom-left-radius: 20px; -moz-border-radius-bottomleft: 20px; -webkit-border-bottom-left-radius: 20px; border-bottom-right-radius: 20px; -moz-border-radius-bottomright: 20px; -webkit-border-bottom-right-radius: 20px; }`