---
title: HTTPS握手时序图
tags:
  - https
  - https握手
url: 2347.html
id: 2347
comments: false
categories:
  - 编程随想
date: 2018-08-11 09:25:53
---

![https handshake](http://wx1.sinaimg.cn/large/6d1c4ea2gy1fu5ibpjuuaj20xc0pztd1.jpg) [大图](http://wx1.sinaimg.cn/large/6d1c4ea2gy1fu5ibpjuuaj20xc0pztd1.jpg) 感觉还有些不明白的地方，等明白了再补上。 源码:

    title HTTPS Handshake
    
    Client->Server: 1.协议版本\n2.加密方式列表\n3.支持的压缩算法\n4.random_C
    Server->Client: 1.确认协议\n2.确认加密方式\n3.确认压缩方式\n4.random_S\n5.数字证书
    note left of Client: 验证证书
    note left of Client: 产生Pre-master
    note left of Client: enc_key=Fuc(random_C, random_S, Pre-Master)
    note left of Client: enc_key加密之前的握手信息得到encrypted_handshake_message
    Client->Server: 1.用证书携带的公钥加密后的Pre-Master \n2.参数协商完成通知\n3.encrypted_handshake_message
    note right of Server: 用私钥解密得到Pre-Master
    note right of Server: enc_key=Fuc(random_C, random_S, Pre-Master)
    note right of Server: 用enc_key解密Client传来的encrypted_handshake_message并验证
    note right of Server: 用enc_key加密之前的握手信息得到新的encrypted_handshake_message
    Server->Client: 1.参数协商完成通知\n2.encrypted_handshake_message
    note left of Client:  用enc_key解密Server传来的encrypted_handshake_message并验证
    

在线绘制地址：https://www.websequencediagrams.com/