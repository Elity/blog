---
title: 破解启程优势教育网络课程快进限制
tags:
  - 启程优势
  - 快进
  - 快进破解
  - 破解
  - 选修课
url: 1871.html
id: 1871
categories:
  - 编程随想
date: 2013-09-12 09:35:00
---

     启程优势教育网络课程地址 [http://www.nengli360.com](http://www.nengli360.com)

    还是先说说破解这个的理由吧。以前破解尔雅， 就有人跟我提出质疑，说我给不想学习的人提供了 一个便利通道。我就笑了：一来，代码我没强制别人用， 仅仅是自己学习JS之外的瞎折腾，破解成功后放在这留个记录而已；二来，看网络课程这个东西，本来就是被学校强迫的，好好的假期，每个人都有自己的安排，或旅游、或打 暑假工。。。愣是要强迫别人选修，看不完的同学还得面临挂科。人家要是自愿看，挤出时间也得看，要是不乐意看，就算是真正上课也在睡觉，强迫又有何用？

    代码不托管了，自己复制直接在课程 播放页面执行

其它浏览器就不说了，chrome直接ctrl+shft+J  粘贴代码，然后回车即可

代码执行完毕没错误的话，刷新页面看看刷了多少课， 有时候剩余课程过多而请求过快导致后面 一些没有成功，此时刷新页面再次执行代码即可

代码如下：

var stuSelectCourseID = $("#stuSelectCourseID").val(),//取课程id
    list = $("#section_list div").filter(function(){//获取未看课程列表
        return ($(this).attr("flag")==1 && $(this).attr("lock")==1)
    });
$.getScript("http://0.tiebaimg.duapp.com/base64.min.js",function(){
    list.each(function(){//发送请求
        var time =new Date().getTime(),
        str = '{"flvTime":"0","sendParam":"' + stuSelectCourseID + '|' + this.id + '","courseFinish":"1","time":' + time + '}';
        $.get("http://hncu.nengli360.com/student/course/listen/video/flv/record?rand=" + $.base64.encode(str));
    })
});

附尔雅通识课 快进破解 传送门  [http://www.ccc5.cc/1579.html](http://www.ccc5.cc/1579.html)