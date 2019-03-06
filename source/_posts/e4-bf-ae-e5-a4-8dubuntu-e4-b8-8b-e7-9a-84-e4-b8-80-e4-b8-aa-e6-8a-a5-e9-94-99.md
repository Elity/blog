---
title: 修复ubuntu下的一个报错
url: 2092.html
id: 2092
categories:
  - 编程随想
date: 2016-09-28 17:54:31
tags:
---

pyenv下安装新版python的时候出现的

perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
    LANGUAGE = (unset),
    LC_ALL = (unset),
    LANG = "en_US.UTF-8"
are supported and installed on your system.
perl: warning: Falling back to the standard locale ("C").

修复方法：

sudo locale-gen en\_US en\_US.UTF-8
sudo dpkg-reconfigure locales