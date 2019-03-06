---
title: CSDN下载地址免费获取（2013.05.06失效）
tags:
  - CSDN
  - 下载
url: 1740.html
id: 1740
categories:
  - 编程随想
date: 2013-04-26 11:58:41
---

这回漏洞测底堵了~~~~~~~~~~

====================================

这是多年前的老BUG了  最近看到吾爱破解有人发布相关软件，特意去查看下了这个BUG 居然还存在

以这个资源为例：

http://download.csdn.net/detail/xht530/4732019

注意最后的 **4732019**

这是资源的唯一标识符 GET如下链接

> http://download.csdn.net/index.php/rest/source/getsourceinfo/**4732019**

得到如下JSON数据：

{
    "fileaddr": "\\/20121104\\/bc4fe82318630b4a89408d0d2bf17de3.pdf",
    "tag": \["qt", "QT", "QT\\u9ad8\\u7ea7\\u7f16\\u7a0b"\],
    "isrepeat": "1",
    "operate_time": "1352041376",
    "isrecommended": "0",
    "username": "xht530",
    "comment_num": "54",
    "title": "文件标题",
    "filetype": "pdf",
    "status": "2",
    "description": "文件描述",
    "pubdate": "1352033788",
    "md5": "705f4526137b084bb5a14dc7d9e20a9c",
    "download_num": "253",
    "fileaddr_prefix": "\\/down10",
    "originfile": "文件名",
    "score_num": "72",
    "categoryid": "16014",
    "operator": "vipliangzi",
    "fav_num": "6",
    "sourcesize": "43305627",
    "sourcescore": "3",
    "id": "4732019",
    "score_sum": "313",
    "otherlink": "",
    "updatetime": "2013-04-26 10:00:37",
    "sourcetype": "1",
    "link": "\\/detail\\/xht530\\/4732019",
    "url": "http:\\/\\/dldx.csdn.net\\/fd.php?i=438647320196808&s=14700ea9619f2d080374a51cf0c8af14"
}

url部分就是下载地址了

提取测试：[http://www.ccc5.cc/csdn](http://www.ccc5.cc/csdn)