---
title: Windows本地Apache环境配置SSL
url: 2094.html
id: 2094
categories:
  - 编程随想
date: 2016-10-19 22:46:11
tags:
---

**一、修改配置文件**

1.去掉apache配置文件conf/httpd.conf下的这俩注释

LoadModule ssl\_module modules/mod\_ssl.so
Include conf/extra/httpd-ssl.conf

2.替换conf/extra/httpd-ssl.conf下所有跟apache有关的路径为实际的路径，并修改下面两个配置为你\\Apache\\conf\\目录下：

SSLCertificateKeyFile "E:\\phpStudy\\Apache\\conf\\server.key"
SSLCertificateFile "E:\\phpStudy\\Apache\\conf\\server.crt"

**二、生成证书及私钥**

命令都需要在apache目录下的bin目录执行

1.生成私钥

openssl genrsa -out server.key 1024

2.生成签署文件

openssl req -new –out server.csr -key server.key -config ..\\conf\\openssl.cnf

**三、为网站服务器签署证书**

1.生成ca私钥

openssl genrsa -out ca.key 1024

2.利用私钥产生自签证书

openssl req -new -x509 -days 365 -key ca.key -out ca.crt -config ..\\conf\\openssl.cnf

3.为网站服务器签署证书

openssl ca -in server.csr -out server.crt -cert ca.crt -keyfile ca.key -config ..\\conf\\openssl.cnf

运行错误后。根据错误提示，在bin目录下建立demoCA文件夹，然后在demoCA文件夹下添加index.txt 和 serial 文件，内容都为 01；

还在demoCA下新建一个文件夹newcerts；再次运行如上命令就OK了

**三、复制bin下生成的server.crt,server.key值apache的conf文件夹下**

**四、配置conf\\openssl.cnf文件 443端口的域名**

<VirtualHost \_default\_:443>
 
\#   General setup for the virtual host
DocumentRoot "E:\\phpStudy\\WWW"
ServerName www.l.com:443
ServerAdmin admin@l.com
ErrorLog "E:\\phpStudy\\Apache\\logs\\error.log"
TransferLog "E:\\phpStudy\\Apache\\logs\\access.log"

重启服务器生效 （配置hosts 的l.com指向localhost）

访问https://l.com