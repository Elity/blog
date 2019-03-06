---
title: ubuntu下运行Puppeteer
tags:
  - puppeteer
  - screen
url: 2307.html
id: 2307
comments: false
categories:
  - 编程随想
date: 2018-07-16 01:17:51
---

通过npm安装完puppeteer后若运行报错，可能需要通过apt-get安装一些缺少的依赖
```bash
sudo apt install gconf-service libasound2 libatk1.0-0 libc6 libcairo2 libcups2 libdbus-1-3 libexpat1 libfontconfig1 libgcc1 libgconf-2-4 libgdk-pixbuf2.0-0 libglib2.0-0 libgtk-3-0 libnspr4 libpango-1.0-0 libpangocairo-1.0-0 libstdc++6 libx11-6 libx11-xcb1 libxcb1 libxcomposite1 libxcursor1 libxdamage1 libxext6 libxfixes3 libxi6 libxrandr2 libxrender1 libxss1 libxtst6 ca-certificates fonts-liberation libappindicator1 libnss3 lsb-release xdg-utils wget
```
vps后台运行puppeteer，除了用nohup外，还可以使用screen，常用命令：
```bash
screen -ls   # 例出所有已经创建的screen
screen -r  1234  # 连上id为1234的screen
screen -d 1234   # detach id为1234的screen
screen -S abc  #创建一个名叫abc的screen
```
在screen中通过ctrl+a  然后按d键退出screen